# Component Specifications --- Event Homepage

## 1. Purpose and scope

This document defines the reusable component system for the supplied
event homepage design. It is intended to guide implementation in Drupal
using **Single Directory Components (SDC)** and to give content editors
control over content and media without exposing layout code.

The page has two major regions:

1.  **Event hero** --- eyebrow, event title, summary, date, venue, two
    calls to action, and a food-focused hero image.
2.  **Audience resources** --- a responsive collection of four audience
    cards: Maker, Buyer, Distributor, and Service Provider.

The specifications below describe the visible design, component
boundaries, data contracts, editor controls, accessibility, responsive
behavior, and acceptance criteria. They do not assume functionality that
is not represented in the reference image.

------------------------------------------------------------------------

## 2. Design inventory

  ------------------------------------------------------------------------------
  Visible element   Proposed component     Reuse level         Rationale
  ----------------- ---------------------- ------------------- -----------------
  Full-width hero   `event-hero`           Page-section        Owns the hero's
  region                                   component           two-column
                                                               composition and
                                                               responsive
                                                               behavior.

  Small uppercase   `eyebrow`              Primitive           Reusable overline
  introductory line                                            treatment;
                                                               content remains
                                                               configurable.

  Large event       `heading` / native     Primitive           Use semantic HTML
  heading           heading element                            rather than a
                                                               bespoke heading
                                                               component unless
                                                               the design system
                                                               needs a shared
                                                               heading API.

  Event summary     `rich-text` / text     Primitive/content   Editorial copy
                    field                  field               should not
                                                               require a
                                                               dedicated
                                                               component.

  Date with         `event-detail`         Reusable primitive  A date or time
  calendar icon                                                value paired with
                                                               an icon and
                                                               accessible text.

  Venue with        `event-detail`         Reusable primitive  Same structure as
  location icon                                                date detail, with
                                                               a different icon
                                                               and value.

  Register /        `button`               Design-system       Shared visual and
  Explore buttons                          primitive           interaction
                                                               contract for
                                                               links styled as
                                                               actions.

  Audience card     `audience-card-grid`   Section component   Controls grid
  collection                                                   layout, gaps,
                                                               breakpoints, and
                                                               card ordering.

  Audience card     `audience-card`        Reusable component  Repeated four
                                                               times with
                                                               different content
                                                               and imagery.

  Card image        Image field / media    Content/media       Reuses Drupal
                    item                   primitive           Media and image
                                                               styles; avoid
                                                               hard-coded image
                                                               URLs.

  Overlapping icon  `icon-badge`           Reusable primitive  Shared visual
  disc                                                         treatment
                                                               positioned at the
                                                               image/content
                                                               boundary.

  Card title,       Card content fields    Content fields      These are data,
  description, link                                            not independent
                                                               layout
                                                               components.
  ------------------------------------------------------------------------------

### Component boundary decisions

-   Keep `event-hero` as a composed section. It owns the hero layout,
    not the internal semantics of every text element.
-   Keep `event-detail` independent because the date and venue share the
    same visual pattern and may be reused on event teasers or detail
    pages.
-   Keep `audience-card` independent from `audience-card-grid`. The card
    owns its own image/content structure; the grid owns collection
    layout.
-   Keep `icon-badge` a small visual primitive. Do not create separate
    Maker, Buyer, Distributor, and Provider card templates.
-   Use Drupal core fields, Media, links, and semantic HTML for
    straightforward content. Avoid wrapping every text element in a
    custom component.
-   Do not make the whole card a single clickable element when it
    contains a separate link. Use a clear, keyboard-accessible link for
    the resource action.

------------------------------------------------------------------------

## 3. Component specifications

## 3.1 `event-hero`

**Type:** Composed page-section component\
**Purpose:** Present the event identity, summary, event logistics, calls
to action, and hero visual.

### Structure

-   Outer section with a full-width background.
-   Inner constrained container.
-   Text/content column.
    -   Eyebrow.
    -   Main heading.
    -   Summary.
    -   Event details row.
    -   CTA group.
