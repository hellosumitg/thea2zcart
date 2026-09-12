# theA2Zcart merchant setup

Practical operating documentation for the theA2Zcart storefront. The theme is
product-agnostic: products are created, edited, published, or unpublished
entirely from Shopify Admin, with **no theme code changes** required. This
document is written so the store can be operated without a developer.

The canonical product-intelligence contract is
[`docs/thea2zcart-product-intelligence.md`](./thea2zcart-product-intelligence.md).
This document references it rather than duplicating it.

---

## A. Shop setup

Complete once during onboarding:

1. Shop details (name, address, currency, timezone, weights/units) — Admin → Settings → Store details.
2. Payment providers — Admin → Settings → Payments (checkout/purchase are Shopify-native; the theme does not alter them).
3. Checkout — Admin → Settings → Checkout (page established, order processing, post-purchase). These are native pages, not theme files.
4. Taxes & duties — Admin → Settings → Taxes and duties (use merchant rules; the theme makes no tax claims).
5. Physical stores / shipping — Admin → Settings → Shipping and delivery (carriers, rates, packaging). Product-specific shipping *copy* lives in the `thea2zcart` `shipping_message` metafield, never in theme code.
6. Customer accounts mode — Admin → Settings → Customers & accounts (select account type; account pages are Shopify-hosted, not theme templates).
7. Marketing preferences / consent — Admin → Settings → Marketing preferences and the consent banner provider (gates Shopify Standard Events per customer consent).
8. Gift cards — Admin → Settings → Gift cards (toggle on if offered; the native gift card page exists in the theme).
9. Blog — Admin → Settings → Preferences (enable if blog used; native blog/article surfaces exist in the theme).
10. Homepage formalization (onboarding mode off) when ready to go live.

## B. Product creation workflow

Products are managed exactly like any Shopify store. Adding a product never
requires code.

1. Admin → Products → Add product.
2. Add title.
3. Add product description (native PDP field).
4. Add media (first image becomes the card/hero image).
5. Add price (and compare-at price if applicable).
6. Add variants, option names/values if applicable.
7. Configure inventory and shipping (physical product, track, SKU/barcode).
8. Add SEO title/description if desired (native).
9. Publish (Online Store sales channel, Online Store sales channel map if using markets).
10. Add the product to the relevant collection(s) (section E).
11. Populate relevant theA2Zcart product-intelligence metafields (section C) — optional; empty fields simply render nothing.
12. Preview the PDP, the product card on collection/search/homepage, and any awareness page that points at this product.
13. Test Add to cart, cart drawer, and checkout handoff.
14. Publish.

A completely new product follows this workflow. The product-agnostic rule:
**nothing anywhere in the theme references a product by name, handle, id,
type, category, or count.**

## C. Product intelligence metafields

Create Product metafield definitions in **Admin → Settings → Custom data →
Products** using namespace `thea2zcart`. The authoritative field table
(name, type, purpose, fallback) is the product-intelligence contract document.

Every field is optional. Never populate a field with placeholder, invented,
or cross-product content. "Keep empty" always means "no component renders
anything for it."

| Field | Type (Admin definition) | Consumed by | Keep empty unless... |
| --- | --- | --- | --- |
| `promise` | Rich text | PDP intelligence-field block | you have real value-proposition copy |
| `short_promise` | Single line text | Product card intelligence (card block) | you want a compact card line |
| `problem` | Rich text | PDP problem block; education/problem-aware pages | you have honest problem copy |
| `desire` | Rich text | PDP desire block; education/problem-aware pages | you have honest desired-outcome copy |
| `mechanism` | Rich text | PDP mechanism block; education/solution-aware pages | it genuinely explains why the product works |
| `how_it_works` | Rich text | PDP how-to-use block | there are real usage steps |
| `education` | Rich text | education landing page | there is real educational content |
| `shipping_message` | Rich text | PDP shipping & guarantee block | it reflects actual shipping expectations |
| `guarantee` | Rich text | PDP shipping & guarantee block | it reflects the actual guarantee |
| `best_for` | List of single line text | Intelligence list (choose field) | you have audience labels |
| `use_cases` | List of single line text | Intelligence list (choose field) | you have use-case labels |
| `badges` | List of single line text | Product card gallery badges | you have real, non-regulatory badges |
| `benefits` | List of references: `a2z_content_item` | PDP intelligence list; product card | each entry is a real product benefit |
| `differentiators` | List of references: `a2z_content_item` | PDP intelligence list | entries are real distinctions |
| `testimonials` | List of references: `a2z_content_item` | PDP intelligence list; education/solution-aware/offer pages | entries are genuine, non-synthesized quotes |
| `results` | List of references: `a2z_content_item` | PDP intelligence list; problem-aware page | entries are merchant-substantiated outcomes |
| `faq` | List of references: `a2z_faq_item` | data contract (no active block consumer) | you build a section that consumes it |
| `comparison` | Reference: `a2z_comparison_table` | data contract (no active block consumer) | you build a section that consumes it |

