# Event Hero — Drupal SDC

Component 1 of 6 from `component-specs.md`.

## Compatibility

- Drupal 10.3+ or Drupal 11 (core Single Directory Components).
- No Next.js, React, TypeScript, or JavaScript build step.

## Install

Copy this folder into your custom theme:

```text
web/themes/custom/YOUR_THEME/
  components/
    event-hero/
      event-hero.component.yml
      event-hero.twig
      event-hero.css
```

Keep the YAML, Twig, and CSS files together. Clear Drupal caches after adding the component:

```bash
drush cr
```

## Inputs

Props:
- `eyebrow`: optional short overline.
- `title`: event title; use as the page H1 when appropriate.
- `summary`: optional plain text.
- `date_text`: optional date or date range.
- `venue_name`, `venue_location`: optional venue fields.
- `primary_cta`, `secondary_cta`: optional objects with `label` and `url`.
- `theme_variant`: currently `warm`.

Slot:
- `hero_image`: rendered Drupal Media/image field output. Configure the image field with an image style or responsive image formatter so Drupal controls image derivatives, alt text, responsive markup, and cache metadata.

## Integration guidance

This is a presentation component. Map Event node or Paragraph fields to its props in the parent Twig/render integration. Keep entity loading, field access, URL generation, and image-style generation in Drupal render/preprocess code—not in this component Twig.

The hero image is first in the markup. CSS places it in the right column on desktop and keeps it at the top above all text below 768px.

Check desktop and mobile widths, verify image alt text and image style output, and confirm CTA destinations before publishing.
