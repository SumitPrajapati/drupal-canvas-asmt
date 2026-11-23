# Component Specifications: Event Homepage (Drupal CMS Canvas)

| | |
|---|---|
| **Version** | 2.0 (reviewed and refined) |
| **Status** | Draft for approval. Code must be generated only after sign-off (section 15). |
| **Supersedes** | v1, the first AI-generated draft |
| **Inputs** | `home-page.png` (reference comp), supplied card photos and icons, `task.md` |
| **Target** | Drupal CMS **Canvas**, built with Single Directory Components (SDC): Twig + CSS + `component.yml` |
| **Hard constraint** | No JavaScript framework (no React, Next.js, Preact). No JavaScript at all unless a requirement cannot be met with HTML and CSS. |

---

## 1. Purpose and scope

Define the reusable component set for the two sections visible in the comp:

1. **Event hero**: eyebrow, title, description, date, venue, two calls to action, and a food photo.
2. **Audience resources**: four cards (Maker, Buyer, Distributor, Service Provider).
3. **Audience card**: slots four cards (Maker, Buyer, Distributor, Service Provider).

Out of scope: site header and navigation, footer, registration logic, search, and any content model outside Canvas (content types, Paragraphs, Layout Builder). Authors build the page in the Canvas editor, so **component props and slots are the data contract**.

---

## 2. Process and review log

This spec follows the required spec-first workflow:

1. **Analyse** the comp with AI and produce a draft spec (v1).
2. **Review and refine** the draft, making architecture decisions (this document, v2).
3. **Approve** the spec (section 15).
4. **Generate code** with the coding agent using **only this spec and `home-page.png`** as input.
5. **Verify** the result against the acceptance criteria (section 13).

### Changes from v1, and why

| # | v1 said | v2 decision | Reason |
|---|---|---|---|
| 1 | Authoring through content types, Paragraphs or Layout Builder | **Canvas props and slots only** | The task targets Canvas. A content model is out of scope and would not match the submitted components. |
| 2 | Six components, including `icon-badge` | **Five components**. `icon-badge` is merged into `audience_card`. | The badge is used in one place and has no editorial meaning of its own. A separate component adds overhead without reuse. |
| 3 | Hero date, venue and CTAs as fixed props | **Slots** that accept `event_detail` and `button` components | Editors can add, remove and reorder CTAs and details. Both are reusable on other pages. |
| 4 | `audience-card-grid` with a `cards` collection prop and `columns_desktop` | **`audience_resources` section with a `cards` slot.** Column count is not editable. | Canvas composes through slots, and arrays of objects are not a supported prop shape. Fixed responsive columns keep layouts consistent. |
| 5 | "Do not make the whole card clickable" | **One link per card, with its clickable area stretched over the card** (single tab stop) | It matches common card behaviour, and there are no nested interactive elements. |
| 6 | Card icon chosen from an enum | **Icon as a Canvas image prop** (supplied icon assets) | The brief supplies the four icon files, and editors can swap them for new audience types. |
| 7 | Hero icons unspecified | **`event_detail` icon is an enum of two inline SVGs** (`calendar`, `location`) | No icon files were supplied. Two fixed SVGs are lighter and need no uploads. |
| 8 | "Do not infer colours from a screenshot" | **Colours sampled from the comp, recorded as tokens, and flagged for confirmation** | A spec needs concrete starting values. They can be changed in one place. |
| 9 | Hero image eager or lazy left open | **Hero image eager and high priority. Card images lazy.** | The hero image is the likely LCP element. |
| 10 | No instructions for the coding agent | **Implementation contract (section 14)** | The spec must stand alone as the agent's only input. |

---

## 3. Design analysis and traceability

| Comp element | Component | Editable through |
|---|---|---|
| Hero section (peach background, text left, photo right) | `event_hero` | Props and slots below |
| "PEOPLE. PRODUCTS. A BRIGHTER FOOD FUTURE." | `event_hero` | `eyebrow` |
| "Winter FancyFaire\* 2028" | `event_hero` | `title`, `heading_level` |
| Intro paragraph | `event_hero` | `description` |
| Calendar icon + date | `event_detail` in the `details` slot | `icon`, `text` |
| Pin icon + venue and city | `event_detail` in the `details` slot | `icon`, `text`, `text_secondary` |
| "Register to Attend" (outline) | `button` in the `actions` slot, `secondary` | `label`, `url`, `variant` |
| "Explore the Event" (filled orange) | `button` in the `actions` slot, `primary` | `label`, `url`, `variant` |
| Food photo with fade into the background | `event_hero` | `image` |
| Row of four cards | `audience_resources` | `heading` and the `cards` slot |
| Each card (photo, icon disc, title, text, link) | `audience_card` | All props below |

