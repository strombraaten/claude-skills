---
name: obsidian-plugin-dev
description: >
  Guides Claude through building Obsidian plugins that pass the official automated review system.
  Trigger this skill any time the user mentions "Obsidian plugin" — even a passing mention, a
  vague idea, or a question. If unsure whether the user wants to build one, ask. Do not wait
  for the user to mention review, ESLint, submission, or code — trigger on the phrase alone.
---

# Obsidian Plugin Development

The goal here is to write plugin code that passes Obsidian's automated review on the first try. The review runs `eslint-plugin-obsidianmd` plus manifest validation on every release. Failures block the plugin from appearing in the community directory within 24 hours. Most violations are mechanical and predictable — avoid them from the start rather than patching them at submission time.

## ESLint Setup

Always set up the linter at the start of a new plugin. Run it before every submission.

```sh
npm install eslint-plugin-obsidianmd --save-dev
```

**`eslint.config.mjs`** (ESLint v9+):
```js
import tsparser from "@typescript-eslint/parser";
import { defineConfig } from "eslint/config";
import obsidianmd from "eslint-plugin-obsidianmd";

export default defineConfig([
  ...obsidianmd.configs.recommended,
  {
    files: ["**/*.ts"],
    languageOptions: {
      parser: tsparser,
      parserOptions: { project: "./tsconfig.json" },
    },
  },
]);
```

Run with `npx eslint .` before any submission or release.

---

## Commands

Obsidian **automatically** prepends the plugin ID to every command ID. Never do it manually.

```ts
// Wrong — results in "my-plugin:my-plugin:do-thing"
this.addCommand({ id: "my-plugin:do-thing", name: "Do thing", ... });

// Correct — results in "my-plugin:do-thing"
this.addCommand({ id: "do-thing", name: "Do thing", ... });
```

Other command rules:
- Do not include the word "command" in command IDs or names
- Do not include the plugin name in the command name (Obsidian adds it in the hotkey UI)
- Never set default `hotkeys` — causes conflicts between plugins and OS shortcuts
- Use the right callback type:
  - `callback` — always available
  - `checkCallback` — conditionally available (return `false` to hide)
  - `editorCallback` — only in markdown editors
- Command names use **sentence case**: "Open image browser", not "Open Image Browser"

---

## `this.app` vs Global `app`

Always use `this.app`, never the global `app`. The global exists only for debugging in the developer console and may be removed in future versions.

```ts
// Wrong
const file = app.vault.getActiveFile();

// Correct
const file = this.app.vault.getActiveFile();
```

---

## Manifest (`manifest.json`)

Key rules the automated validator checks:

| Field | Requirement |
|---|---|
| `id` | Must not contain the word `obsidian` |
| `description` | Max 250 chars; ends with `.`; no emoji; sentence case; not starting with "This is a plugin" |
| `minAppVersion` | Must reflect actual minimum supported version |
| `isDesktopOnly` | Must be `true` if you use any Node.js or Electron APIs |
| `fundingUrl` | Only allowed if you actually accept donations; link only to donation/sponsorship services |
| `version` | Semantic versioning `x.y.z` — no `v` prefix |

Description format:
```json
{
  "description": "Translates selected text using DeepL API and replaces it in the editor."
}
```
- Starts with an action verb
- Sentence case (only the first word and proper nouns are capitalised)
- Ends with a period

---

## Mobile Compatibility

If your plugin uses Node.js or Electron APIs (`fs`, `path`, `crypto`, `os`, `child_process`, etc.):

Option 1 — Mark as desktop-only in `manifest.json`:
```json
{ "isDesktopOnly": true }
```

Option 2 — Guard with `Platform`:
```ts
import { Platform } from "obsidian";

if (Platform.isDesktop) {
  const fs = require("fs");
  // ...
}
```

Also avoid regex lookbehind expressions — unsupported on some iOS versions.

---

## DOM Creation

Use Obsidian's DOM helpers instead of native browser APIs. This is both an ESLint error and a readability convention.

```ts
// Wrong
const div = document.createElement("div");
div.className = "my-class";
container.appendChild(div);

// Correct
const div = container.createDiv({ cls: "my-class" });
// or: createEl, createSpan, createSvg, createFragment
```

Never set inline styles on DOM elements. Use CSS classes with Obsidian's CSS variables:
```ts
// Wrong
el.style.color = "red";

// Correct — add a CSS class in styles.css instead
el.addClass("my-error-text");
```

---

## Security: No `innerHTML` with User Input

Never pass user-controlled strings to `innerHTML`, `outerHTML`, or `insertAdjacentHTML`. This is an XSS vulnerability.

