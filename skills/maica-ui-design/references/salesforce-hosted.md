# Maica UI · Salesforce-Hosted Context

For UI that lives inside Maica's managed Salesforce package (`maica__`, `maica_cc__`). The New Roster modal in the planner is the canonical example.

## When this applies

Any of these counts as Salesforce-hosted:

- A Lightning Web Component (LWC) in the package
- An Aura component in the package
- A Visualforce page
- Anything that renders inside Lightning Experience or the Salesforce mobile app
- Anything that goes through AppExchange security review

If the UI is in a standalone web app (even if it's Maica's), it's web-facing instead. Read `web-facing.md`.

## The dominant constraint: SLDS

Salesforce Lightning Design System (SLDS) is the design system for everything on the platform. Maica is already building this way (LWCs extend `LightningModal`, components use `slds-*` classes throughout). The visual identity here is "Salesforce, with Maica touches" not "Maica, hosted in Salesforce".

Two versions exist:

- **SLDS 1** is the dominant version, deployed across all production orgs. BEM-style classes (`slds-modal`, `slds-button_brand`), design tokens, and component styling hooks (`--slds-c-*`, `--slds-g-*`).
- **SLDS 2** launched Spring '25, opt-in via the "Salesforce Cosmos" theme. New architecture, more flexible theming. Not yet supporting component-level styling hooks. Most orgs still on SLDS 1.

Target SLDS 1 conventions by default. Use only the patterns that work in both versions.

## Rules you must follow

**Do not override `slds-*` classes directly with custom CSS.** Salesforce warns that SLDS class internals can change in any release. Overrides become brittle and break.

**Form elements cannot be deeply restyled.** Component styling hooks explicitly do NOT cover form elements: `lightning-input`, `lightning-textarea`, `lightning-combobox`, `lightning-radio-group`, `lightning-checkbox-group`. They get the default SLDS appearance, including focus rings and validation states. Accept this.

**Use SLDS styling hooks for customisation.** This is the sanctioned path. Set CSS custom properties like `--slds-c-button-brand-color-background: #196061` on `:host` in your component's CSS. They cascade to the component's internals.

**Native Shadow DOM scoping applies.** `LightningModal` uses Native Shadow, so styles defined outside the modal don't leak in. Component CSS lives inside the LWC.

**No external fonts.** Loading Poppins (or any external font) introduces Content Security Policy issues and security review friction. The Salesforce font stack handles typography.

**Use platform components for inputs.** Dates → `lightning-input type="date"`. Dropdowns → `lightning-combobox`. Multiline text → `lightning-textarea`. Buttons → `lightning-button`. Icons → `lightning-icon`.

## Typography

Use the Salesforce font stack. Don't load custom fonts. SLDS uses "Salesforce Sans" with system fallbacks. Specify `font-family: inherit` on your custom elements; the parent handles it.

## Where brand teal CAN go

These are the safe styling hook targets for the primary action button on `:host`:

```css
:host {
  --slds-c-button-brand-color-background: #196061;
  --slds-c-button-brand-color-background-hover: #0F4748;
  --slds-c-button-brand-color-background-active: #0E3D3E;
  --slds-c-button-brand-color-border: #196061;
  --slds-c-button-brand-color-border-hover: #0F4748;
  --slds-c-button-brand-color-border-active: #0E3D3E;
}
```

Optional extra hooks (use sparingly, only if needed):

```css
/* Tabs */
--slds-c-tabs-list-color-border-selected: #196061;
--slds-c-tabs-item-text-color-active: #196061;

/* SLDS native badges */
--slds-c-badge-color-background: rgba(25, 96, 97, 0.12);
--slds-c-badge-text-color: #196061;
```

Default rule: only the primary action button gets the brand hook in most modals. Don't theme every component.

## What CAN be custom

Some elements aren't SLDS form components and are fair game for custom styling:

- **Custom pills** (e.g. the location pill in the New Roster modal): inline-flex container with brand-tinted background, custom padding, rounded corners.
- **Custom callouts** (e.g. "X rosters will be created"): a custom div with brand-tinted background, info icon, optional action link.
- **Custom status indicators** that don't use form elements (e.g. a coloured dot next to a label).
- **Custom illustrations or graphics** inside the modal body.
- **Layout structure** inside the body: how you arrange form elements, group sections, add spacing.