-   Image/visual column.
-   On small screens, content and image stack in a deliberate order.

### Suggested SDC interface

  --------------------------------------------------------------------------
  Prop               Type                          Required Default / notes
  ------------------ ---------------- --------------------- ----------------
  `eyebrow`          string                              No Short
                                                            introductory
                                                            line above the
                                                            title.

  `title`            string                             Yes Plain text;
                                                            render as the
                                                            page's primary
                                                            `h1` when this
                                                            is the page
                                                            hero.

  `summary`          string or safe                      No Keep formatting
                     rich text                              limited; do not
                                                            accept arbitrary
                                                            unsafe markup.

  `hero_image`       Drupal                             Yes Use a responsive
                     image/media                            image style and
                     reference                              meaningful
                                                            alternative
                                                            text.

  `hero_image_alt`   string                     Conditional Required when
                                                            image conveys
                                                            information;
                                                            empty alt is
                                                            acceptable only
                                                            when decorative.

  `date_text`        string                              No Human-readable
                                                            event date
                                                            range.

  `venue_name`       string                              No Venue name.

  `venue_location`   string                              No City / region
                                                            text.

  `primary_cta`      link object                         No Usually
                                                            registration.

  `secondary_cta`    link object                         No Usually event
                                                            exploration.

  `theme_variant`    enum                                No `warm`
                                                            (default); allow
                                                            additional
                                                            variants only
                                                            when a real use
                                                            case exists.
  --------------------------------------------------------------------------

**Slots / nested content:** Prefer explicit props for the two event
details and two CTAs because their count and meaning are fixed in this
design. If future event pages need arbitrary metadata, replace the fixed
details with a repeatable `details` collection rather than adding more
one-off props.

### Visual behavior

-   Desktop: two-column composition, with the text column on the left
    and large food image on the right.
-   Image fills its visual area using `object-fit: cover`; focal point
    should be editor-configurable where the Drupal media/image field
    supports it.
-   Text column has a comfortable maximum width to preserve readable
    line lengths.
-   Hero background uses a warm cream token; image blends into the
    section without a visible card frame.
-   CTA buttons are aligned horizontally when space permits and
    wrap/stack on narrow screens.
-   The section must not rely on fixed pixel heights. Use content-driven
    height and responsive spacing.

### Editor flexibility

Editors can update the eyebrow, title, summary, event date, venue, CTAs,
image, alt text, and focal point. Editors should not control arbitrary
column widths, font sizes, or CSS classes. The visual theme should be a
constrained choice, not a free-form styling field.

### Empty-state rules

-   Hide the eyebrow, summary, date detail, venue detail, or individual
    CTA when its value is absent.
-   Do not render an empty metadata row.
-   The title and hero image are required for a published hero.
-   If the image is missing in draft content, show an editorial
    placeholder in preview; do not publish a broken image.

------------------------------------------------------------------------

## 3.2 `event-detail`

**Type:** Small reusable component\
**Purpose:** Render a single event fact with a meaningful icon and
readable value.

### Suggested interface

  -------------------------------------------------------------------------
  Prop             Type                          Required Notes
  ---------------- ---------------- --------------------- -----------------
  `icon`           enum / icon                        Yes Constrained set,
                   identifier                             e.g. `calendar`
                                                          or `location`.

  `label`          string                              No Accessible label
                                                          when the visible
                                                          value alone is
                                                          not sufficiently
                                                          clear.

  `value`          string                             Yes Human-readable
                                                          date or location.

  `url`            link URL                            No Optional; only
                                                          use when the
                                                          detail is
                                                          genuinely
                                                          navigable.
  -------------------------------------------------------------------------

### Rules

-   Use an inline SVG or approved icon library with decorative icons
    hidden from assistive technology when the text already communicates
    the meaning.
-   Do not encode the date or venue only in an icon or image.
-   On desktop, date and venue may appear side by side. On mobile, allow
    wrapping or vertical stacking.