`faq` and `comparison` are contracts, not built-in sections. FAQ is natively
covered by the theme's collapsible/content blocks; a comparison table would be
a future, deliberately designed section. Do not store FAQ or comparison markup
inside the other fields to force rendering.

### Content-integrity rules (apply to every product)

- Leave unsupported claims empty. Never invent reviews, testimonials, results,
  ratings, customer counts, scientific claims, urgency, or scarcity.
- Preview-only rating defaults in the review block are limited to Shopify's
  visual-preview mode and never appear on live pages.
- Never infer a product's information from its collection, category, or the
  presence of another product.
- Regulatory disclosures remain the native `shopify` disclosures; the
  theA2Zcart merchandising fields never substitute for a required disclosure.

## D. Metaobjects

Create these definitions in **Admin → Settings → Custom data** only if a
product uses the corresponding field. Keep each reference metafield constrained
to its named definition.

| Metaobject | Fields | Notes |
| --- | --- | --- |
| `a2z_content_item` | `heading` (single line, required), `body` (rich text, optional), `icon` (file reference, optional) | One entry = one list item |
| `a2z_faq_item` | `question` (single line, required), `answer` (rich text, required) | Contract for future FAQ section |
| `a2z_comparison_table` | `heading` (single line, optional), `intro` (rich text, optional), `rows` (list of references to `a2z_comparison_row`, required) | Contract; single reusable record |
| `a2z_comparison_row` | `label` (single line, required), `product_value` (rich text, optional), `alternative_value` (rich text, optional) | Contract |

Publish metaobjects to the storefront. If comparison requirements ever grow,
revise the comparison metaobject deliberately — never store table markup or
JSON in a product metafield.

## E. Collections

The current merchandising model uses four collections, selectable anywhere a
Collection setting exists — in particular the homepage **Category routing**
section.

1. Admin → Products → Collections → Create collection (Manual or Automated).
2. Add products via Admin normally (admin-selected, or by rules).
3. In Theme Editor, open the category routing section and choose each collection
   in the four collection slots. The internal settings are `home_collection`,
   `car_bike_collection`, `collection_3`, `collection_4`; their editor labels
   are merchant hints only — any collection can be placed in any slot.
4. Replacing a collection later is a Theme Editor selection; no code changes.

Collection-agnostic behavior (verified in the theme):

- Fewer than four populated collections: the section only renders slots that
  have a collection with at least one product; other slots are skipped.
- Empty collections: a slot is skipped until the collection has products.
- No collections configured: the whole section renders nothing.
- The store also works with one collection, none, or many; the four-collection
  routing is merchandising, not a technical limit.

Other collection surfaces: the homepage "Current finds" section is a
Shopify-collection selection (currently `all`), the collection template and
the collection-list template are native and render whatever Shopify provides.

## F. Navigation

Configure menus in **Admin → Online Store → Navigation**; the theme reads
menus from these settings — do not hard-code destinations.

Suggested structure (adjust to the catalog):

- **Main menu (header):** Shop → link to a collection (or collection list
  `/collections`); each current collection; About; Contact.
- **Footer menu:** Policies (Shipping, Refund, Privacy, Terms), Contact,
  and the newsletter/account areas as enabled.

Native sections (header, footer, announcement bar) allow menus and links to be
changed in the Theme Editor without code.

## G. Theme Editor setup

All configuration below happens in the Theme Editor (Online Store → Themes →
Customize). Defaults already ship; change only what the merchant must control.

- **Homepage:** hero (category-neutral default copy), "Current finds"
  product-list (set the Collection setting; max products and columns are
  merchant controls), category routing (four collection slots). The homepage is
  a brand + discovery + routing hub — not a fixed four-product page. Product
  lists use Shopify-selected collections/products; no product is hard-coded.
- **Collection / Search:** native templates; card block preset (media, review,
  title, card intelligence, price, swatches) applies to every product
  automatically.
- **PDP:** one reusable architecture for all products — media gallery, details
  group (review, title), promise, benefits, price, variant picker, buy buttons,
  shipping & guarantee, disclosures, Problem→Desire, mechanism,
  differentiators, testimonials, results, how-to-use, native description,
  recommendations. Product-specific copy comes only from native product data
  and the `thea2zcart` metafields. Block settings (heading text, which field
  each intelligence block reads, which blocks appear) are merchant-editable in
  the Theme Editor. Native variants/quantity/ATC/buy buttons/cart/checkout/
  payment/sticky-ATC events are untouched.
- **Awareness routes:** `education`, `problem-aware`, `solution-aware`, `offer`
  templates are generic and configurable. Each intelligence block has a
  Product setting and a Field setting; set the product and field in the Theme
  Editor. Their CTAs route to `/collections/all` by default and can be changed
  per button. Do not create product-specific landing pages.
