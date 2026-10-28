---
name: maica-ui-design
description: Apply Maica's visual identity to UI work, including pages, modals, forms, components, dashboards, and HTML emails. Use this skill whenever building or restyling any user interface for Maica, including phrases like "design X for Maica", "apply Maica brand", "restyle in Maica style", "build a [page/modal/component]" in a Maica context, or any UI work in the VerticAU/ClientCare or VerticAU/MaicaClientCare codebases. Also use it for any HTML email built for Maica, such as Plane notification emails, comment alerts, or digests. The skill covers three distinct contexts (standalone web apps, Salesforce-hosted UI, and HTML email) and teaches you to pick the right one and follow its conventions. Always consult this skill before producing any visual deliverable for Maica that will render on a screen or in an inbox.
---

# Maica UI

Maica's visual identity has three contexts that look different and follow different rules. Picking the wrong context produces UI that either looks off-brand on the platform or fights the platform's conventions. The first job of this skill is helping you pick correctly.

## The three contexts

**Web-facing**: Standalone web apps where Maica controls the entire stack. Examples: the Time Tracker statement page at `time.maica.com.au`, customer-facing portals, public marketing or product surfaces. Maica has full design freedom here. Use Poppins typography, Maica brand colours directly, custom layouts, document-feel aesthetics.

**Salesforce-hosted**: UI that lives inside Maica's managed Salesforce package (`maica__`, `maica_cc__`). Examples: the Planner, scheduling modals, the New Roster modal, configuration screens, any LWC or Aura component. Salesforce Lightning Design System (SLDS) is the dominant constraint. Use `lightning-*` base components, SLDS classes for layout, SLDS styling hooks for brand polish.

**Email**: HTML emails sent from Maica systems or about Maica data, such as Plane work item notifications, comment alerts, and summary digests. Email borrows the web-facing aesthetic and Poppins typography, but the rendering environment (mail clients) is far more constrained, so it is built with table layouts and fully inline styles rather than modern CSS.

Same brand intent, different technical approaches per context. The colour palette is shared everywhere; typography and component construction vary by where the UI lives.

## How to pick

Ask: where does this UI live?

- If it's a route in a Next.js, React, Vue, or similar standalone web app → web-facing
- If it's a Lightning Web Component, Aura component, Visualforce page, or anything rendered inside Salesforce → Salesforce-hosted
- If it's an HTML email rendered in a mail client (notifications, alerts, digests, transactional mail) → email
- If a Visualforce page hosts a separate web app inside an iframe → treat each layer by its own context

If you're not sure, ask the user before building. Doing the wrong one creates rework.

## Brand foundation (shared across all three contexts)

Maica's brand identity comes from `brand.maica.com.au`. These five colours are the entire palette:

| Token | Hex | Use |
|-------|-----|-----|
| Maica Teal | `#196061` | Primary action, brand emphasis, totals, grand-total banner |
| Mid Teal | `#7DA0A0` | Secondary status, neutral outlined elements (e.g. Non-billable pill) |
| Dark | `#152C36` | High-contrast text on teal surfaces, deep-tone numbers |
| Off-white | `#F7F9F9` | Soft surface backgrounds (KPI cards, work item headers) |
| Light Gray | `#EEEEEE` | Borders and dividers |

The teal is the throughline. Whatever context you're in, the primary action is Maica Teal. Resist adding colours outside this palette. If you find yourself wanting a red for errors or an amber for warnings, that's a sign to either use platform defaults (SLDS error red, browser defaults) or to escalate the request.

## Common rules across all contexts

A few patterns are consistent regardless of where the UI lives:

**Hours formatting**: `Xh Ym` (`3h 20m`, `1h`, `30m`). Avoid decimal hours like `0.3h`; they read as typos.

**Date ranges**: Use the word "to" between dates (`18 to 24 May 2026`), not a dash. Never use em dashes anywhere in copy or code.

**Empty Plane work items**: When an issue has no descriptive title (`issue.name === issue.sequence_id`), render the title slot as `"No title"` in italic muted style. The ID pill alone identifies the item.

**Meta blocks**: Client name, project, date range, SOW number, and address each on their own line. Do not concatenate them with separators.

**Billable status**: Lives only at the individual time entry level, never aggregated to a work item header. A work item can mix billable and non-billable entries, so any pill at the header level would be misleading.

**Section headings and labels**: Sentence case. Not ALL CAPS, not Title Case (except for proper nouns).

**No em dashes anywhere**: in copy, in code, in CSS. Use "to", commas, or parentheses instead.

## Picking the deep dive

Once you know the context, read the corresponding reference:

- Web-facing → `references/web-facing.md`
- Salesforce-hosted → `references/salesforce-hosted.md`
- Email → `references/email.md`

All three references include canonical examples and concrete patterns. The brand tokens are shared; how you apply them differs significantly.

For deeper colour and typography reference, see `references/brand-tokens.md`.

## Canonical examples

All three contexts have a real, polished example you can read before writing new UI:

- `assets/time-statement.html` — the web-facing canonical example. A complete statement page with KPI cards, proportional pills for resource utilisation, work item blocks, time entries grouped by work item, brand-aligned typography.
- `assets/roster-modal-slds.md` — the Salesforce-hosted canonical example. An LWC modal with SLDS conventions, `lightning-*` base components, brand applied via styling hooks, and custom UI for the location pill and info callout.
- `assets/email-foundation.html`: the email canonical example. A component gallery showing the colour tokens and every reusable partial (header strip, ID pill, status pills, "No title" rule, section heading, snapshot panel, primary button, footer) as email-safe, table-based, inline-styled HTML. Each partial sits between `<!-- BEGIN partial:name -->` markers so it can be lifted directly. Read this before writing any Maica email. Two complete assembled instances show the partials in context with the Outlook hardening applied: `assets/email-issue-notification.html` (a Plane work-item-created notification) and `assets/email-comment-notification.html` (a comment-added notification using the comment-block partial).

If you're building something similar to either, read the canonical example first; nearly every pattern you'll need is in there.

## What this skill does NOT cover

- Maica's print and PDF document styling (use `maica-document-formatting`)
- Maica Knowledge Base article writing (use `maica-kb-writer`)
- Maica Salesforce code review (use `maica-code-review`)
- Maica release notes (use `maica-release-notes`)

Stay in your lane: this skill is UI for screens and inboxes, not deliverables on paper or written documentation.
