# Audience Resources – Drupal Canvas components

Two code components that rebuild the "I'm a Maker / Buyer / Distributor / Service Provider" section from the Winter FancyFaire home page.

| Component | Purpose | Editable by authors |
|---|---|---|
| `audience-card` | One card: photo, icon, title, description, link | Photo, Icon, Title, Description, Link text, Link URL |
| `audience-resources` | Section wrapper (responsive grid, 1 / 2 / 4 columns) | Optional Heading, plus a `cards` slot to add, remove, and reorder cards |

## Install

1. Copy `components/audience-card` and `components/audience-resources` into your Canvas project's components folder.
2. Run `npx canvas validate`, then push the components to your site with the Canvas CLI (see the Canvas CLI docs for the exact push/upload command in your version).
   - Alternative: create each component in the Canvas in-browser code editor and paste in `index.jsx` and the props from `component.yml`.
3. Upload everything in `assets/` to the Media Library (image media type).
4. Make sure both components are enabled (`status: true` is already set).

## Build the section on a page

Add **Audience Resources** to the page, then drop four **Audience Card** components into its `Cards` slot:

| Card | Photo | Icon | Title | Description | Link text |
|---|---|---|---|---|---|
| 1 | card-maker.webp | icon-maker.png | I’m a Maker | Grow your brand and reach new markets. | See resources for makers |
| 2 | card-buyer.webp | icon-buyer.png | I’m a Buyer | Discover new products and suppliers. | See resources for buyers |
| 3 | card-distributor.webp | icon-distributor.png | I’m a Distributor | Find tools to grow your business. | See resources for distributors |
| 4 | card-provider.webp | icon-provider.png | I’m a Service Provider | Connect with the specialty food community. | See resources for providers |

Set each Link URL to your real resource pages. Add photo alt text in the Media Library or in the prop. Icons are decorative and use empty alt text.

## Editing by authenticated users

Every field above is a Canvas prop, so editors change it in the Canvas editor sidebar with no code. To allow it:

1. Go to `/admin/people/permissions`.
2. Give the role your authors use (or Authenticated user, if every logged-in user should edit) the Canvas permissions for editing pages and using the Media Library. Exact permission names vary by Canvas version.

Tip: save the finished section as a Canvas pattern, if your version offers it, so authors can reuse the four-card layout on other pages.

## Notes

- Styling uses Tailwind utility classes (Canvas provides Tailwind 4) and inherits your theme's font. The design uses a Poppins-style sans.
- Accent colour is Tailwind `amber-500`. For the exact brand orange, swap it for your theme token.
- The whole card is clickable through the link, and keyboard focus shows an outline.
- Cards have no JS state, so they render server-side with the page.
