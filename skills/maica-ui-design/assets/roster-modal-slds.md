# New Roster Modal · SLDS-compliant Redesign

## What this changes

Refreshes the `createRosterModal` LWC visually while keeping it fully compliant with Salesforce Lightning Design System (SLDS) conventions. The modal already extends `LightningModal` and uses SLDS classes, so this is a polish pass, not a rebuild.

Goals:

- Tighter visual rhythm so the modal doesn't feel vacant
- Labels on every field for clarity
- Group the derived "X rosters will be created" message with Recurrence, since it depends on it
- Apply Maica brand teal where SLDS allows (primary button, custom pill, custom callout)
- Use SLDS-native form components throughout

## SLDS constraints honoured

These shaped the design. Worth reading before editing:

1. **Form elements cannot be deeply restyled.** SLDS component styling hooks (`--slds-c-*`) explicitly do NOT cover form elements (`lightning-input`, `lightning-textarea`, `lightning-combobox`, `lightning-radio-group`, `lightning-checkbox-group`). They get the default SLDS appearance, including focus rings.
2. **No direct overrides of `slds-*` classes.** Salesforce warns these can change in any release. Customisation goes through styling hooks (`--slds-c-button-brand-color-background`) and custom classes for non-SLDS UI.
3. **Native Shadow DOM scoping.** `LightningModal` uses Native Shadow, so component CSS lives inside the LWC and styles can't leak in or out.
4. **No external fonts.** Use the platform font stack. Loading Poppins (or any external font) introduces CSP issues and security review headaches.
5. **Standard SLDS spacing utilities** (`slds-m-*`, `slds-p-*`) for layout adjustments where applicable.

## The redesign

### `createRosterModal.html`

```html
<template>

  <lightning-modal-header label="New Roster"></lightning-modal-header>

  <lightning-modal-body>
    <div class="slds-form slds-form_stacked">

      <!-- Schedule: lightning-input type="date" x 2 with shared label -->
      <div class="slds-form-element">
        <label class="slds-form-element__label">Schedule</label>
        <div class="slds-grid slds-gutters_x-small slds-grid_vertical-align-center">
          <div class="slds-col">
            <lightning-input
              type="date"
              label="Start date"
              variant="label-hidden"
              value={startDate}
              onchange={handleStartChange}>
            </lightning-input>
          </div>
          <div class="slds-col slds-no-flex slds-text-color_weak">to</div>
          <div class="slds-col">
            <lightning-input
              type="date"
              label="End date"
              variant="label-hidden"
              value={endDate}
              onchange={handleEndChange}>
            </lightning-input>
          </div>
        </div>
      </div>

      <!-- Location: custom pill + address (custom UI, not an SLDS form element) -->
      <div class="slds-form-element">
        <label class="slds-form-element__label">Location</label>
        <span class="maica-loc-pill">
          <span class="maica-loc-pill__mark">
            <lightning-icon icon-name="utility:location" size="xx-small" variant="inverse"></lightning-icon>
          </span>
          {locationName}
        </span>
        <div class="slds-text-body_small slds-text-color_weak slds-m-top_xx-small">
          {locationAddress}
        </div>
      </div>

      <!-- Recurrence: lightning-combobox + derived count callout -->
      <div class="slds-form-element">
        <label class="slds-form-element__label">Recurrence</label>
        <lightning-combobox
          name="frequency"
          label="Recurrence"
          variant="label-hidden"
          value={frequency}
          options={frequencyOptions}
          onchange={handleFrequencyChange}>
        </lightning-combobox>
        <div class="maica-callout slds-m-top_x-small">
          <lightning-icon icon-name="utility:info" size="xx-small" class="maica-callout__icon"></lightning-icon>
          <span>
            <span class="maica-callout__count">{rosterCount} rosters</span>
            will be created based on this schedule
          </span>
          <a href="javascript:void(0)" class="maica-callout__link" onclick={handleShowDetails}>
            Show details
          </a>
        </div>
      </div>

      <!-- Status: lightning-combobox -->
      <div class="slds-form-element">
        <label class="slds-form-element__label">Status</label>
        <lightning-combobox
          name="status"
          label="Status"
          variant="label-hidden"
          value={status}
          options={statusOptions}
          onchange={handleStatusChange}>
        </lightning-combobox>
      </div>

      <!-- Notes: lightning-textarea -->
      <div class="slds-form-element">
        <label class="slds-form-element__label">Notes</label>
        <lightning-textarea
          name="notes"
          label="Notes"
          variant="label-hidden"
          value={notes}
          placeholder="Add notes for this roster (optional)"
          onchange={handleNotesChange}>
        </lightning-textarea>
      </div>

    </div>
  </lightning-modal-body>

  <lightning-modal-footer>
    <lightning-button label="Cancel" onclick={handleCancel}></lightning-button>
    <lightning-button
      label="Create"
      variant="brand"
      class="slds-m-left_x-small"
      onclick={handleCreate}>
    </lightning-button>
  </lightning-modal-footer>

</template>
```

### `createRosterModal.css`

