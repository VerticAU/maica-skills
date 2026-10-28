---
name: maica-document-formatting
description: "Apply Maica's visual style to any deliverable produced for the Maica or Vertic team. Use whenever the task is to create or restyle a Word document (.docx), spreadsheet (.xlsx), or slide deck (.pptx) that will go to a Maica client, prospect, partner, or internal stakeholder. Triggers include: Statements of Work, scoping documents, demo prep sheets, requirements response matrices, sales decks, market position decks, internal team docs, board updates, conference materials, anything where the user says 'in the Maica style', 'use our brand', 'make it on-brand', or shares an existing Maica file and asks for something in the same style. Use even when the user does not explicitly invoke the brand, as long as the deliverable is clearly for Maica/Vertic. Do NOT use for Maica Knowledge Base articles (use maica-kb-writer for those), email drafts, Slack messages, or generic Salesforce work that is not a branded artifact."
---

# Maica Document Formatting

This skill governs the visual treatment of Maica deliverables across Word documents (.docx), spreadsheets (.xlsx), and slide decks (.pptx). It does not prescribe document structure or content. It describes how individual elements should look whenever they appear.

The Maica brand kit at [brand.maica.com.au](https://brand.maica.com.au/) is the source of truth for colour, typography, and logos. Only assets from the brand kit may be used. If a colour, font, or logo is not in the kit, it is not on-brand.

## The aesthetic

Maica's house style is restrained and confident. It draws from Apple and Claude.ai marketing: lots of whitespace, one idea per slide or section, decisive typography, very little ornament. There are no drop shadows, no gradients, no clip art, no decorative icons, no rainbow palettes, no exclamation marks, no "engagement" punctuation. Bold is used sparingly, only on the words that carry meaning. Em dashes are not used; use a single dash with spaces on either side, or rewrite. Salesforce object and field names, and Maica object names, are set in Consolas on a light grey ground when referenced inline (see [Inline entity and field names](#inline-entity-and-field-names)). The work should feel like it came from a serious software company, not a marketing agency.

## Brand foundations

### Colour palette

Only these five colours may be used. No off-brand teals, blues, greys, or accent colours.

| Token | Hex | Where it goes |
|---|---|---|
| Maica Teal (Primary) | `#196061` | Slide deck backgrounds, headings on light backgrounds, table header bands, accent text, the logo on light backgrounds. The dominant brand colour. |
| Mid Teal (Secondary) | `#7DA0A0` | Secondary accents, subtle dividers, supporting elements that should sit quietly behind the primary teal. |
| Dark | `#152c36` | The safest choice for body text on white. Also available as a deeper background where higher contrast than Maica Teal is needed. |
| Off-white | `#F7F9F9` | Light backgrounds where pure white would feel sterile, and as the body-text colour on dark backgrounds. |
| Light Gray | `#EEEEEE` | Borders, dividers, hairline rules in tables. |

### Typography

**Poppins** is the only typeface for prose. Available from [Google Fonts](https://fonts.google.com/specimen/Poppins). When Poppins is unavailable in a target environment, substitute Arial.

There is exactly one exception: **Consolas** for inline entity and field names, as defined below. Consolas is never used for prose, headings, table headers, or slide titles. No other typeface may appear in a Maica deliverable.

Use the brand kit type scale:

| Style | Weight | Size / line height |
|---|---|---|
| Display | 700 (Bold) | 48 / 56 |
| Heading 1 | 700 (Bold) | 36 / 44 |
| Heading 2 | 600 (SemiBold) | 28 / 36 |
| Heading 3 | 600 (SemiBold) | 20 / 28 |
| Body | 400 (Regular) | 16 / 24 |
| Small | 400 (Regular) | 14 / 20 |
| Label | 500 (Medium) | 12 / 16 |

Use one weight per role consistently across a document. Do not mix Poppins with another sans-serif.

### Inline entity and field names

Whenever a technical name is referenced inside running prose, it is set apart from the prose so the reader can see at a glance that it is a literal name rather than a business concept. This treatment is not decorative and is not optional, because the distinction it carries is a meaningful one: it is how a reader tells `Service Appointment` the object from a service appointment the thing a support worker attends.

**The treatment:**

| Property | Value |
|---|---|
| Typeface | Consolas Regular. Fall back to Courier New only where Consolas is unavailable. |
| Size | One point smaller than the surrounding text (15pt inside 16pt Body, 13pt inside 14pt Small). Consolas has a large x-height and wide glyphs, so at matched size it looks oversized next to Poppins. |
| Colour | Dark `#152c36`, the same as body text. Never teal, never a second colour. |
| Ground | Light Gray `#EEEEEE` character shading behind the name only. |
| Weight and style | Regular. Never bold, italic, or underlined, and never combined with those. |

**What gets the treatment:**

- Salesforce standard and custom object names, whether the label or the API name: `Work Order`, `Service Appointment`, `ServiceContract`, `WorkOrderLineItem__c`.
- Maica object names: `Service Agreement`, `Agreement Item`, `Care Service Visit Plan`.
- Field, picklist, and record type API names.
- Apex class names, flow names, permission set names, file paths, and code identifiers.
- Literal values being quoted from a system.

**What does not:**

- Business concepts and plain-English usage, even where a same-named object exists. In "the service agreement business concept, including funded budgets", service agreement is prose and stays in Poppins.
- Product, company, and team names: Maica, Comlink, ProQuest, Salesforce, Vertic.
- Headings, table header rows, slide titles, captions. If an object name must appear in a heading, it stays in Poppins with no shading. The treatment is for running text only.
- Anything already inside a code block. Code blocks have their own full-width treatment; do not shade individual runs inside them.

**Per-surface implementation:**

*Word (.docx).* Apply character-level shading (`w:shd` on the run properties) rather than the text highlight tool. The Word highlighter offers only a fixed palette of saturated colours and cannot produce `#EEEEEE`. Shade the name itself with no leading or trailing space inside the shaded run, so the grey ground stops exactly where the name does. Define this once as a character style named `Maica Entity` and apply the style, rather than formatting each run by hand: it keeps a long document consistent and makes it editable later. Where a shaded name falls across a line break, leave it; do not insert a non-breaking space to force it onto one line unless the break makes the name unreadable.

*Spreadsheets (.xlsx).* Set the cell font to Consolas and omit the grey ground. Body cells carry no fill anywhere else in this skill, and introducing one here would read as a highlight rather than as a type treatment. Consolas alone carries the distinction. Where only part of a cell's text is a technical name, apply Consolas as rich text to that run.

*Slides (.pptx).* Set the run in Consolas at the surrounding size, in Off-white `#F7F9F9`, with no shading. On the teal slide background a `#EEEEEE` ground would fight the text and pull the eye. Use the treatment sparingly on slides at all, since technical names rarely belong in a deck.

*HTML and web output.* Consolas on a `#EEEEEE` background, with roughly 0.15em horizontal padding and a 3px corner radius. No border.

### The logo

The four official logo files are bundled with this skill at `assets/logos/`, in both PNG and SVG. Embed these files directly into the document being generated. Do not fetch from the web, redraw, or recreate the logo.

| Variant | When to use | Files |
|---|---|---|
| Full logo (default) | White or light backgrounds | `full-logo.png`, `full-logo.svg` |
| Full logo, white | Dark or teal backgrounds | `horizontal-logo-white.png`, `horizontal-logo-white.svg` |
| Logo mark | Tight spaces on light backgrounds | `logo-mark.png`, `logo-mark.svg` |
| Logo mark, white | Tight spaces on dark or teal backgrounds | `logo-mark-white.png`, `logo-mark-white.svg` |

For Word documents, spreadsheets, and slides use the PNG. For digital surfaces (HTML, web embeds) prefer the SVG. Prefer the full horizontal logo unless space genuinely demands the mark. Do not place a tagline next to the logo. Do not put a circle, square, or background tile behind it. The logo stands alone.

---

## Word documents (.docx)

The page itself is white or off-white. Margins are generous, around 1 inch (2.54 cm) on all sides.

### Headings

Use the brand kit type scale. Headings are Maica Teal `#196061` and use Poppins SemiBold or Bold depending on level. Headings have noticeable space above them so each section reads as its own block.

### Body text

Body text is Poppins Regular at 16/24 (Body) or 14/20 (Small) for footnotes and dense supporting copy. Body text colour is Dark `#152c36`. Use generous line height. Paragraphs are separated by space, not by indentation.

### Tables

- Header row: Maica Teal `#196061` fill, white Poppins SemiBold text.
- Body rows: white. No alternating row shading.
- Borders: thin Light Gray `#EEEEEE` hairlines, not bold lines.
- Cells have generous internal padding so text never touches the cell edge.
- Header text and body text are top-aligned with text-wrap on.

### Lists

Use lists only when the content is genuinely a list of equal-weight items. Prefer numbered lists for procedural content. Bullet markers are simple round dots, never icons or coloured shapes.

### Emphasis

Bold is for the one or two words in a sentence that carry meaning. Italics are for proper nouns of works (document titles, product names) and rare emphasis. Do not underline. Do not use coloured text for emphasis - reserve Maica Teal for headings.

### What to avoid

- Coloured page backgrounds.
- Drop caps, decorative dividers, horizontal rules between paragraphs.
- Any colour outside the brand kit.
- Alternating row shading, double borders, bold borders.
- Any typeface other than Poppins, and Consolas for inline entity and field names.
- The Word text highlight tool. Grey grounds are character shading at `#EEEEEE`.

---

## Spreadsheets (.xlsx)

The sheet uses default white cells with gridlines visible. Cell borders are not drawn explicitly; the gridlines do that work.

### Header row

The header row uses a Maica Teal `#196061` fill with white Poppins text, top-aligned, with text-wrap on. The header row is taller than a normal row to give the labels room to breathe. Header text is SemiBold.

### Body cells

Body cells are white. Text is Poppins Regular, 10-11pt, top-aligned, with text-wrap on. Text colour is black or Dark `#152c36`. No fills on body cells.

### Grouping and merging

When several consecutive rows belong to the same category, the category cell is merged across those rows so the label only appears once. The category text is bold. The same merge pattern applies to any other column that groups rows.

### Borders

No explicit cell borders. Default gridlines do the visual separation. If a stronger separator is genuinely needed, use a single thin Light Gray `#EEEEEE` rule, not a bold or double line.

### Page setup

Landscape orientation when the sheet is wide (most response matrices and demo prep sheets are). Set the print options to horizontally centre the content.

### What to avoid

- Coloured body cells. The header row is the only filled row.
- Conditional formatting traffic lights, unless the user specifically asks for a status indicator.
- Bold borders, double lines, coloured borders.
- Emoji or symbols like ✅ ❌ in cells. Use plain words ("Yes" / "No") or booleans.
- Any typeface other than Poppins, and Consolas in cells carrying technical names.
- Grey fills behind technical names. In spreadsheets the Consolas font does that work alone.

---

## Slide decks (.pptx)

Slides are 16:9. The slide background is Maica Teal `#196061`. Text on slides is Off-white `#F7F9F9` or pure white. The logo sits in the bottom-left of every slide, in its white variant, including the cover and end card.

### One idea per slide

A slide carries a single idea. If it starts to feel busy, split it. If it has bullet points and a title and a chart and a footnote, it is doing too much. Remove until the slide is calm.

### Titles

Slide titles use the Display or Heading 1 size from the type scale, in white, Poppins Bold. They typically sit in the upper-left of the slide.

### Body text

Body text is Poppins Regular, scaled up from the document type scale because slides are read from further away (commonly in the 22-36pt range). Colour is Off-white `#F7F9F9`. Lines are short. Paragraphs sit comfortably within the slide with substantial whitespace around them.

### Statement slides

A statement slide carries a single sentence centred in the middle of the slide, with one or two key words bolded for emphasis. Everything else stays regular weight. The slide is otherwise empty except for the logo.

### Tables on dark slides

- Borderless or very thin Off-white horizontal lines only. No vertical lines.
- Text is Off-white `#F7F9F9`.
- The header row carries a single hairline beneath it, no fill.
- If a single hero figure needs emphasis, bold it. Do not introduce an off-brand accent colour.

### Emphasis

Bold the one or two words in a sentence that carry meaning. Mid Teal `#7DA0A0` may be used very sparingly as a quiet secondary accent. Do not use any colour outside the brand kit.

### What to avoid

- Bullet points on cover or statement slides.
- Icons, illustrations, or stock imagery beyond minimal monochrome diagrams.
- Transitions or animations beyond simple cuts.
- More than one accent element per slide.
- Exclamation marks anywhere.
- More than one idea per slide.

---

## The aesthetic check

Before delivering any Maica file, run the file against this list:

1. Is the logo present, in the right variant for the background, and untouched?
2. Is the typeface Poppins throughout, with no Calibri / Times / Helvetica leaking in, and Consolas confined to inline entity and field names?
3. Does every inline object, field, and code name carry the Consolas treatment, one point down, on a `#EEEEEE` ground where the surface calls for it? And is the treatment absent from headings, table headers, and plain-English uses of the same words?
4. Are all colours from the brand kit, with no off-brand teals, blues, greens, yellows, or greys?
5. Is there any em dash anywhere? Replace with " - ".
6. Is bold being used for emphasis only, and on no more than one or two words per sentence?
7. Do slides each carry a single idea, with breathing room around it?
8. Are tables minimal: one teal header band, no alternating rows, hairline borders only?
9. Is the tone restrained and confident, not salesy? No "supercharge", "unleash", "revolutionary", "game-changing".

If anything fails, fix it before presenting the file.

## When matching an existing template

If the user attaches an existing Maica file and asks for output in the same style, treat that file as the source of truth and override anything in this skill that conflicts with it. Existing precedents always win, because the team has already iterated on them.