---

## 4. Architecture decisions

| ID | Decision | Alternatives considered | Rationale and trade-off |
|---|---|---|---|
| AD-1 | Five SDC components: `event_hero`, `event_detail`, `button`, `audience_resources`, `audience_card` | One monolithic hero and one monolithic cards block | Smaller parts are reusable. The cost is more components for authors to assemble, so a default composition is provided (section 6). |
| AD-2 | Compose with **slots** (hero details, hero actions, section cards) | Fixed props per item | Slots give editors control over count and order. Fixed props would need a code change for a third CTA. |
| AD-3 | **One `audience_card`** for all four audiences | Four templates, one per audience | The four cards differ only in content. |
| AD-4 | Section and item are separate: `audience_resources` owns layout, `audience_card` owns card markup | One component with repeated fields | The grid works for any child, and a card works outside the grid. |
| AD-5 | **`button` is a primitive** with two variants | Button styles inside the hero only | It is reused by the hero and any future section. Editors cannot pick custom colours. |
| AD-6 | **Constrained editing**: copy, media, links, order and enumerated choices only | Free CSS classes, colour pickers, spacing controls | Protects design consistency and accessibility. |
| AD-7 | **Styling with plain CSS and BEM class names**, with design tokens as CSS custom properties | A utility CSS framework, or CSS-in-JS | There is no build-step requirement, styles are scoped by naming, and tokens can be overridden by the theme. |
| AD-8 | **Zero JavaScript** | A JS library for layout or interaction | Everything in the comp is achievable with HTML and CSS. |
| AD-9 | Heading levels are props (`heading_level`) | Hard-coded levels | The component can sit under different parents without breaking the page outline. |
| AD-10 | Card icon is an **image prop**. Hero detail icon is an **enum of inline SVGs** | One approach for both | Matches the supplied assets. Cards need swappable icons, but the hero has exactly two fixed icons. |

---

## 5. Component specifications

Conventions for every component:

- Machine names use `snake_case`. Prop names use `snake_case`.
- "Canvas image" means the Canvas image prop shape (`src`, `alt`, `width`, `height`).
- "URL" means `type: string`, `format: uri-reference`.
- Every prop has a `title` for the editor sidebar, and an example value.
- Optional output is **not rendered when empty**, with no empty wrappers.

### 5.1 `event_hero`

**Type:** Section component. **Purpose:** Present the event identity, summary, logistics, calls to action and photo.

| Prop or slot | Type | Required | Default | Editor help |
|---|---|---|---|---|
| `eyebrow` | string | No | none | Short line above the title. Rendered uppercase by CSS. |
| `title` | string | **Yes** | none | Event name. |
| `heading_level` | enum `h1`, `h2` | No | `h1` | Use `h1` when this is the page's main heading. |
| `description` | string | No | none | Plain text, about 300 characters. |
| `image` | Canvas image | No | none | Hero photo. Alt text is empty if decorative. |
| slot `details` | slot | No | none | Accepts `event_detail` components. |
| slot `actions` | slot | No | none | Accepts `button` components. |

**Markup:** `<section>`, then an inner container, then a content column containing `eyebrow` (`p`), the heading, `description` (`p`), a details wrapper and an actions wrapper. A separate `div` holds the photo.

**Behaviour:**

- Desktop (62rem and up): the content is on the left, with a maximum width of about 32rem. The photo is anchored right at about 56% of the section width and **fades into the background from its left edge** (CSS mask).
- Below 62rem: the content stacks above the photo. The photo is 16:9 and fades in from the top.
- The details wrapper renders only if the `details` slot has content. Likewise for `actions`.
- Section height comes from its content, with a minimum of about 36.5rem on desktop. There are no fixed pixel heights.
- The hero `img` is `loading="eager"` with `fetchpriority="high"`.


### 5.2 `audience_resources`

**Type:** Section component. **Purpose:** Responsive container for audience cards.

| Prop or slot | Type | Required | Default | Editor help |
|---|---|---|---|---|
| `heading` | string | No | none | Optional heading. Not shown in the comp. |
| `heading_level` | enum `h2`, `h3` | No | `h2` | Only used when `heading` is set. |
| slot `cards` | slot | **Yes** | none | Accepts `audience_card` components. Add, remove and reorder. |

**Behaviour:** One column below 40rem, two from 40rem, four from 64rem. Gap is 1.5rem. Container max width is 84rem with 1.5rem side padding. Cards in a row are **equal height**. The component also works if Canvas wraps each slot item in its own element.

### 5.3 `audience_card`

**Type:** Item component. **Purpose:** Promote one audience's resource page.