```css
/* Brand the primary action button via SLDS styling hooks.
   Scoped to :host so it only affects buttons inside this modal. */
:host {
  --slds-c-button-brand-color-background: #196061;
  --slds-c-button-brand-color-background-hover: #0F4748;
  --slds-c-button-brand-color-background-active: #0E3D3E;
  --slds-c-button-brand-color-border: #196061;
  --slds-c-button-brand-color-border-hover: #0F4748;
  --slds-c-button-brand-color-border-active: #0E3D3E;
}

/* Custom Maica location pill.
   This is custom UI, not an SLDS form element, so direct styling is fine. */
.maica-loc-pill {
  display: inline-flex;
  align-items: center;
  gap: 0.4375rem;
  background: rgba(25, 96, 97, 0.12);
  color: #196061;
  padding: 0.25rem 0.75rem 0.25rem 0.375rem;
  border-radius: 999px;
  font-size: 0.8125rem;
  font-weight: 500;
  line-height: 1.2;
}
.maica-loc-pill__mark {
  width: 1.125rem;
  height: 1.125rem;
  border-radius: 0.25rem;
  background: #196061;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  --slds-c-icon-color-foreground-default: #ffffff;
}

/* Custom Maica callout for the derived "X rosters will be created" message.
   Also custom UI. */
.maica-callout {
  display: flex;
  align-items: center;
  gap: 0.5625rem;
  background: rgba(25, 96, 97, 0.10);
  border-radius: 0.25rem;
  padding: 0.5rem 0.75rem;
  font-size: 0.8125rem;
  color: #080707;
}
.maica-callout__icon {
  --slds-c-icon-color-foreground-default: #196061;
  flex-shrink: 0;
}
.maica-callout__count {
  font-weight: 700;
}
.maica-callout__link {
  margin-left: auto;
  color: #196061;
  font-weight: 500;
  white-space: nowrap;
  text-decoration: none;
  font-size: 0.8125rem;
}
.maica-callout__link:hover,
.maica-callout__link:focus {
  text-decoration: underline;
  outline: none;
}
```

### `createRosterModal.js` (changes)

Existing `@api` properties and handlers should be preserved. The redesign adds rendering needs:

```javascript
import { api, track } from 'lwc';
import LightningModal from 'lightning/modal';

export default class CreateRosterModal extends LightningModal {
  @api frequency;
  @api startDate;
  @api endDate;
  @api rosterId;

  // Existing: location name and address derived from the selected location record.
  // Surface these as gettable values for the template:
  get locationName() {
    return this._location?.name ?? '';
  }
  get locationAddress() {
    return this._location?.address ?? '';
  }

  // Existing: count derived from date range and recurrence.
  // Surface for the callout:
  get rosterCount() {
    return this._calculatedRosterCount;
  }

  // Recurrence options for the combobox.
  get frequencyOptions() {
    return [
      { label: 'Daily', value: 'Daily' },
      { label: 'Weekly', value: 'Weekly' },
      { label: 'Fortnightly', value: 'Fortnightly' },
      { label: 'Monthly', value: 'Monthly' }
    ];
  }

  // Status options for the combobox.
  get statusOptions() {
    return [
      { label: 'Draft', value: 'Draft' },
      { label: 'Published', value: 'Published' }
    ];
  }

  // ... existing handlers (handleStartChange, handleEndChange, handleFrequencyChange,
  //     handleStatusChange, handleNotesChange, handleCancel, handleCreate, handleShowDetails)
}
```

## Implementation notes

### Preserve existing functionality

The modal's existing behaviour is intact. The redesign is the markup and CSS, not the data flow. Confirm:

- Date picker behaviour on Schedule Start and End. `lightning-input type="date"` provides the picker; the existing onchange handlers wire up unchanged.
- Location selector behaviour. If location is selected upstream and passed in, render via the pill. If it's selected inside the modal, the existing selector logic stays.
- Recurrence options come from existing logic; map them into `frequencyOptions` for the combobox.
- Status options similarly.
- The derived `rosterCount` is recalculated when date range or recurrence change. Keep that wiring.
- "Show details" link should expand to show the individual roster dates that will be created.

### Don't do these things

- Don't add custom CSS that targets SLDS classes (`.slds-modal`, `.slds-input`, etc.). Use the styling hooks instead.
- Don't load external fonts. The Salesforce font stack handles typography.
- Don't try to restyle the focus ring on form elements. SLDS owns that.
- Don't change the modal width via custom CSS. If a different size is needed, use the `size` attribute on `LightningModal` (`small`, `medium`, `large`).

### Optional: extend brand coverage

If brand teal is wanted on more places without breaking SLDS conventions, these are the safe styling hooks to add to `:host`:

```css
/* Tabs (if any are introduced) */
--slds-c-tabs-list-color-border-selected: #196061;
--slds-c-tabs-item-text-color-active: #196061;

/* Badge / pill backgrounds for SLDS native badges */
--slds-c-badge-color-background: rgba(25, 96, 97, 0.12);
--slds-c-badge-text-color: #196061;
```

But for this modal specifically, only the brand button needs the hook. Don't over-apply.

### Accessibility

`lightning-modal-header`, `lightning-modal-body`, and `lightning-modal-footer` handle ARIA correctly out of the box. The `label="New Roster"` on the header sets `aria-labelledby`. Focus management is handled by `LightningModal`. No additional ARIA work needed unless custom elements (the location pill, the callout link) need specific labelling.

## Out of scope

- Don't change the modal's open/close behaviour or the trigger.
- Don't change the underlying data model.
- Don't add new fields. If something looks missing, flag for follow-up.
- Don't override the `LightningModal` `size` default unless there's a specific need; standard size is fine for this content.
