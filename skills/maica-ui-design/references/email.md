# Maica UI · Email Context

For HTML emails sent from Maica systems or about Maica data: Plane work item notifications, comment alerts, summary digests, and transactional mail. Email shares the web-facing aesthetic, but mail clients are a hostile rendering target, so the construction rules are different and stricter.

## When this applies

- Any HTML email body rendered in a mail client (Gmail, Outlook desktop and web, Apple Mail, mobile mail apps)
- Notification and alert emails (for example Plane issue created, comment added)
- Digest and summary emails
- Transactional mail (invites, confirmations) where you control the body

If the UI renders in a browser, read `web-facing.md`. If it renders inside Salesforce, read `salesforce-hosted.md`.

## Aesthetic direction

Same document feel as the web-facing context: a calm, bordered document card on a soft grey page, constrained width, generous gutters, hairline dividers, and the teal reserved for the single primary action. An email should read as a tidy notice, not a marketing blast. Reuse the web-facing component vocabulary (header strip, snapshot panel, section heading, button, footer); only the construction changes.

## Hard constraints (read first)

Email clients do not render modern CSS reliably. Build for the worst common denominator (Outlook on Windows, which uses Word's rendering engine) and let richer clients enhance.

- **Tables for layout, not divs.** Flexbox and grid do not work in Outlook. Every layout is `role="presentation"` tables with `cellpadding="0" cellspacing="0" border="0"`.
- **Inline styles for anything critical.** Gmail strips the `<head>`, so a `<style>` block cannot be relied on. Put every important style inline on the element. A head `<style>` is acceptable only for progressive enhancement (for example the Poppins `@import`), never for layout or colour that must appear.
- **No CSS custom properties.** The `--m-` tokens used in web-facing do not resolve in email. Inline the hex values directly.
- **Font fallback is mandatory.** Web fonts do not load in many clients, Outlook desktop especially. Use the stack `'Poppins', 'Segoe UI', Helvetica, Arial, sans-serif` on every text element so it falls back cleanly when Poppins is unavailable.
- **No icon webfonts.** Tabler (used in web-facing) will not render. Use small hosted PNG or SVG images with alt text, or omit icons. Body text needs no icons anyway.
- **Graceful degradation is fine.** `border-radius` and `box-shadow` are ignored by Outlook, giving square corners and no shadow. That is acceptable; do not contort the layout to force them.
- **Content width 600px.** The brand's roughly 680px statement width is tightened to 600px, the safe email column width. Centre it inside a full-width wrapper table.

## Required scaffolding

Every Maica email includes:

- `<meta name="color-scheme" content="light">` and `<meta name="supported-color-schemes" content="light">` to reduce forced dark-mode colour mangling.
- A hidden preheader at the very top of `<body>`: the inbox preview line. Hide it with `display:none;max-height:0;overflow:hidden;mso-hide:all;font-size:1px;line-height:1px;` and colour it to the page background.
- A full-width wrapper table (background `#F0F2F2`) with a centred 600px content column, wrapped in an MSO ghost table so Outlook on Windows honours the width and centring (see Outlook hardening).

## Outlook hardening

Outlook on Windows (the Word rendering engine) breaks in two predictable places. Handle both with MSO conditional comments, which only Outlook reads, so every other client is unaffected.

**Centre the column with a ghost table.** `max-width` and `align="center"` alone leave the column left-aligned in Outlook. Wrap the 600px column in an Outlook-only table:

```html
<!--[if mso]>
<table role="presentation" width="600" align="center" cellpadding="0" cellspacing="0" border="0"><tr><td>
<![endif]-->
<table role="presentation" width="600" align="center" cellpadding="0" cellspacing="0" border="0" style="width:600px;max-width:600px;">
  <!-- column content -->
</table>
<!--[if mso]>
</td></tr></table>
<![endif]-->
```

**Make the button bulletproof.** Outlook ignores padding on an anchor, so a button styled only that way collapses to its text. Put the colour on the cell with `bgcolor`, recreate the padding for Outlook with `mso-padding-alt`, and keep the anchor padding for every other client:

```html
<table role="presentation" cellpadding="0" cellspacing="0" border="0"><tr>
  <td align="center" bgcolor="#196061" style="border-radius:6px;mso-padding-alt:11px 22px;">
    <a href="{{url}}" target="_blank"
       style="display:inline-block;padding:11px 22px;font-family:'Poppins','Segoe UI',Helvetica,Arial,sans-serif;font-weight:500;font-size:14px;line-height:18px;color:#FFFFFF;text-decoration:none;border-radius:6px;">View work item</a>
  </td>
</tr></table>
```

Corners stay square in Outlook, which is acceptable. If a deliverable genuinely needs rounded corners there, wrap the button in a VML `roundrect`, but reach for that only when asked; the square fallback is the default.

## Typography

Poppins with the mandatory fallback stack. Three weights only: 400 body, 500 labels and buttons and pills, 600 headings and emphasised values. Sizes in px (em and rem are unreliable in email):

- Title (the work item name): 22px / 600 / line-height 30px / letter-spacing -0.02em
- Event line (who did what): 13px / 400 / secondary colour #5A6B72
- Section heading: 13px / 600 / sentence case
- Snapshot label: 12px / 500 / #5A6B72
- Body and values: 13 to 14px / 400 / #152C36
- Footer: 12px / 400 / #8E9BA1

## Colour use (inlined hex)

Same five-token palette as everywhere else. In email, inline these exact values:

- Teal `#196061`: primary button background, brand mark, links, filled status pill text
- Mid Teal `#7DA0A0`: outlined secondary pills, muted accents
- Dark `#152C36`: titles and emphasised values
- Off-white `#F7F9F9`: header strip and snapshot panel surfaces
- Light Gray `#EEEEEE`: every border and divider
- Teal tint `rgba(25, 96, 97, 0.12)`: filled pill and brand-tint backgrounds (degrades acceptably; use a flat hex equivalent if a target client mishandles rgba)
- Text secondary `#5A6B72` and tertiary `#8E9BA1`: labels and footer
- Page background behind the card: `#F0F2F2`

## Component partials

These are the email-safe rebuilds of the web-facing components. The canonical gallery in `assets/email-foundation.html` shows each one with copy-ready markup between `<!-- BEGIN partial:name -->` markers. Lift them directly.

- **Header strip**: off-white bar with the top corners rounded, the brand mark (a teal rounded square with "M" plus the Maica wordmark) on the left, the work item ID pill on the right.
- **ID pill**: teal-tint background, teal text, `border-radius:999px`, `display:inline-block` with padding.
- **Status pill**: filled (teal-tint background, teal text) for primary states; outlined (white background, mid-teal text, 1px mid-teal border) for secondary states like Draft.
- **No-title rule**: when a Plane work item has no real title (`issue.name === issue.sequence_id`), render the title slot as italic muted "No title" (`font-style:italic;color:#8E9BA1`). The ID pill identifies the item.
- **Section heading with rule**: a two-cell table. The label cell is shrink-to-fit with `white-space:nowrap`; the second cell is `width:100%` carrying `border-bottom:1px solid #EEEEEE` and a `&nbsp;` at `font-size:0;line-height:0`. This recreates the web-facing trailing rule without the `::after` flex trick.
- **Snapshot panel**: off-white table, hairline border, `border-radius:8px`, label and value rows separated by `border-top:1px solid #EEEEEE`. Labels in a fixed-width left column (about 110px), values on the right. This is the email equivalent of the web-facing KPI cards.
- **Comment block**: the same off-white panel surface as the snapshot, used to quote a comment. A top row carries the commenter's display name (13px / 500 / #152C36) on the left and a muted timestamp (12px / #8E9BA1) on the right, then the rendered comment body below (14px / 400 / line-height 21px / #152C36). Render mentions as `@name` in teal 500 (#196061). Use this for comment-added and reply emails.
- **Primary button (bulletproof)**: a one-cell table whose cell carries `bgcolor="#196061"`, `border-radius:6px`, and `mso-padding-alt` for the padding, wrapping an anchor styled `display:inline-block` with matching padding, white 500-weight text, and `text-decoration:none`. The cell padding covers Outlook (which ignores anchor padding); the anchor padding covers every other client. See Outlook hardening for the markup.
- **Footer**: a hairline rule then 12px tertiary text giving the reason the recipient received the email plus a teal "Manage notifications" link. The reason line is per recipient (assignee, subscriber, or mention), filled by the sender, not hardcoded.

## Assembly order

Header strip, then the body card (event line, title, snapshot panel, optional section heading plus content, primary action), then the footer. The body card carries left and right borders so the three pieces read as one document; the header rounds the top corners and the footer rounds the bottom.

## Data and content rules

- Format hours as `Xh Ym`, never decimal hours.
- Join date ranges with the word "to", never a dash.
- Meta fields (client, project, dates) each on their own line, not concatenated.
- Sentence case for headings and labels.
- No em dashes anywhere, in copy or code or styles. Use "to", commas, or parentheses.
- Plane rich-text fields (descriptions, comment bodies) arrive as JSON or HTML. Render and truncate them; do not dump raw markup. Convert Plane mention tags (`<mention-component entity_identifier="...">`) to a readable `@name`.

## Common mistakes to avoid

- Using divs and flexbox for layout. It collapses in Outlook. Tables only.
- Relying on a head `<style>` block for layout or colour. Gmail strips it. Inline everything critical.
- Forgetting the font fallback stack, so non-Poppins clients render a serif.
- Using `--m-` CSS variables. They do not resolve in email.
- Bringing in icon webfonts. They show as empty boxes.
- Letting the column exceed 600px, which clips on some clients.
- Introducing colours outside the five-token palette. For a genuine error state, use a muted red and escalate.
- Hardcoding the footer reason line instead of setting it per recipient.
- Centring a fixed-width column with only `max-width` and `align`. Outlook left-aligns it without a ghost-table wrapper.
- Putting button padding only on the anchor. Outlook drops it; use `mso-padding-alt` on the cell.
