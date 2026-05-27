# GHL Form CSS Reference

A machine-readable database of CSS selectors for **Go High Level / HighLevel / LeadConnector** inline form embeds — the iframe forms served via `form_embed.js`.

## Why this exists

HighLevel has no official CSS class API. Their own [help doc](https://help.gohighlevel.com/support/solutions/articles/155000006727-how-to-add-custom-css-to-forms-surveys-and-quizzes-) confirms only generic selectors (`input`, `textarea`, `button[type="submit"]`, `.hl-error`, `.error-message`) and tells you to right-click → Inspect for anything specific. There's an [open ideas-board ticket](https://ideas.gohighlevel.com/forms/p/custom-css-classes-on-form-fields) asking for user-assignable classes — years old, never shipped.

This repo collects the selectors the community has confirmed work, with provenance, so you don't have to start from scratch every time you customize a form.

## What's here

- **[`selectors.json`](./selectors.json)** — the database. Every entry has:
  - `selector` — the CSS selector
  - `category` — what part of the form it targets (`structure`, `field-wrapper`, `field-targeting`, `field-input`, `field-types`, `labels`, `validation`, `submit`, `links`, `survey`, `host-page`, `thank-you`)
  - `purpose` — plain-English description
  - `confidence` — `confirmed` (community-verified in production) or `inferred` (needs DevTools verification on your form)
  - `sources` — which references back this up
  - `notes` — caveats, version warnings, edge cases

## How to use it

**For humans:** open `selectors.json`, search for the category or purpose you need, copy the selector.

**For Claude / scripts / tooling:** parse `selectors.json` directly. Filter on `category` + `confidence` to get the high-trust subset.

```bash
# Example: list every confirmed selector
jq '.selectors[] | select(.confidence == "confirmed") | "\(.category): \(.selector)"' selectors.json
```

```bash
# Example: get the submit button selector
jq '.selectors[] | select(.id == "submit-button")' selectors.json
```

## The single most stable hook

Use `data-q="<field_key>"` to target a specific field. It mirrors the field's internal name set in the form builder and survives most DOM refactors that break class-name or `:nth-child` selectors.

```css
/* Brittle */
.form-field-wrapper:nth-child(3) input { ... }

/* Stable */
input[data-q="email"] { ... }
.form-field-wrapper:has(input[data-q="email"]) { ... }
```

## Stability warning

GHL's DOM changes between platform releases. This is a point-in-time snapshot. The `last_updated` field in `selectors.json` records the date of the most recent verification. Treat older entries with appropriate suspicion.

## Sources

The `sources` array in `selectors.json` lists every reference used to compile this database, including:

- **[krishnalewin-hash/tourism-ui-kit](https://github.com/krishnalewin-hash/tourism-ui-kit)** — the closest thing to an authoritative practitioner reference. Battle-tested CSS for inline GHL form embeds.
- **[simple10's GHL survey CSS gist](https://gist.github.com/simple10/0ffbc5a8294efd3d1d91e855b3515ff7)** — survey-specific selectors.
- **[Official HighLevel help doc](https://help.gohighlevel.com/support/solutions/articles/155000006727-how-to-add-custom-css-to-forms-surveys-and-quizzes-)** — generic-only.
- **[HighLevel ideas board ticket](https://ideas.gohighlevel.com/forms/p/custom-css-classes-on-form-fields)** — confirms no official per-field class API exists.

## Contributing

Found a selector that's missing, broken, or has a better alternative? Open a PR against `selectors.json` with:

1. The new/updated entry following the existing schema
2. A `sources` reference (link to where you verified it — a public repo, gist, or your own screenshot of DevTools on a live form)
3. A bump to `last_updated`

## License

MIT.