| Prop | Type | Required | Default | Editor help |
|---|---|---|---|---|
| `image` | Canvas image | **Yes** | none | Card photo. Cropped to 16:9. |
| `icon` | Canvas image | No | none | Square icon (128px or larger, transparent background preferred). Alt is always empty. |
| `title` | string | **Yes** | none | e.g. "I'm a Maker". |
| `heading_level` | enum `h2`, `h3`, `h4` | No | `h3` | Match the surrounding outline. |
| `description` | string | No | none | One or two short sentences. |
| `link_text` | string | **Yes** | none | e.g. "See resources for makers". |
| `link_url` | URL | **Yes** | none | Resource page. |

**Structure:**

1. `article` wrapper with a 1px neutral border and 16px radius.
2. Media area: photo at 16:9 with `object-fit: cover`, clipped to the top corners. An **icon disc** (64px white circle, icon about 36px) is positioned on the photo's bottom edge, left-aligned with 20px inset, and overlaps the content area by half.
3. Body: title, description, and the link pinned to the bottom.

**Link behaviour:** The link is the only interactive element. Its pseudo-element covers the whole card, so the card is clickable with a single tab stop. Hover shows a border highlight in the accent colour and an underlined link. The focus state is a visible outline on the card (`:has(a:focus-visible)`).

**Empty states:** No photo leaves a neutral grey 16:9 area. No icon renders no disc. No description renders no paragraph. The card still has equal height.

---

## 6. Page composition in Canvas

Default composition for the home page:

1. `event_hero`
   - `details` slot: `event_detail` (calendar, "January 17–19, 2027"), then `event_detail` (location, "Moscone Center", "San Francisco, CA").
   - `actions` slot: `button` (secondary, "Register to Attend"), then `button` (primary, "Explore the Event").
2. `audience_resources`
   - `cards` props: four `audience_card` components in the order Maker, Buyer, Distributor, Service Provider.
3. `audience_card`
   - `cards` slot: four `audience_card` components in the order Maker, Buyer, Distributor, Service Provider.
If the Canvas version supports saved patterns or sections, save the hero and the audience section as patterns so authors start from the full composition rather than empty slots.

---

## 7. Design tokens

Values were **sampled or measured from the 1920 × 1067 comp**. Values marked *estimate* were not sampled. All must be confirmed against the design system.

| Token (CSS custom property) | Value | Used for |
|---|---|---|
| `--color-hero-surface` | `#fff3e1` (sampled) | Hero background |
| `--color-accent` | `#ffad00` (sampled) | Primary button, detail icons, card hover border |
| `--color-text` | `#000000` (sampled) | Headings, body, buttons |
| `--color-text-muted` | `#5f5f5f` (estimate) | Card description |
| `--color-border` | `#d9d9d9` (estimate) | Card border |
| `--color-surface` | `#ffffff` | Cards, icon disc |
| `--font-display` | serif stack, e.g. DM Serif Display, Playfair Display, Georgia, serif | Hero title. The brand font is unknown and must be confirmed. |
| `--font-body` | inherit from theme | Everything else |
| `--radius-card` | `1rem` | Cards |
| `--radius-pill` | `999px` | Buttons |

Approximate comp measurements at 1920 px: content container 1344 px wide (288 px gutters), hero about 584 px tall, hero title about 56 px, cards about 316 × 355 px with 24 px gaps, icon disc 64 px.

Each component declares tokens with fallbacks so it works standalone, and a theme can override them globally.

---

## 8. Responsive behaviour

| Area | 64rem and up | 40rem to 63.99rem | Below 40rem |
|---|---|---|---|
| Hero layout | Two columns (62rem and up), photo faded in from the right | Content, then photo | Content, then photo |
| Hero title | Fluid, up to about 3.5rem | Fluid | Fluid, minimum about 2.5rem |
| Details row | Inline with wrap | Inline with wrap | Wraps or stacks |
| Buttons | Inline with wrap | Inline with wrap | Wrap to full width as needed |
| Audience grid | 4 columns | 2 columns | 1 column |

No horizontal scrolling at any width. Test at intermediate widths as well.

---

## 9. Accessibility requirements

- One `h1` per page. `event_hero` defaults to `h1`, and `heading_level` allows `h2`. Card titles default to `h3`.
- Links are real `<a>` elements with descriptive text. No nested interactive elements and no clickable `div`s.
- Visible keyboard focus on buttons and cards.
- Text meets WCAG AA contrast. Confirm the orange button with black text and the muted grey on white once final colours are approved.
- Decorative SVGs use `aria-hidden="true"`. Card icons use `alt=""`.
- Informative images need alt text. Decorative images use empty alt.
- Reading order matches visual order on mobile.
- Transitions are limited to border colour and wrapped in `prefers-reduced-motion: no-preference`.

---

## 10. Performance

