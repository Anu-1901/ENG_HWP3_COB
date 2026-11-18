---
name: bosch-frontend-design
description: "A generic system for building, redesigning, or auditing React frontend applications using Bosch-inspired layout, typography, and visual rules."
version: "2.0.0"
category: "Design System Guidelines"
tools: ["read", "edit", "search"]
---

# Bosch-Inspired Frontend Design Runbook (Generic Module)

Use this system to construct clean, consistent, and professional React interfaces inspired by industrial design aesthetics, ensuring a unified user experience (UX) across your applications.

---

## 🔍 Discovery Phase: Repo-Aware Inspection

Before generating or modifying any user interface elements, you must run a discovery pass to detect existing branding assets, styles, and layout shells:

1. **Brand Asset Search**: Search the workspace for brand assets such as the Bosch logo, the Supergraphic, and the Bosch Sans font files (`.woff`, `.woff2`, `.ttf`).
2. **Typography & Styles Check**: Locate the primary stylesheet (e.g., `index.css`, `global.css`, or design tokens). Scan for custom variables representing brand colors.
3. **App Shell Analysis**: Find the primary layout or shell component (e.g., `Layout.tsx`, `AppShell.tsx`, or `Navigation.tsx`) to identify where the global header, sidebar, and footer are rendered.
4. **Local Components Mapping**: Identify existing shared UI elements (such as custom Icon wrappers, Alerts, Cards, and Inputs) to prevent code duplication.

> 💡 **Harness Rule**: If any required assets (logos, custom fonts, or theme variables) are missing from the current repository, do not invent them. Flag their absence immediately to the developer and provide instructions on where they should be placed.

---

## 📐 The Layout & Typography System

Always structure your app screens according to these visual hierarchies:

### 1. Structure of the App Shell
- **Branded Header**: A clean, square-edged top bar containing the brand logo on the left, global navigation in the center, and user profile/status indicators on the right.
- **Visual Boundaries**: Use crisp, restrained, neutral-colored borders (e.g., `1px solid var(--grey-border)`) instead of rounded cards, drop-shadows, or heavy gradients.
- **Page Header Grid**: Every page must begin with a clear visual hierarchy:
  1. An `<h1>` title representing the user's current destination.
  2. A concise 1-2 sentence subtitle describing the page's functional scope.
  3. Action buttons (if any) grouped consistently in the top-right corner.
- **Footer**: A minimal, low-contrast footer containing copyright information and helpful metadata.

### 2. Styling Tokens & Custom Properties
Prefer theme variables (CSS variables) to allow immediate integration with existing project colors. Use this palette mapping:

| Intent | Key Variable Preference | UI Element Mapping |
| :--- | :--- | :--- |
| **Accent / Focus** | `--blue` (or primary theme color) | Active state, main buttons, focus rings, selected menu items |
| **Restrained Brand** | `--red` | Highly-restrained brand accents (e.g., decorative top line) |
| **Success** | `--green` | Success alerts, completed indicators, positive metrics |
| **Warning** | `--yellow` | Pending states, attention-required system flags |
| **Danger / Error** | `--red` | Destructive actions, system errors, failed states |
| **Surfaces & Borders**| `--grey-*` | Layout backgrounds, cards, and subtle separators |

---

## ⚙️ Core Implementation Guidelines

### 1. Typography & Hierarchy
- Set the global font-family to **Bosch Sans** (or your fallback system sans-serif like Arial/Helvetica if missing).
- Use a structured scale for copy layout:
  - `h1`: Bold, minimal spacing, no decorative decorations.
  - `h2` / `h3`: Clear section demarcations.
  - `body`: High-contrast, scannable reading sizes with comfortable line height.

### 2. Form & Data Workflow States
- **Square-Edged Controls**: Inputs, Select fields, Buttons, and Cards must use sharp corners (`border-radius: 0px`).
- **Interaction Feedback**: Avoid blank screens during asynchronous work. Always implement:
  - **Loading state**: A clean skeleton UI or generic loading indicator.
  - **Empty state**: A structured, informative empty card containing a primary call-to-action (CTA).
  - **Error state**: Clear text inline explaining what failed and how to retry.

### 3. Accessibility & Responsiveness
- Icon-only controls must always include an accessible name (e.g., `aria-label`).
- Decorative images or background icons must be hidden from screen readers (`aria-hidden="true"`).
- Grids, columns, and navigation headers must collapse seamlessly into a mobile-friendly view on screens smaller than `768px` wide without losing metadata or clipping data.

---

## 🧪 Pipeline Checklist

Before delivering code changes, confirm you have:
1. Validated that all brand assets load successfully without broken paths.
2. Verified that the custom styles align with the discovered theme variables.
3. Ensured that your page layout fits seamlessly into the detected application frame (avoiding nested scrollbars or clipping).