```ts
// Wrong — XSS risk
el.innerHTML = userInput;

// Correct — use DOM methods or sanitised helpers
el.setText(userInput);
el.createSpan({ text: userInput });
```

Use `el.empty()` to clear element contents instead of `el.innerHTML = ""`.

---

## Memory and Resource Management

Register everything through Obsidian's lifecycle methods so it gets cleaned up automatically on plugin unload:

```ts
// Event listeners
this.registerEvent(
  this.app.vault.on("modify", (file) => { ... })
);

// DOM event listeners
this.registerDomEvent(document, "click", (evt) => { ... });

// Intervals
this.registerInterval(
  window.setInterval(() => { ... }, 5000)
);
```

Do not:
- Detach leaves in `onunload()` — Obsidian will reinitialise them during updates
- Store custom view references directly on the plugin class (memory leak)
- Pass the plugin instance to `MarkdownRenderer.render` as a component

```ts
// Wrong — stores view reference on plugin
this.view = new MyView(leaf);

// Correct — access views via workspace when needed
const leaves = this.app.workspace.getLeavesOfType(MY_VIEW_TYPE);
```

---

## Workspace API

```ts
// Wrong
const leaf = this.app.workspace.activeLeaf;

// Correct
const view = this.app.workspace.getActiveViewOfType(MarkdownView);
const editor = this.app.workspace.activeEditor?.editor;
```

Register views like this — don't store the instance:
```ts
this.registerView(MY_VIEW_TYPE, (leaf) => new MyView(leaf));
```

---

## Vault API

Prefer the Vault API over the raw Adapter API. The Vault API handles caching and events correctly.

```ts
// Wrong — iterate to find a file
const file = this.app.vault.getFiles().find(f => f.path === "notes/foo.md");

// Correct
const file = this.app.vault.getFileByPath("notes/foo.md");
```

Always call `normalizePath()` on any path that comes from user input or string concatenation:
```ts
import { normalizePath } from "obsidian";

const path = normalizePath(`${folderInput}/${filename}`);
const file = this.app.vault.getFileByPath(path);
```

For editing the active note, use the `Editor` interface (preserves cursor position):
```ts
// Wrong for active note — loses cursor position, triggers full re-render
await this.app.vault.modify(file, newContent);

// Correct for active note
editor.setValue(newContent);

// Correct for background file edits (atomic, race-condition safe)
await this.app.vault.process(file, (content) => newContent);
```

Parse frontmatter via the API, never manually:
```ts
await this.app.fileManager.processFrontMatter(file, (frontmatter) => {
  frontmatter["my-key"] = "value";
});
```

---

## Settings Tab UI

```ts
// Wrong — HTML heading element
containerEl.createEl("h2", { text: "Advanced settings" });

// Correct — use setHeading()
new Setting(containerEl).setName("Advanced").setHeading();
```

Settings heading rules:
- Don't add a top-level heading if there's only one section
- Don't use "Settings", "General", or the plugin name as a heading

All text in the settings tab must be **sentence case**:
```ts
// Wrong
new Setting(containerEl).setName("Template Folder Location").setDesc("Choose Folder");

// Correct
new Setting(containerEl).setName("Template folder location").setDesc("Choose a folder.");
```

Exceptions: proper nouns and acronyms keep their casing (Obsidian, Markdown, PDF, API, GitHub).

---

## Timers and Globals

Use `window.` prefixed timer functions for popout window compatibility:
```ts
// Wrong
setTimeout(() => { ... }, 1000);

// Correct
window.setTimeout(() => { ... }, 1000);
```

Use `window` or `activeWindow`, not `global` or `globalThis`.

---

## Submission Checklist

Before creating a GitHub release:

- [ ] `npx eslint .` passes with zero errors
- [ ] All placeholder names replaced (`MyPlugin`, `MyPluginSettings`, `SampleSettingTab`)
- [ ] Template/sample code removed
- [ ] `manifest.json` description: ≤250 chars, ends with `.`, sentence case, no emoji
- [ ] `manifest.json` `id` does not contain "obsidian"
- [ ] `manifest.json` `isDesktopOnly: true` if Node.js/Electron APIs are used
- [ ] `README.md` and `LICENSE` present in repo root
- [ ] Release tag matches `manifest.json` version exactly (e.g., `1.0.0`, not `v1.0.0`)
- [ ] Release assets include `main.js` and `manifest.json` (and `styles.css` if applicable)
- [ ] If network use, external services are documented in README
- [ ] If server-side telemetry, privacy policy linked in README

Submission is via [community.obsidian.md](https://community.obsidian.md) — connect GitHub, enter repo URL, submit.