- **Reviews:** the review block renders only when a native rating is present;
  its rating defaults exist only in Shopify visual-preview mode.

## H. Policies and trust foundation

Create these in **Admin → Settings → Policies** and link them via the footer
menu (section F). Policy text must reflect actual business rules entered by
the merchant — never invented promises:

1. Shipping policy (carriers, rates, processing time, delivery expectations).
2. Return / refund policy.
3. Privacy policy.
4. Terms of service.
5. Refund policy (if distinct) and payment methods shown at checkout.
6. Contact information and customer-support email (Admin → Settings → Contact).
7. Taxes/duties handling as applicable.

Product-specific shipping and guarantee *copy* can also be added via the
`shipping_message` / `guarantee` metafields (section C) when it differs per
product.

## I. Analytics

The theme already emits Shopify Standard Events through Shopify's storefront
event pipeline (page viewed, product viewed, collection/list viewed, search,
product selected, add-to-cart, cart view, checkout, purchase). **Do not alter
the theme analytics layer** and do not add a `dataLayer` or duplicate events.

Admin-side configuration:

1. **Consent:** choose one consent mechanism (Shopify's built-in consent banner
   or a consent app) so Standard Events are gated per customer consent. No
   theme code is involved.
2. **Shopify Customer Events:** in Admin, ensure customer-event sharing /
   customer privacy thresholds are set per region requirements before sending
   purchase data to marketing apps.
3. **GA4 (if required):** add Google Analytics 4 through Sales channels /
   Google (Web Pixel app) — it consumes the Shopify purchase/checkout events;
   configure conversions on purchase.
4. **Meta Pixel / Conversions API (if applicable):** set up via the Meta sales
   channel in Admin and map its Pixel; the Conversions API is configured in
   that channel, not in the theme.
5. After going live, verify purchase/test events in GA4 and the Meta Events
   Manager before launch is considered complete.

## J. Live QA

Browser/preview QA must run on a store-connected preview before launch.

**Breakpoints:** large desktop (≥1440), desktop (1024), tablet (768), mobile
(375), small mobile (320).

**Surfaces to test on each primary breakpoint:**

Homepage; collection page; collection list (`/collections`); search; PDP;
variant selection; Add to cart; quick add; sticky add-to-cart; cart page;
cart drawer; mini-cart/icon states; cart→checkout handoff; 404; contact page;
gift card page (if enabled); the four awareness pages; blog index (if blog
enabled); article page; generic page; password page (pre-launch).

**Product scenarios:**

- Add a brand-new product in Admin and confirm the PDP, cards, collection,
  search, and home features render with and without theA2Zcart metafields.
- Test with zero products, one product, four products, and many products —
  no code change should ever be needed; the theme renders whatever Shopify
  provides.
- Test an unpublished product, a product with only a title, and a product with
  full intelligence data.

## K. Launch checklist

- [ ] Shop details, payments, shipping, taxes configured and matching policy text.
- [ ] Customer accounts mode chosen; contact/support email live.
- [ ] Consent banner / marketing preferences configured.
- [ ] Product metafield definitions (`thea2zcart`) created; metaobject definitions created where used.
- [ ] Every live product has correct title/description/media/price/inventory.
- [ ] Required products have real intelligence metafields; none contain placeholder or invented content.
- [ ] Collections created, products assigned; category routing slots set in Theme Editor; homepage list collection chosen.
- [ ] Navigation menus (main + footer) built; policy pages created and linked.
- [ ] Awareness pages configured (product + field per block) and reachable from navigation/links.
- [ ] Gift card and blog toggles set as intended.
- [ ] Analytics channels (GA4, Meta) and consent configured and test-purchase verified.
- [ ] Live QA matrix (section J) passed on desktop and mobile.
- [ ] SEO basics: homepage/collection/product titles and descriptions; single H1 per page; images have alt text.
- [ ] Password page still on (or store marked ready) per launch plan.

## L. New-product / replacement workflow

To add, replace, or rotate a product:

1. Admin → Products → (Add product or edit an existing one).
2. Complete title, description, media, price, variants, inventory, shipping.
3. Publish to the Online Store.
4. Add it to the intended collection(s).
5. Populate its theA2Zcart metafields (real content only).
6. If replacing an old hero product, either edit the existing product (its
   intelligence updates automatically) or point the relevant Theme Editor
   selections — collections and any product-linked blocks — at the new product.
7. Preview the PDP, cards, and any awareness pages before publishing.
8. Unpublish/delete the retired product from Admin when ready.

Adding or replacing products requires **no theme code changes**. If a
product-specific need ever appears that cannot be met by the reusable
architecture, that is the exact moment to design a deliberate, generic section
— not a per-product template.