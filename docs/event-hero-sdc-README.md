# Event Hero – Drupal SDC (Twig + CSS, no JavaScript framework)

## Install
1. Copy `components/event_hero` into your custom theme or module, inside its `components/` folder.
2. Run `drush cr`. Canvas discovers the component automatically.
3. Upload the hero photo to the Media Library. `assets/hero-photo-placeholder.webp` is cropped from your screenshot, so use the original full-resolution photo for production.

## Editable fields (Canvas sidebar)
Eyebrow, Title, Heading level (H1/H2), Description, Date, Venue name, City, Primary button (text + URL), Secondary button (text + URL), Hero photo.
A button only appears when both its text and URL are filled in. The meta row hides when date and location are empty.

## Editing by authenticated users
At `/admin/people/permissions`, give the role your authors use (or Authenticated user) the Canvas page-editing and Media Library permissions. Exact permission names vary by Canvas version.

## Styling
- Change `--hero-bg`, `--hero-accent`, and `--hero-heading-font` at the top of `event_hero.css`. Colours were sampled from your design (`#fff3e1`, `#ffad00`).
- The heading font is a serif stack (DM Serif Display, Playfair Display, Georgia). Load your real brand serif in your theme. Body text inherits your theme font.
- Layout: the photo sits under the text on mobile and fades in from the right on screens 62rem and wider.