These survive SLDS conventions because they're not SLDS components; they're custom UI placed inside an SLDS shell.

## Form layout pattern

Use SLDS form classes:

```html
<div class="slds-form slds-form_stacked">
  <div class="slds-form-element">
    <label class="slds-form-element__label">Field name</label>
    <lightning-input ... variant="label-hidden"></lightning-input>
  </div>
  ...
</div>
```

Use `variant="label-hidden"` on `lightning-input` and similar, and provide your own label via `.slds-form-element__label`. This gives you control over the label styling while still using the SLDS-native input.

For inline date ranges (start + "to" + end), use SLDS grid:

```html
<div class="slds-grid slds-gutters_x-small slds-grid_vertical-align-center">
  <div class="slds-col">
    <lightning-input type="date" ... variant="label-hidden"></lightning-input>
  </div>
  <div class="slds-col slds-no-flex slds-text-color_weak">to</div>
  <div class="slds-col">
    <lightning-input type="date" ... variant="label-hidden"></lightning-input>
  </div>
</div>
```

## Modal pattern

Always extend `LightningModal` for modals. Use the slot components:

```html
<template>
  <lightning-modal-header label="Modal title"></lightning-modal-header>
  <lightning-modal-body>
    <!-- form content -->
  </lightning-modal-body>
  <lightning-modal-footer>
    <lightning-button label="Cancel" onclick={handleCancel}></lightning-button>
    <lightning-button label="Submit" variant="brand" onclick={handleSubmit} class="slds-m-left_x-small"></lightning-button>
  </lightning-modal-footer>
</template>
```

`lightning-modal-header` handles ARIA labelling. `LightningModal` handles focus management. Don't reimplement these.

## Spacing utilities

Use SLDS spacing utilities for layout adjustments rather than custom CSS:

- Margin: `slds-m-around_*`, `slds-m-vertical_*`, `slds-m-horizontal_*`, `slds-m-top_*`, etc.
- Padding: `slds-p-*` (same shape)
- Sizes: `xxx-small`, `xx-small`, `x-small`, `small`, `medium`, `large`, `x-large`, `xx-large`

Example: `slds-m-top_x-small` (0.5rem top margin), `slds-p-around_medium` (1rem all around).

## Icons

Use `lightning-icon` with the SLDS icon library:

```html
<lightning-icon icon-name="utility:calendar" size="xx-small"></lightning-icon>
```

Available libraries: `utility:*`, `standard:*`, `custom:*`, `action:*`, `doctype:*`. Browse at lightningdesignsystem.com/icons.

For brand teal on an icon inside a custom element, use the styling hook scoped to a custom class:

```css
.maica-callout__icon {
  --slds-c-icon-color-foreground-default: #196061;
}
```

## Reading the canonical example

The full SLDS-compliant Maica modal pattern is in `assets/roster-modal-slds.md`. It includes:

- Complete LWC HTML template with all `lightning-*` components correctly placed
- Component CSS with styling hooks for brand application
- The custom location pill and rosters-count callout (custom UI patterns)
- Notes on preserving existing JS behaviour
- Explicit "don't do these things" list

Read it before building a new modal or restyling an existing one.

## Common mistakes to avoid

- Don't load Poppins or any external font.
- Don't write custom CSS that targets `.slds-*` classes directly. Use styling hooks.
- Don't try to restyle the focus ring on `lightning-input`. SLDS owns that.
- Don't reimplement the modal shell with custom markup; use `LightningModal` and its slots.
- Don't override the `LightningModal` `size` attribute via custom CSS; use the `size` prop (`small`, `medium`, `large`).
- Don't add brand teal to every visible element. The primary action button is enough; any further teal should be a deliberate, sparing choice.

## When the design feels off

If the UI feels "vacant" in this context (the original complaint about the New Roster modal):

1. Add labels to any unlabelled fields
2. Group related fields with `slds-m-bottom_*` spacing tighter than the default
3. Convert any visible-but-passive value into a SLDS-compliant control with proper affordance (combobox with chevron, etc.)
4. Wrap derived information in a custom callout with brand-tinted background

If the UI feels "off-brand":

1. Confirm the primary action button has the brand styling hooks applied
2. Confirm custom pills and callouts use Maica teal (not random teal-like SLDS defaults)
3. Confirm the icon column or decorative elements (if any) use the muted SLDS text colour, not random colours