-   Keep date formatting editorially controlled or use a Drupal date
    field with a configured display format. Store dates as structured
    date values when they are used for filtering, sorting, or event
    logic.

------------------------------------------------------------------------

## 3.3 `button`

**Type:** Design-system primitive\
**Purpose:** Provide consistent action styling and accessible link
behavior.

### Suggested interface

  ---------------------------------------------------------------------------
  Prop                Type                          Required Notes
  ------------------- ---------------- --------------------- ----------------
  `label`             string                             Yes Visible action
                                                             text.

  `url`               URL                       Yes for link Use a real link
                                                             for navigation.

  `variant`           enum                                No `primary`,
                                                             `secondary`;
                                                             default
                                                             `primary`.

  `size`              enum                                No `medium` by
                                                             default.

  `open_in_new_tab`   boolean                             No Default false;
                                                             only enable when
                                                             editorially
                                                             justified.
  ---------------------------------------------------------------------------

### Visual variants

-   **Primary:** orange fill, dark text, rounded pill-like corners.
-   **Secondary:** transparent / cream fill, dark outline, dark text.
-   Hover and focus states must be visible and consistent.
-   Use an anchor for navigation and a button only for an in-page
    action. Do not use a clickable `div`.

### Editor flexibility

Editors choose the link and a constrained style variant. They cannot
enter custom colors, padding, border radius, or arbitrary CSS classes.

------------------------------------------------------------------------

## 3.4 `audience-card-grid`

**Type:** Section / collection-layout component\
**Purpose:** Display a set of audience resource cards in a consistent
responsive grid.

### Suggested interface

  ---------------------------------------------------------------------------
  Prop                Type                          Required Notes
  ------------------- ---------------- --------------------- ----------------
  `heading`           string                              No Optional section
                                                             heading; not
                                                             visible in the
                                                             supplied design.

  `cards`             ordered                            Yes Each item
                      collection of                          references an
                      audience-card                          audience card
                      items                                  content item or
                                                             component data.

  `columns_desktop`   enum                                No Default 4;
                                                             constrain to
                                                             supported layout
                                                             options.
  ---------------------------------------------------------------------------

### Layout behavior

-   Desktop: four equal-width columns in one row at the reference
    viewport.
-   Tablet: two columns where space allows.
-   Mobile: one column.
-   Maintain consistent gaps and card heights where practical; do not
    force descriptions to clip.
-   Cards should follow source/editorial order. Drag-and-drop ordering
    may be provided by the Drupal entity reference field.
-   If the number of cards changes, the grid adapts without a template
    change.

### Boundary

The grid must not own card-specific image, title, description, icon, or
link markup. It only places child cards and controls collection-level
spacing and responsive columns.

------------------------------------------------------------------------

## 3.5 `audience-card`

**Type:** Reusable content component\
**Purpose:** Promote a resource destination for one audience type.

### Suggested interface

  -----------------------------------------------------------------------------
  Prop             Type                          Required Notes
  ---------------- ---------------- --------------------- ---------------------
  `title`          string                             Yes E.g. "I'm a Maker".

  `description`    string                             Yes Short supporting
                                                          statement.

  `image`          Drupal                             Yes Use a consistent card
                   Media/image                            image style and crop
                   reference                              ratio.

  `image_alt`      string                     Conditional Meaningful alt text
                                                          for informative
                                                          images.

  `icon`           enum / icon                        Yes E.g. `sprout`, `bag`,
                   identifier                             `users`, `truck`; map
                                                          to approved assets.

  `link`           link object                        Yes Resource destination.

  `link_label`     string                             Yes E.g. "See resources
                                                          for makers".

  `audience_key`   enum                                No `maker`, `buyer`,
                                                          `distributor`,
                                                          `service_provider`;
                                                          useful for analytics
                                                          or display variants,
                                                          not for selecting
                                                          separate templates.
  -----------------------------------------------------------------------------

### Internal structure

