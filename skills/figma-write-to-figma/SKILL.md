---
name: figma-write-to-figma
description: Write designs into Figma files using the official Figma MCP server's generate_figma_design tool. Use this skill whenever the user wants to create designs in Figma, push UI into Figma, generate Figma frames from descriptions, capture web pages to Figma, or convert HTML/CSS to Figma design layers. Also use when the user says "write to Figma", "create in Figma", "push to Figma", "design in Figma", or provides a Figma URL and wants new designs created there. IMPORTANT — the Figma MCP is NOT read-only. It can write editable design layers to Figma files.
---

# Write Designs to Figma

## Overview

The official Figma MCP server (`https://mcp.figma.com/mcp`) includes the `generate_figma_design` tool which **writes editable design layers into Figma files**. This is NOT a screenshot — it creates real, editable frames, text layers, shapes, and components.

Many Claude users incorrectly assume the Figma MCP is read-only. **It is not.** The `generate_figma_design` tool is the write mechanism.

## Prerequisites

1. **Figma MCP plugin installed**: `claude plugin install figma@claude-plugins-official`
2. **Figma MCP authenticated**: The remote server at `https://mcp.figma.com/mcp` must be authenticated via OAuth
3. **Figma seat**: User needs a Full or Dev seat on a paid Figma plan (Professional, Organization, or Enterprise)
4. **Supported client**: Currently Claude Code and Codex by OpenAI

## Authentication

If `generate_figma_design` is not in the available tool list:

1. Run `/mcp` in Claude Code
2. Select the `figma` server
3. Choose "Authenticate"
4. Complete the OAuth flow in the browser
5. **Restart the Claude Code session** — new MCP tools only load at session start

Check authentication status:
- `whoami` tool returns the user's email, plans, and seat type
- Auth cache at `~/.claude/mcp-needs-auth-cache.json` shows servers needing auth

## Workflow: Creating Designs in Figma

### Step 1: Build the UI as HTML/CSS

Create the design as local web pages. This is the source material that gets converted to Figma layers.

- Use semantic HTML with clean CSS
- Load web fonts (Google Fonts for free fonts, or local fonts for commercial ones like Tiempos)
- Build at the target resolution (e.g., 1440×900 for desktop screens)
- Create separate HTML pages for each frame/screen you want in Figma
- **CRITICAL**: Include the Figma capture script in every HTML page's `<head>`:

```html
<script src="https://mcp.figma.com/mcp/html-to-design/capture.js" async></script>
```

This script enables the capture toolbar that lets `generate_figma_design` convert DOM elements to Figma layers. Without it, the tool cannot capture the page.

**Example structure:**
```
my-design/
├── styles.css
├── colors.html        → Color palette page
├── typography.html    → Type samples
├── components.html    → Buttons, forms, cards
├── dashboard.html     → Full screen (1440×900)
└── detail.html        → Full screen (1440×900)
```

**Example HTML template:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>My Design</title>
  <link rel="stylesheet" href="styles.css">
  <script src="https://mcp.figma.com/mcp/html-to-design/capture.js" async></script>
</head>
<body>
  <!-- Your design here -->
</body>
</html>
```

### Step 2: Serve Locally

Start a local development server:

```bash
# Simple Python server
cd my-design && python3 -m http.server 8765

# Or use npx serve
npx serve my-design -p 8765

# Or use Vite for hot-reload
npx vite my-design --port 8765
```

Verify it works: `curl http://localhost:8765/`

### Step 3: Use `generate_figma_design`

Call the tool to capture the live UI into Figma:

**To a new Figma file:**
> "Capture the UI at http://localhost:8765/dashboard.html into a new Figma file"

**To an existing Figma file:**
> "Capture http://localhost:8765/dashboard.html to https://www.figma.com/design/[fileKey]/[fileName]"

**Multiple pages:**
> "Capture all pages from http://localhost:8765 into my Figma file at [URL]. Start with colors.html, then typography.html, then components.html, then all screens."

### Step 4: Capture Toolbar

When `generate_figma_design` runs:
1. A browser window opens showing the localhost page
2. A **capture toolbar** appears for selecting specific elements or pages
3. The user selects what to capture (full page, specific elements, different states)
4. The user signals when done
5. The tool returns a link to the Figma file with the new design layers

### Step 5: Verify and Iterate

After capture, use the read tools to verify:
- `get_screenshot` — Visual check of the created frames
- `get_metadata` — Verify layer structure and naming
- `get_design_context` — Check the code representation matches

## Tool Reference

### `generate_figma_design` (WRITE)
- **Direction**: Write to Figma
- **Server**: Remote only (`https://mcp.figma.com/mcp`)
- **What it does**: Captures live UI from web apps/localhost and converts to editable Figma design layers
- **Supported file types**: Figma Design files
- **Clients**: Claude Code, Codex by OpenAI

### Other Write Tools
| Tool | What it writes |
|------|---------------|
| `generate_diagram` | Creates FigJam diagrams from Mermaid syntax |
| `add_code_connect_map` | Maps Figma nodes to code components |
| `send_code_connect_mappings` | Saves bulk Code Connect mappings |

### Read Tools (for verification)
| Tool | What it reads |
|------|--------------|
| `get_design_context` | Design data, code representation |
| `get_screenshot` | Visual screenshot of a node |
| `get_metadata` | XML structure with IDs, names, sizes |
| `get_variable_defs` | Variables and styles |
| `whoami` | User identity and plan info |

## Common Issues

### "generate_figma_design is not available"
The remote Figma MCP needs authentication. Run `/mcp` → select `figma` → Authenticate. Then **restart the Claude Code session**.

### "User does not have permission"
The user needs a Full or Dev seat on a paid Figma plan. View-only or Collab seats cannot use this tool. Check with `whoami`.

### "Font not rendering correctly"
Commercial fonts (Tiempos, Graphik, etc.) must be installed locally on the machine running the server. Google Fonts are loaded via `@import` or `<link>` tags.

### Design looks different in Figma
The capture converts CSS to Figma's layout system. For best results:
- Use flexbox/grid (maps to Figma Auto Layout)
- Avoid complex CSS transforms
- Use explicit sizing rather than intrinsic sizing
- Test at the exact resolution you want in Figma

## Examples

**Example 1: Design system to Figma**
```
User: "Create a design system for our app and put it in Figma"

1. Build HTML pages for colors, typography, components
2. Serve on localhost
3. generate_figma_design → capture each page
4. Result: Editable Figma file with design system
```

**Example 2: Redesign existing app**
```
User: "Redesign our dashboard and create it in Figma"

1. Screenshot existing app for reference
2. Build new dashboard design as HTML/CSS
3. Serve on localhost
4. generate_figma_design → capture to Figma
5. Result: New design in Figma for team review
```

**Example 3: Prototype from description**
```
User: "Design a settings page with sidebar nav and put it in my Figma file"

1. Build settings page as HTML/CSS
2. Serve on localhost
3. generate_figma_design → capture to user's Figma URL
4. Result: Settings page as editable Figma layers
```
