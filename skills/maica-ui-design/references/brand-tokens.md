# Maica UI · Brand Tokens

Shared reference for both web-facing and Salesforce-hosted contexts. Source of truth is `brand.maica.com.au`.

## Colour palette

Five colours. The entire system runs on this.

### Maica Teal · `#196061`

The primary brand colour. Used for:

- Primary action buttons (Create, Save, Submit, etc.)
- Brand emphasis (totals, headings on coloured surfaces)
- The grand-total banner background
- Brand mark (`M` square in headers/footers)
- Custom pill backgrounds (with opacity reduction)
- Status indicators that need brand presence

Avoid using as body text colour; it's too saturated. For text on dark teal, use white.

### Mid Teal · `#7DA0A0`

The secondary brand colour. Used for:

- Non-billable pill and other secondary-state pills (outlined treatment)
- Muted status indicators (Draft status dot, etc.)
- Decorative accents where Maica Teal would be too strong

In dark mode it shifts roles: Mid Teal becomes the primary brand colour because it reads better on dark surfaces than Maica Teal does.

### Dark · `#152C36`

High-contrast text and high-emphasis values. Used for:

- KPI numeric values on Off-white cards
- Bold totals on tinted teal backgrounds
- Optional: deep navigation chrome

Not for body text (use the platform default `--color-text-primary` or `#080707` in web-facing contexts).

### Off-white · `#F7F9F9`

Soft surface for nested or grouped content. Used for:

- KPI card backgrounds
- Work item header strips (the cap of each work item block)
- Document header strip (the area containing the brand mark)
- Modal footer background (Salesforce-hosted contexts)

Subtle enough to read as a surface, not as a colour.

### Light Gray · `#EEEEEE`

The only divider colour. Used for:

- Hairline borders between rows
- Card and block borders
- Section dividers
- Modal header bottom border

Never darker than this for a divider. Never coloured.

## Derived tints

For brand-tinted backgrounds (custom pills, callouts, resource bars), use Maica Teal at reduced opacity:

| Use case | Value |
|----------|-------|
| Soft tint (pill background, resource bar) | `rgba(25, 96, 97, 0.12)` |
| Medium tint (subtotal row, callout background) | `rgba(25, 96, 97, 0.20)` |
| Strong tint (proportional bar fill on top of soft tint) | `rgba(25, 96, 97, 0.30)` |

In dark mode, shift to Mid Teal at slightly higher opacities:

| Use case | Value |
|----------|-------|
| Soft tint | `rgba(125, 160, 160, 0.14)` |
| Medium tint | `rgba(125, 160, 160, 0.22)` |
| Strong tint | `rgba(125, 160, 160, 0.34)` |

## Typography

| Context | Typeface |
|---------|----------|
| Web-facing | Poppins (Maica brand kit) |
| Salesforce-hosted | Salesforce Sans (platform default; do not load custom fonts) |

In both contexts, use only three weights: 400 (body), 500 (labels and buttons), 600 (headings, emphasised values).

Type scale (rough; adjust to context):

| Use | Size | Weight |
|-----|------|--------|
| Page title | 22-24px | 600 |
| Section heading | 13px | 600 |
| Field label | 11-12px | 500 |
| Body | 13-14px | 400 |
| Small caption | 11-12px | 400 |
| KPI value | 24-26px | 600 |
| Brand wordmark | 15px | 600 |
| Pill content | 12-13px | 500 |

Letter-spacing: slightly tighten large display text (`letter-spacing: -0.02em`) and slightly open small caption text (`letter-spacing: 0.01-0.04em`) where it improves legibility.

## Spacing

For web-facing, use a multiple-of-2px scale, leaning on the 4 / 8 / 12 / 16 / 24 progression for vertical rhythm.

For Salesforce-hosted, use SLDS spacing utilities (`slds-m-around_small`, `slds-p-vertical_medium`, etc.). The SLDS spacing scale (rem units): `xxx-small` 0.125, `xx-small` 0.25, `x-small` 0.5, `small` 0.75, `medium` 1, `large` 1.5, `x-large` 2, `xx-large` 3.

## Shape

| Element | Border radius |
|---------|---------------|
| Pills | 999px |
| Cards and blocks (web-facing) | 8px |
| Document container (web-facing) | 12px |
| Modal (Salesforce-hosted) | 4px (SLDS default) |
| Inputs (Salesforce-hosted) | 4px (SLDS default) |
| Buttons (Salesforce-hosted) | 4px (SLDS default) |
| Custom pills inside SLDS modals | 999px |

Web-facing has more rounded corners; Salesforce-hosted follows SLDS's modest 4px radius for inputs and buttons.

## Borders

| Element | Border |
|---------|--------|
| Card outer (web-facing) | 1px solid `#EEEEEE` |
| Block divider | 0.5px solid `#EEEEEE` |
| Input (web-facing) | 1px solid `#DDDDDD` |
| Input focus (web-facing) | 1px solid `#196061` + box-shadow ring at 0.12 opacity |
| Input (Salesforce-hosted) | SLDS default; do not override |

## Naming convention for CSS variables

Use the `--m-` prefix for Maica-specific tokens defined inside a component scope:

```css
.my-component {
  --m-teal: #196061;
  --m-teal-deep: #152C36;
  --m-soft: #F7F9F9;
  --m-mid-teal: #7DA0A0;
  --m-border: #EEEEEE;
  --m-tint-light: rgba(25, 96, 97, 0.12);
  --m-tint-medium: rgba(25, 96, 97, 0.20);
}
```

This namespace avoids clashes with platform variables (`--color-*` in claude.ai, `--slds-*` in Salesforce) and makes Maica tokens easy to grep for.

In Salesforce-hosted contexts, the SLDS styling hooks use the `--slds-c-*` and `--slds-g-*` prefixes. Don't conflate the two namespaces. The `--m-` tokens are for your own CSS; the `--slds-c-*` are for talking to SLDS internals.

## Iconography

- Web-facing: use Tabler icons via the webfont CDN. Consistent stroke weight, ~16-17px size in body context, 18-22px in callouts and KPI cards.
- Salesforce-hosted: use `lightning-icon` with the SLDS icon library (utility, standard, custom, action, doctype). Size via the `size` attribute (`xx-small`, `x-small`, `small`, `medium`, `large`).

Pick the right library for the context; don't load both.

## What's NOT in the palette

If you find yourself wanting one of these, stop and reconsider:

- **Red for errors**: Web-facing should use a subtle muted red (e.g. `#C62828`) only for genuine error states; Salesforce-hosted should defer to SLDS's `--slds-g-color-error-base-50`.
- **Amber for warnings**: Same logic. Defer to platform defaults.
- **Bright greens**: The Maica teal is the only green. Don't introduce a "fresh" or "growth" green.
- **Multiple blues**: There are no blues in the palette. If a link needs styling, use Maica Teal as the link colour.

When in doubt, fewer colours. The palette's restraint is the design.