1.  Card wrapper.
2.  Image region.
3.  Icon badge overlapping the image/content boundary.
4.  Content region containing title, description, and resource link.

### Visual behavior

-   White surface, subtle neutral border, rounded corners, and
    consistent clipping.
-   Image spans the card width and uses a consistent aspect ratio.
-   Icon badge is a white circular surface with orange icon and overlaps
    the lower image edge.
-   Content padding is consistent across cards.
-   Resource link is visually distinct and remains readable even when
    descriptions wrap.
-   The card should not depend on the title being a specific audience
    label.

### Editor flexibility

Editors can create, edit, reorder, and remove cards; change image, alt
text, title, description, icon choice, link label, and destination. Icon
choices must come from an approved icon set. The image style, badge
geometry, border, and spacing remain design-system controlled.

### Link behavior

Use a descriptive anchor label. The card itself is not a second, nested
link. If the card image is decorative because the title and description
already provide the same information, use empty alt text; otherwise
provide a useful description without repeating adjacent copy.

------------------------------------------------------------------------

## 3.6 `icon-badge`

**Type:** Visual primitive\
**Purpose:** Provide the small circular icon treatment used on audience
cards.

### Suggested interface

  ----------------------------------------------------------------------------
  Prop                 Type                          Required Notes
  -------------------- ---------------- --------------------- ----------------
  `icon`               approved icon                      Yes Required.
                       identifier                             

  `size`               enum                                No Use the
                                                              card-badge size
                                                              by default.

  `accessible_label`   string                              No Only needed if
                                                              the icon
                                                              communicates
                                                              information not
                                                              already in text.
  ----------------------------------------------------------------------------

### Rules

-   Use a consistent circular white background and orange icon token.
-   The icon should be decorative when the adjacent card title already
    identifies the audience.
-   Do not allow arbitrary uploaded icon files per card unless there is
    a validated editorial requirement; a controlled icon selector keeps
    visual quality and accessibility consistent.

------------------------------------------------------------------------

## 4. Drupal content and authoring model

A practical Drupal model is:

### Event content

Use an `Event` content type (or the existing event content type if one
already exists) with fields for:

-   Event title.
-   Eyebrow.
-   Summary.
-   Hero media reference.
-   Event start and end date/time (structured date field(s)).
-   Venue name.
-   Venue location.
-   Primary CTA link.
-   Secondary CTA link.
-   Optional constrained hero theme variant.

The hero component should receive these values from the event entity
through a paragraph/layout builder display or a node template, depending
on the site's existing page-building approach.

### Audience resource content

Use an `Audience resource` content type or a Paragraph bundle if the
cards are only ever embedded in one page and do not need independent
URLs.

Recommended fields:

-   Title.
-   Short description.
-   Card image media reference.
-   Image alternative text (or rely on the media image field's alt
    text).
-   Icon selection from an approved list.
-   Resource link.
-   Link label.
-   Optional audience key.

Reference these items from a multi-value entity reference field on the
homepage or an `Audience resources` paragraph. This supports ordering
and reuse without duplicating card content.

### Paragraphs / Layout Builder decision

-   Prefer **Paragraphs** when editors need to assemble and reorder a
    curated page section within a known set of layouts.
-   Prefer **Layout Builder** when the site already uses it as its page
    composition system and editors need broader layout control.
-   Prefer a dedicated homepage content type with entity-reference
    fields when the page structure is stable and should not be freely
    rearranged.
-   Do not introduce both Paragraphs and Layout Builder solely for this
    page. Choose the pattern already established in the project.

### SDC organization

A possible component directory structure:

``` text
components/
  event-hero/
    event-hero.component.yml
    event-hero.twig
    event-hero.css
  event-detail/
    event-detail.component.yml
    event-detail.twig
    event-detail.css
  button/
    button.component.yml
    button.twig
    button.css
  audience-card-grid/
    audience-card-grid.component.yml
    audience-card-grid.twig
    audience-card-grid.css
  audience-card/
    audience-card.component.yml
    audience-card.twig
    audience-card.css
  icon-badge/
    icon-badge.component.yml
    icon-badge.twig
    icon-badge.css
```