- Hero image: eager loading, high fetch priority, explicit `width` and `height`.
- Card images: `loading="lazy"`, explicit dimensions, consistent 16:9 crop.
- Serve resized images (about 900 px wide for cards). Prefer WebP.
- No JavaScript, no web fonts bundled by the components, and no external requests.
- Component CSS is scoped by BEM prefix (`.event-hero`, `.event-detail`, `.button`, `.audience-resources`, `.audience-card`).

---

## 11. Editor flexibility and permissions

| Editors can | Editors cannot |
|---|---|
| Change all copy, links and images | Enter custom CSS, colours, fonts or spacing |
| Add, remove and reorder cards, details and buttons | Change grid column counts |
| Choose button variant, icon (hero) and heading level | Upload arbitrary icons for the hero details |
| Swap card icons | Change card geometry, border or radius |

Authenticated users who have the Canvas page-editing and Media Library permissions can edit all of the above in the Canvas editor. The role and exact permission names vary by Canvas version and are configured at `/admin/people/permissions`.

---

## 12. Content issues and open questions

1. **Date inconsistency in the comp.** The title says "Winter FancyFaire\* 2028", the intro says "kick off 2027", and the date reads "January 17–19, 2027". The spec **preserves the visible copy**. The event owner should confirm the year.
2. **Brand fonts and exact colours** are not supplied. Tokens in section 7 are placeholders to confirm.
3. **Link destinations** for the buttons and the four cards are not defined in the comp. Example paths are used.
4. **Alt text** for the hero and card photos is to be supplied by editors.
5. **Whole-card click target** (AD-5) is a design assumption. Confirm with the design owner.
6. **Hero photo source.** Only the screenshot is available, so a cropped placeholder is used until the original image is supplied.

---

## 13. Acceptance criteria

### Event hero
- [ ] Eyebrow, title, description, details, buttons and photo render when provided.
- [ ] Each optional element is omitted cleanly when empty, with no empty wrappers or gaps.
- [ ] `heading_level` changes the title tag, and defaults to `h1`.
- [ ] Details and buttons can be added, removed and reordered in the Canvas editor.
- [ ] The photo fades in from the right on wide screens and sits below the text on narrow screens.
- [ ] Hero image loads eagerly with high priority.
- [ ] Buttons show hover and keyboard focus states.

### Audience cards
- [ ] One `audience_card` component renders all four cards, with no audience-specific templates.
- [ ] All card fields are editable in the Canvas sidebar.
- [ ] The grid shows 4, 2 and 1 columns at the defined breakpoints.
- [ ] Cards in a row have equal height, with links aligned at the bottom.
- [ ] The whole card is clickable through one link, and there are no nested links.
- [ ] Cards remain readable with long or short titles and descriptions.

### Technical
- [ ] No JavaScript and no JavaScript framework anywhere in the submission.
- [ ] Each component folder has `*.component.yml`, `*.twig` and `*.css`.
- [ ] Every prop has a `title` and an example, and required props are declared.
- [ ] No horizontal scroll from 320 px upward.
- [ ] Content issues in section 12 are listed in the handover notes.

---

## 14. Implementation contract (for the coding agent)

**Inputs allowed:** this document and `home-page.png`, plus the supplied photos and icons. Do not add features, props or components that are not in this spec. If something is unclear, list it as a question instead of guessing.

**Deliverables:**

```text
components/
  event_hero/           event_hero.component.yml, event_hero.twig, event_hero.css
  event_detail/         event_detail.component.yml, event_detail.twig, event_detail.css
  button/               button.component.yml, button.twig, button.css
  audience_resources/   audience_resources.component.yml, audience_resources.twig, audience_resources.css
  audience_card/        audience_card.component.yml, audience_card.twig, audience_card.css
assets/                 resized card photos and icons
README.md               install steps, composition steps, token overrides
```

**Rules:**

- Twig, CSS and YAML only. No JavaScript, no frameworks, no build step.
- Use Canvas-compatible props: strings, enums, booleans, URL strings (`format: uri-reference`), and images via `$ref: json-schema-definitions://canvas.module/image`. Use slots for composition.
- Use exactly the prop names, types, defaults and enums in section 5.
- Use BEM class names prefixed with the component name.
- Declare design tokens as CSS custom properties with fallbacks (section 7).
- Include a `title` and `examples` for every prop.
- Parse-check the YAML files, and state clearly what was not tested.

---

## 15. Approval

Complete this before any code is generated.

- [ ] Component boundaries (AD-1 to AD-5) reviewed and approved.
- [ ] Editor flexibility and constraints (section 11) reviewed and approved.
- [ ] Tokens (section 7) and open questions (section 12) reviewed.
- [ ] Reviewer name and date: ______________________
