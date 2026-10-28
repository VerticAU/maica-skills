# Maica UI · Web-Facing Context

For standalone web apps where Maica controls the stack. The Time Tracker statement page is the canonical example.

## When this applies

Building UI for any of these counts as web-facing:

- A Next.js, React, Vue, or other framework app at a Maica-controlled domain
- A page customers reach via a tokenized URL (statements, reports, shared views)
- A marketing or product surface outside Salesforce
- A portal or extranet for customers

If the UI lives inside Salesforce, read `salesforce-hosted.md` instead.

## Aesthetic direction

**Document feel, not dashboard.** The Time Statement reads as a deliverable a customer might forward, print, or attach to an invoice. Constrained max-widths (around 680px for a statement-style page), calm surfaces, clear sectioning, room to breathe. Avoid Salesforce-style information density and avoid dashboard chrome (sidebars, top bars, dense controls).

## Typography

Use **Poppins** from Maica's brand kit. In Next.js, prefer `next/font` for performance and CSP cleanliness:

```js
import { Poppins } from 'next/font/google';
const poppins = Poppins({ subsets: ['latin'], weight: ['400', '500', '600'] });
```

Otherwise, load via fontsource on jsdelivr:

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@fontsource/poppins@5.0.8/400.css">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@fontsource/poppins@5.0.8/500.css">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@fontsource/poppins@5.0.8/600.css">
```

**Three weights, no more**: 400 (body), 500 (labels, buttons, table totals), 600 (headings and emphasised numbers). Skip 300 and 700; the system is calmer with fewer weights.

**Type scale (statement-style page)**:

- Page title: 24px / 600 / letter-spacing -0.02em
- Section heading: 13px / 600 / sentence case
- Field label: 11-12px / 500 / sentence case / muted colour
- Body text: 13-14px / 400
- KPI value: 26px / 600 / tabular-nums
- Brand mark wordmark: 15px / 600

## Layout principles

**Constrained width**: Statement-style pages cap around 680px. Wider pages (dashboards, list views) can go to 960-1080px. Avoid full-bleed unless there's a strong reason.

**Generous gutters**: 22-26px horizontal padding on the document container. 20-28px vertical spacing between major sections.

**Hairline borders**: 0.5px or 1px in `#EEEEEE` (Light Gray). Never heavier.

**Rounded corners**: 8px (`--radius-md`) for cards and blocks, 12px (`--radius-lg`) for the outer document container. 999px for pills. Never sharp corners.

**Soft shadows**: Only on elevated elements like modals or floating cards. `box-shadow: 0 12px 36px rgba(0, 0, 0, 0.10), 0 2px 6px rgba(0, 0, 0, 0.05)`. Avoid heavy shadows.

## Component patterns

These are the patterns established in the Time Statement canonical example. Read `assets/time-statement.html` for the complete reference.

### Document container

A bordered card that wraps the entire content, with subtle border and rounded corners. Sets up the document feel.

```css
.ts-doc {
  background: #FFFFFF;
  border: 1px solid #EEEEEE;
  border-radius: 12px;
  overflow: hidden;
  font-family: 'Poppins', sans-serif;
}
```

### Header strip

Thin horizontal bar at the top of the document with the Maica mark and a contextual reference pill on the right. The reference pill is for the document identifier (e.g. `Statement · CN-0001`), not for navigation.

### KPI cards

Three (or sometimes four) cards in a row showing summary metrics. Off-white background, teal icon and label, deep-tone value. Each card has an icon, a label in 12px, and a 26px value.

```css
.ts-kpi {
  background: #F7F9F9;
  border: 1px solid #EEEEEE;
  border-radius: 8px;
  padding: 14px 16px;
}
.ts-kpi-icon { color: #196061; font-size: 16px; }
.ts-kpi-label { color: #196061; font-size: 12px; font-weight: 500; }
.ts-kpi-value { color: #152C36; font-size: 26px; font-weight: 600; }
```

### Section headings

Small bold label with a trailing horizontal rule that fills the remaining width. Reads as "this is a new section" without shouting.

```css
.ts-section-heading {
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 13px;
  font-weight: 600;
}
.ts-section-heading::after {
  content: '';
  flex: 1;
  height: 1px;
  background: #EEEEEE;
}
```

### Proportional pills (resource utilisation)

For showing each row's share of a total. The pill's width is set inline (`style="width: 83.3%"`), the row uses flexbox, and the hours value is pushed to the right with `margin-left: auto`. Gap between rows (6px) so they read as separate pills.

```css
.ts-resource-rows { display: flex; flex-direction: column; gap: 6px; }
.ts-resource-row { display: flex; align-items: center; }
.ts-bar {
  background: rgba(25, 96, 97, 0.12);
  border-radius: 8px;
  padding: 11px 14px;
  min-width: fit-content;
  flex-shrink: 0;
}
.ts-resource-hours { margin-left: auto; font-weight: 600; }
```

`min-width: fit-content` is important: it lets the pill grow when the proportion is too small to fit the name.

### Work item blocks

Bordered card per work item with an off-white header (ID pill + name + total) and rows underneath. Used for both aggregated views ("Per work item") and detail views ("Time entries"). The two sections rhyme visually because they share the block structure.

### Status pills

Filled vs outlined treatment to distinguish primary vs secondary status:

- **Billable** (primary): filled teal tint background, teal text
- **Non-billable** (secondary): faint mid-teal background, mid-teal text, 1px mid-teal border

### Grand total banner

The closing visual element of a financial section. Solid Maica Teal background, white text, font-size slightly larger than other totals.

### Info callouts

For derived or informational content (e.g. "X rosters will be created"). Teal-tinted background, info icon, optional action link aligned right.

## Dark mode

The canonical example includes a `@media (prefers-color-scheme: dark)` block. The pattern: define brand-relative CSS custom properties on the component scope, then override them inside the dark-mode media query. Text colours come from CSS variables (`--color-text-primary`) that auto-adapt; brand colours are explicitly overridden.

```css
.ts-doc { --m-teal: #196061; }
@media (prefers-color-scheme: dark) {
  .ts-doc { --m-teal: #7DA0A0; }
}
```

For brand teal in dark mode, shift to the lighter Mid Teal `#7DA0A0` so it remains readable on dark surfaces. Adjust tint opacities upward (0.14 instead of 0.12, etc.) so they remain visible.

## Common mistakes to avoid

- Don't introduce colours outside the five-token palette. If you need an error state, defer to platform red.
- Don't use more than three Poppins weights.
- Don't use em dashes anywhere; "to" or "and" instead.
- Don't use Title Case or ALL CAPS for section headings.
- Don't reach for dashboard tropes (sidebars, top navs, dense filter controls) on a statement-style page.
- Don't make borders darker than `#EEEEEE`. Light Gray is the only divider colour.
- Don't add icons everywhere; they're for KPI cards, status indicators, and section markers. Body text doesn't need icons.

## When the design gets stuck

If a deliverable feels "vacant" or "too sparse", the answers are usually one of:

1. Tighten spacing (reduce vertical gaps between elements)
2. Add labels for any unlabelled fields
3. Group related elements into a single bordered block
4. Add a section heading with trailing rule
5. Apply a soft teal tint to give a passive element a presence

If a deliverable feels "too busy", the answers are usually:

1. Remove decorative icons
2. Reduce font weight (drop from 600 to 500 where possible)
3. Drop borders on nested elements (keep only on the outer container)
4. Reduce the count of distinct background colours