The exact location and naming should follow the Drupal theme's
conventions. Keep each component's schema, Twig, and component-specific
CSS together. Use shared design tokens for colors, spacing, typography,
borders, and radii rather than duplicating raw values across components.

------------------------------------------------------------------------

## 5. Design tokens and visual guidance

The reference image suggests the following token categories. Exact
values should be sampled from the approved design file or established
design system before implementation; values below are semantic
recommendations, not measured pixel specifications.

  -----------------------------------------------------------------------
  Token                               Intended use
  ----------------------------------- -----------------------------------
  `color-brand-accent`                Orange CTA, icons, icon badge.

  `color-hero-surface`                Warm cream hero background.

  `color-surface`                     White card and page surfaces.

  `color-text-primary`                Main headings and body copy.

  `color-text-secondary`              Supporting card descriptions.

  `color-border-subtle`               Card outlines and secondary button
                                      border.

  `font-display`                      High-contrast serif event headline.

  `font-body`                         Sans-serif body copy, labels,
                                      buttons, and card text.

  `space-section`                     Vertical spacing between page
                                      regions.

  `radius-card`                       Rounded card corners.

  `radius-pill`                       Rounded CTA buttons.

  `shadow-subtle`                     Optional restrained elevation if
                                      supported by the design system.
  -----------------------------------------------------------------------

Typography should be responsive and use fluid sizing where appropriate.
The hero title is the strongest typographic element; card titles and
descriptions should remain clearly subordinate.

Do not infer exact font families, color hex values, or spacing
measurements from a raster screenshot. Confirm those against the source
design system or approved brand assets.

------------------------------------------------------------------------

## 6. Responsive behavior

  -----------------------------------------------------------------------
  Area              Wide desktop      Tablet            Mobile
  ----------------- ----------------- ----------------- -----------------
  Hero layout       Two columns; copy Two columns if    Stack content and
                    left, image right readable;         image
                                      otherwise stack   

  Hero title        Large display     Reduced fluid     Compact, readable
                    heading           size              heading

  Event details     Inline row        Wrap as needed    Stack or wrap

  CTAs              Inline            Wrap              Stack or wrap to
                                                        available width

  Audience grid     Four columns      Two columns       One column

  Audience card     Consistent        Same ratio        Same ratio unless
  image             landscape crop                      design testing
                                                        requires
                                                        adjustment

  Card content      Consistent        Consistent        Comfortable touch
                    padding           padding           spacing
  -----------------------------------------------------------------------

Use CSS Grid/Flexbox and content-based sizing. Avoid fixed widths that
cause horizontal scrolling. Test at intermediate widths, not only the
three nominal device categories.

------------------------------------------------------------------------

## 7. Accessibility and interaction requirements

-   Use one meaningful page-level `h1` for the event title; use
    appropriate section and card heading levels.
-   Use semantic `section`, `article`, `ul`/`li` where appropriate, and
    anchors for navigation.
-   All links and buttons must be keyboard accessible and have a visible
    focus state.
-   Text and interactive controls must meet WCAG AA contrast
    requirements.
-   Do not rely on orange color or icons alone to convey meaning.
-   Provide useful image alternative text when an image conveys
    information. Use empty alt text for purely decorative imagery.
-   Hide decorative SVG icons from assistive technology
    (`aria-hidden="true"` or equivalent).
-   Do not create nested interactive elements.
-   Ensure responsive order does not create confusing reading order.
-   Respect reduced-motion preferences if transitions or animations are
    introduced.

------------------------------------------------------------------------

## 8. Content quality checks

The supplied screenshot contains a potential content inconsistency that
should be confirmed with the event owner before publication:

-   Heading: **"Winter FancyFaire\* 2028"**
-   Summary: says the community will "kick off 2027".
-   Event date: **January 17--19, 2027**.

This specification preserves the visible copy rather than silently
correcting it. The content editor or event owner should confirm the
intended event year and update the relevant fields consistently.

Also confirm:

-   Registration and Explore links point to the correct destinations.
-   Venue spelling and location are approved.
-   All card resource links are valid.
-   Image rights and alternative text are supplied.

------------------------------------------------------------------------

## 9. Performance and implementation guidance

-   Use Drupal Media entities for reusable images and responsive image
    styles for the hero and cards.
-   Generate appropriately sized image derivatives; do not serve the
    original large image to every viewport.
-   Set image dimensions or aspect ratio to reduce layout shift.
-   Lazy-load below-the-fold audience card images. Consider eager
    loading / high fetch priority for the primary hero image when it is
    the page's LCP element.
-   Keep component CSS scoped and avoid global selectors that leak into
    other components.
-   Prefer Drupal render arrays and cache metadata propagation. Ensure
    output varies and invalidates correctly when referenced event or
    audience content changes.
-   Avoid embedding business logic or entity queries inside Twig
    templates. Prepare component data in preprocess/build logic or a
    suitable render integration.
-   Validate external URLs and use Drupal's link field handling rather
    than manually concatenating URLs.
-   Do not add JavaScript for static layout behavior that CSS can
    provide.

------------------------------------------------------------------------

## 10. Acceptance criteria

### Event hero

-   [ ] Displays eyebrow, title, summary, event date, venue, image, and
    available CTAs.
-   [ ] Missing optional fields do not leave empty wrappers or awkward
    gaps.
-   [ ] Image crop and focal point work across breakpoints.
-   [ ] Title is the correct semantic heading level.
-   [ ] Both CTA variants have hover and keyboard focus states.
-   [ ] Hero stacks cleanly on mobile without horizontal overflow.

### Audience resource cards

-   [ ] One card component renders all four audience types without
    audience-specific templates.
-   [ ] Card image, icon, title, description, link label, and URL are
    independently configurable.
-   [ ] Card order follows the editor-configured order.
-   [ ] Grid adapts from four to two to one column at the defined
    breakpoints.
-   [ ] Cards remain readable when titles and descriptions vary in
    length.
-   [ ] No nested links or inaccessible icon-only controls are present.

### Drupal authoring and maintainability

-   [ ] Editors can update content and media without editing Twig, CSS,
    or code.
-   [ ] Icon choices and style variants are constrained to approved
    options.
-   [ ] Reusable audience content can be referenced without duplicating
    fields.
-   [ ] SDC prop schemas document required values and types.
-   [ ] Component CSS and Twig are colocated and scoped.
-   [ ] Cache tags/contexts are preserved through the Drupal render
    pipeline.
-   [ ] The year/date inconsistency is resolved by the content owner
    before production publication.

------------------------------------------------------------------------

## 11. Final architecture decisions

1.  **Use six reusable components:** `event-hero`, `event-detail`,
    `button`, `audience-card-grid`, `audience-card`, and `icon-badge`.
2.  **Keep content separate from presentation:** event and audience
    information belongs in Drupal fields/entities; SDC components render
    that data.
3.  **Use one audience card implementation:** the four cards are content
    variations, not four separate components.
4.  **Separate collection layout from item presentation:** the grid
    controls columns and spacing; each card controls its own visual
    structure.
5.  **Constrain editor choices:** editors control copy, media, links,
    ordering, and approved icon/variant choices---not arbitrary CSS or
    layout values.
6.  **Use semantic primitives where custom components add no value:**
    headings, paragraphs, lists, and links should remain native markup.
7.  **Follow the existing Drupal page-building approach:** use the
    project's established Paragraphs, Layout Builder, or fixed homepage
    content model rather than introducing competing systems.
8.  **Treat responsive behavior, accessibility, image performance, and
    cache metadata as component requirements**, not later enhancements.

This architecture provides a small, maintainable component library that
reproduces the supplied design while allowing the homepage content to
evolve independently of its presentation.
