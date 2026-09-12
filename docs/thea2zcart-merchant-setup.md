# theA2Zcart merchant operating guide

Practical, complete operating documentation for the theA2Zcart storefront. It
is written so the store can be operated entirely from **Shopify Admin** without
a developer and without touching theme code.

The canonical product-intelligence contract is
[`docs/thea2zcart-product-intelligence.md`](./thea2zcart-product-intelligence.md).
This guide references it instead of duplicating it.

The theme is **product-agnostic and modular**. The storefront intentionally
merchandises a small catalog (today, four active products), but that is only a
merchandising decision. The theme supports 0, 1, 2, 3, 4, 8, 20+ products
without code changes. Products are added, removed, replaced, unpublished,
republished, and edited through **Admin → Products → Add product**. Nothing
that follows creates a technical limit.

---

## 1. Merchant vs. code responsibility

| Area | Owner | How |
| --- | --- | --- |
| Products, prices, variants, inventory, media | Merchant | Admin → Products |
| Product collections | Merchant | Admin → Collections |
| Navigation menus | Merchant | Admin → Content → Menus (Online Store → Navigation) |
| Policy pages | Merchant | Admin → Settings → Policies (real business terms only) |
| Generic/static pages | Merchant | Admin → Content → Pages |
| Blog (optional) | Merchant | Admin → Content → Blog posts |
| Product metafields (`thea2zcart`) | Merchant | Admin → Settings → Custom data → Products |
| Metaobjects | Merchant | Admin → Settings → Custom data (create only when used) |
| Theme Editor configuration | Merchant | Online Store → Themes → Customize |
| Shipping, taxes, payments | Merchant | Admin → Settings |
| Customer accounts mode | Merchant | Admin → Settings → Customers & accounts (Shopify-hosted pages; no `customers/*` templates needed) |
| Analytics pixels (GA4, Meta) + consent | Merchant | Admin → Settings → Marketing / Sales channels |
| Domain, password/launch state | Merchant | Admin |
| Theme architecture, reusable components, bugs, unsupported needs, measured improvements | Code (only when genuinely necessary) | Strictly needed theme work only |

Normal product creation or replacement **must never require Codex**.

## 2. Shop setup

1. Store details (name, address, currency, timezone, weights/units) — Admin → Settings → Store details.
2. Payments — Admin → Settings → Payments (checkout/purchase are Shopify-native; the theme never alters them).
3. Checkout — Admin → Settings → Checkout.
4. Taxes & duties — Admin → Settings → Taxes and duties (merchant rules; theme makes no tax claims).
5. Shipping — Admin → Settings → Shipping and delivery (carriers, rates, packaging, locations). Product-specific shipping *copy* lives in the `shipping_message` metafield.
6. Customer accounts mode — Admin → Settings → Customers & accounts.
7. Marketing preferences / consent — Admin → Settings → Marketing preferences + consent banner provider (gates Shopify Standard Events per consent).
8. Gift cards — Admin → Settings → Gift cards (native gift card page exists in the theme).
9. Blog — Admin → Settings → Preferences (native blog/article surfaces exist).
10. Domain — Admin → Settings → Domains (confirm `thea2zcart.com` primary, SSL active).
11. Contact — Admin → Settings → Contact (customer-support email live before launch).
12. Homepage formalization (onboarding mode off) when ready to go live.

## 3. Product creation workflow

**Any** future product. **No theme code change is ever required.**

1. Admin → Products → Add product
2. Core information: title, product type, vendor (native fields)
3. Description (native PDP rich text)
4. Media: first image becomes the card/hero image; add additional images/video as real media
5. Pricing: price; compare-at price only when a real discount exists
6. Variants: one option ("Title") if single variant; option name/value pairs if multi-variant; variant price, compare-at as applicable
7. Inventory: track or don't track; SKU/barcode as applicable (per product policy)
8. Shipping: weight/dimensions/HS code as applicable
9. SEO: page title, meta description (native)
10. Publish to the Online Store sales channel
11. Assign to the appropriate collection(s) (section 6)
12. Populate optional theA2Zcart product-intelligence fields (section 4) — real content only; empty fields render nothing
13. Preview the storefront PDP
14. Verify the product card (homepage/collection/search)
15. Verify the collection page rendering
16. Verify search finds the product
17. Verify homepage merchandising if the product is assigned to the homepage list/category routing
18. Verify recommendation surfaces (product page "related" recommendations)
19. Test Add to cart, cart drawer, cart page, checkout handoff
20. Publish state confirmed live

## 4. Product intelligence — Admin foundation

Define Product metafields in **Admin → Settings → Custom data → Products** in
namespace **`thea2zcart`** (key = field name below). Authoritative contract:
`docs/thea2zcart-product-intelligence.md`.

Every field is optional. Blank always means "no component renders anything."

| Key | Type | Purpose | Consumed on | Never put here |
| --- | --- | --- | --- | --- |
| `promise` | Rich text | Core value proposition | PDP promise block; awareness pages | invented claims, hype |
| `short_promise` | Single line text | Compact card line | product card intelligence | truncated `promise`, invented taglines |
| `problem` | Rich text | The real customer problem | PDP problem block; education/problem-aware | invented pain points |
| `desire` | Rich text | Desired outcome | PDP desire block; education/problem-aware | invented outcomes/aspirations |
| `mechanism` | Rich text | Why it works | PDP mechanism block; education/solution-aware | speculative or scientific-sounding claims |
| `how_it_works` | Rich text | Real usage steps | PDP how-to-use block | fake steps |
| `education` | Rich text | Educational content | education page | unsupported claims |
| `shipping_message` | Rich text | Product-specific delivery note | PDP shipping & guarantee block | invented delivery times (match policy) |
| `guarantee` | Rich text | Real guarantee/risk-reduction | PDP shipping & guarantee block | invented guarantees (match policy) |
| `best_for` | List of single line | Audience labels | intelligence list (pick field) | invented audiences |
| `use_cases` | List of single line | Use-case labels | intelligence list (pick field) | invented scenarios |
| `badges` | List of single line | Non-regulatory merchandising badges | product card gallery | regulatory/safety claims (use native disclosures) |
| `benefits` | List: `a2z_content_item` | Benefit statements | PDP intelligence list; product card | fabricated benefits |
| `differentiators` | List: `a2z_content_item` | Real distinctions | PDP intelligence list | invented distinctions |
| `testimonials` | List: `a2z_content_item` | Genuine customer quotes | PDP intelligence list; education/solution-aware/offer | synthesized/fake quotes, UGC |
| `results` | List: `a2z_content_item` | Substantiated outcomes | PDP intelligence list; problem-aware | made-up stats/counts |
| `faq` | List: `a2z_faq_item` | Product FAQ (contract) | no active block consumer | FAQ markup shoved into other fields |
| `comparison` | Reference: `a2z_comparison_table` | Structured comparison (contract) | no active block consumer | table markup stored in the product |

`faq` and `comparison` are **contracts**, not built-in sections — do **not**
create a custom renderer for them now. FAQ is natively coverable with the
theme's collapsible/content blocks if the merchant chooses. Create entries only
when real FAQ/comparison needs exist.

### Content-integrity rules

- Never invent: reviews, testimonials, results, ratings, customer counts,
  scientific claims, urgency, scarcity, guarantees, shipping promises, or
  product categories.
- Never reuse another product's intelligence content or a global default; every
  field is product-specific.
- Never infer product information from its collection/category.
- Preview-only rating defaults in the review block exist only in Shopify
  visual-preview mode and never appear live.
- Regulatory/safety disclosures remain the native Shopify disclosure fields;
  merchandising fields never replace them.

## 5. Metaobject operating model

Create metaobject definitions in Admin → Settings → Custom data (menu: "Create
definition" → Content type "Metaobject") **only when a product uses the field**.
Publish each metaobject to the storefront. Constrain each reference metafield
to its named definition.

| Metaobject | Fields | Used for |
| --- | --- | --- |
| `a2z_content_item` | `heading` (single line, required), `body` (rich text, optional), `icon` (file reference, optional) | Shared list entries for statements, distinctions, quotes, outcomes |
| `a2z_faq_item` | `question` (single line, required), `answer` (rich text, required) | FAQ entries (future consumer) |
| `a2z_comparison_table` | `heading` (single line, optional), `intro` (rich text, optional), `rows` (list of references to `a2z_comparison_row`, required) | A single reusable comparison record |
| `a2z_comparison_row` | `label` (single line, required), `product_value` (rich text, optional), `alternative_value` (rich text, optional) | One comparison row |

Operating rules:

- One `a2z_content_item` per product per benefit/distinction/quote/result. Do
  not share metaobjects across products — that causes cross-product
  contamination when one product's entry is updated.
- To update one product without affecting another: edit **that product's own
  metaobjects** (list references are per-product). Never point two products at
  the same benefit/testimonial entry.
- Leave the field with **zero entries** rather than creating empty shell
  metaobjects. Reference lists read as blank when nothing is selected. Do not
  create placeholder "coming soon" entries.
- If a definition becomes unused, unpublish its metaobjects and optionally
  delete the definition; the theme renders nothing when fields are empty.
- Never store FAQ/comparison/table markup or JSON inside a product metafield.

## 6. Collections — merchandising architecture

Four active products may be spread across any number of collections however the
merchant chooses. The **category routing** section on the homepage is the
current collection merchandising hub.

Create/use collections:

1. Admin → Products → Collections → Create collection (Manual or Automated).
2. Name the collection (display name shown to visitors + the URL handle).
3. Add a description and a collection image (used by the routing cards).
4. Assign products via Admin (hand-picked or rules).
5. In the Theme Editor, homepage **Category routing** section, choose each
   collection in the four slots. Internal settings: `home_collection`,
   `car_bike_collection`, `collection_3`, `collection_4`; the editor labels are
   merchant-visible hints only — any collection may go in any slot.
6. In the Theme Editor, homepage "Current finds" product-list: choose the
   collection it reads (default `all`).

Rules:

- Fewer than four populated collections: only populated slots (collection with
  ≥1 product) render.
- Empty collections: a slot is skipped until it has products.
- No collections configured: the routing section renders nothing.
- Replacing a collection later = Theme Editor selection. No code changes.
- Recommended homepage setup: 2×2 routing grid on desktop, 1 column on mobile.
- Homepage collection ("Current finds") and category routing both remain
  Shopify collection selectors — nothing about products is hard-coded.

## 7. Navigation

Menus are configured in **Admin → Content → Menus** (previously Online Store →
Navigation). The theme reads menus from these settings; navigation is never
hard-coded in theme code.

Suggested main menu (adjust to the live catalog):

- Home
- Shop / All products → link to `collections/all` (or collection list `/collections`)
- Curated collection links (the four current collections)
- About, Contact as created

Footer menu:

- Contact, About
- Shipping policy, Returns/Refund, Privacy policy, Terms, Refund policy
- Social links where applicable
- Account link (works with the configured customer-accounts mode)

Use dynamic store page/collection links in the menu picker (not raw URLs) so
the menu stays correct if pages are renamed.

## 8. Homepage merchandising

The homepage is the **brand + discovery + routing hub** — not a fixed
four-product page. Theme Editor (Online Store → Themes → Customize →
Homepage):

- Hero: category-neutral default copy stays as shipped. Replace only with real
  brand copy; keep **one H1**.
- "Current finds" product-list: choose collection, max products, columns (all
  merchant settings).
- Category routing: four collection slots (section 6).
- Trust/proof appears only when genuine (see content-integrity rules).

Do **not** route all paid traffic to the homepage. Route traffic by intent:

| Incoming traffic | Send to |
| --- | --- |
| Problem-aware | problem-aware page |
| Solution-aware | solution-aware page |
| Educational | education page |
| Offer/discount | offer page |
| Product-aware | product PDP |
| Generic/category search | collection / search |
| Direct/referral | homepage |
| Returning customers | product / reorder journey |

## 9. PDP operating workflow

One reusable PDP architecture serves every product. No per-product templates.
Configure blocks in **Theme Editor → Product → default product**; the same
configuration applies to new products automatically.

Above the fold (populate per product in Admin/Theme Editor):

- Media gallery (native; media from Admin)
- Review block (renders only when a genuine native rating exists)
- Product name (native)
- `promise` (PDP promise block)
- `benefits` (PDP intelligence-list block, field = benefits)
- Price (native)
- Variant picker (native; add variants in Admin)
- Buy buttons, quantity, dynamic checkout (native), sticky add-to-cart (native)
- Reassurance (`shipping_message` / `guarantee` in the shipping & guarantee block)

Below the fold — add per block, showing **only where the product genuinely has
data**: `problem`/`desire`, `mechanism`, `differentiators`, `testimonials`,
`results`, `how_it_works`, native description, related-product recommendations.

Rules:

- Do not force every block onto every product. A minimal product (title, price,
  description, media) still produces a clean PDP because every optional block
  self-hides when its field is empty.
- Native functionality is never altered: variants, quantity, ATC, buy buttons,
  cart, checkout, payment, sticky ATC, standard events.

## 10. Awareness journeys

Four generic, configurable landing templates exist and map to the intent flow:
`education`, `problem-aware`, `solution-aware`, `offer`. The product-aware
route is the native PDP. Configure each in the Theme Editor (page template),
per block: a **Product** setting (which product) and a **Field** setting (which
metafield). CTAs default to `/collections/all` and are merchant-configurable.

- Each page has a hero + intelligence blocks + product list + a clear CTA.
- All routes enter the same conversion system (same products, cart, checkout).
- Do **not** create product-specific landing pages and do **not** make any
  awareness route depend on a category.

## 11. Theme Editor quick reference

All normal storefront configuration is in **Online Store → Themes → Customize**:

- Header/footer/announcement bar: menus, social, newsletter, layout.
- Homepage: hero, product list (collection, count, columns), category routing.
- Product template: media layout, details column blocks, below-fold blocks.
- Collection/search: card layout, filters, sorting, density.
- Page templates: hero + blocks; awareness pages: intelligence blocks + CTAs.
- Colors, typography, spacing, buttons: global theme settings.

Preview every change at desktop and mobile widths before saving.

## 12. Policies, payments, shipping, tax

### Policies (real business terms — merchant must supply)

Admin → Settings → Policies. Mark each **MERCHANT MUST PROVIDE ACTUAL BUSINESS TERMS**; never invent:

- Shipping policy — carriers, rates, processing time, delivery expectations.
- Return/refund policy — window, conditions, how to start a return.
- Privacy policy.
- Terms of service.
- Cancellation policy (if applicable).
- Contact details, support email.
- Payment methods accepted (mirrors Admin → Payments).
- Taxes/duties handling (if applicable).

Do not invent delivery times, return windows, guarantees, warranty periods, or
support claims anywhere (policies or product fields must agree).

### Payments / shipping / tax checklist

- Payments: providers, capture vs. authorize, payment methods shown at checkout.
- Shipping: zones and rates, carriers, packaging, fulfillment locations.
- Taxes: merchant tax rules per region.
- Markets (if used): sales channels map; currency/presentment.
- Checkout: established page, order processing, post-purchase page — **remain
  Shopify-native; no theme code**.
- Order processing: confirmation emails, fulfillment statuses.
- Inventory: track at locations, low-stock thresholds.
- Locations: set at least one fulfillment location and reference it in shipping.

## 13. Customer accounts

Shopify-hosted customer accounts are used. **No `templates/customers/*` theme
files are required** — do not create them. Configure:

- Admin → Settings → Customers & accounts → choose account type.
- Set whether order history/address books are available to customers.
- Optionally surface the Account link in the header/footer menus.

## 14. Analytics

The theme's analytics layer is already complete and must not be modified:

`snippets/scripts.liquid`, `assets/page-view-event.js`,
`assets/view-event-elements.js`, `assets/standard-actions-override.js`,
`layout/theme.liquid` (`content_for_header`) — all protected.

It emits Shopify **Standard Events** covering the full journey:

`page_view` → `product_view` → `collection/list_view` → `search` →
`product_selection` → `add_to_cart` → `cart_view` → `checkout` → `purchase`

Admin-side setup:

1. **Consent first** (section 15).
2. Shopify Customer Events: enable/harmonize per regional customer-privacy
   thresholds before sending purchase data to marketing apps.
3. GA4 (if required): add the Google/GA4 channel in Admin (Web Pixel app);
   it consumes Shopify purchase/checkout events; configure the purchase
   conversion.
4. Meta Pixel / Conversions API (if applicable): set up via the Meta sales
   channel; map the Pixel; CAPI is configured there, not in the theme.
5. After launch, verify a live test order appears as a `purchase` event in GA4
   and the Meta Events Manager.

Do **not** add a `dataLayer`, duplicate events, or duplicate tracking scripts.

## 15. Consent

Checklist (merchant responsibility; the theme has no consent code):

- Install/choose one consent mechanism — Shopify's built-in consent banner or a
  consent app. This gates Standard Events per customer consent.
- Configure analytics consent and marketing consent independently.
- Confirm the banner shows before analytics fire, on all pages, on mobile.
- Confirm consent state routes through to GA4/Meta so no event fires where
  consent was refused.
- Confirm compliance with regional requirements (e.g., EEA/UK GDPR) — legal
  configuration is the merchant's responsibility; nothing here is legal advice.

## 16. Email / post-purchase

Launch checklist (Admin → Settings → Notifications; no theme automation):

- Order confirmation
- Shipping confirmation
- Delivery notification
- Refund/cancellation notifications
- Abandoned-checkout emails if enabled
- Post-purchase education (optional, real content only)
- Review request after delivery (optional)
- UGC request (optional, only where genuinely used)
- Replenishment/reorder reminder where relevant

Do not implement speculative automation in theme code.

## 17. SEO

- Homepage title + meta description (Admin → Online Store → Preferences).
- Product page SEO title/description per product.
- Collection SEO title/description per collection.
- Page SEO per page.
- Image alt text: fill product media alt text in Admin (theme already
  fallbacks to product title).
- Canonical URLs, sitemap, robots: native Shopify — do not duplicate.
- Indexing: ensure public products not "noindex" before launch.
- Google Search Console: verify the domain and submit the sitemap after launch.
- Structured data: native Shopify product/collection markup preserved; do not
  add custom schema; no SEO claims invented.

## 18. Performance (preserved constraints)

The repository stays at:

- zero unnecessary custom JavaScript
- responsive, lazy-loaded imagery where appropriate
- no duplicate analytics
- no unnecessary DOM duplication
- no unnecessary third-party scripts

Do not add speculative performance tooling. If something must be measured later,
measure with real Lighthouse data on a store-connected preview.

## 19. Accessibility / live QA remediation

- Heading hierarchy: one H1 per page; sequence intact (H1 → H2 → H3).
- Keyboard: full site operable with Tab; visible focus rings retained.
- Button/link labels are descriptive (Theme Editor text).
- Images have alt text (Admin media alt).
- Form labels and error messages are native and visible.
- Color contrast passes with the default palette; verify with any custom
  colors added in Theme Editor.
- Touch targets are thumb-friendly on mobile.
- Drawer/modal/navigation close behavior is native and focus returns sensibly.

## 20. Live QA matrix

Run on a store-connected preview before launch (external validation gate — not
executable in a headless environment).

Viewports: large desktop (≥1440), desktop (1024), tablet (768), mobile (375),
small mobile (320). Browsers where available: Chrome, Safari, Firefox, Edge.

Surfaces, at minimum:

Homepage, collection page, collection list (`/collections`), search, PDP,
variants, Add to cart, quick add, sticky add-to-cart, cart page, cart drawer,
mini-cart/icon, cart→checkout handoff, 404, contact page, gift card page,
the four awareness pages, blog index, article page, generic page, password page
(pre-launch).

For each test verify: visual hierarchy, layout, spacing, typography, buttons,
links, images, overflow, navigation, keyboard, focus, screen-reader semantics,
forms, errors, empty states, loading states, variant behavior, cart behavior,
checkout handoff. Record pass/fail + viewport + browser.

### Mobile-first checks

- No horizontal overflow at 320–375px.
- No clipped text; long product titles wrap cleanly.
- No tiny tap targets; buttons ≥ 44px effective.
- Sticky ATC does not obscure content; it stays reachable.
- Header/announcement bar and drawer open/close correctly.
- Product cards and collection grid stack correctly.
- PDP media gallery swipes/zooms correctly.
- Forms usable on mobile; cart drawer works; checkout CTA remains visible.
- Images do not create excessive layout shift (sizing attributes present).

### Per-product live journey

For each real product, once, before launch:

1. Product exists in Admin and is published.
2. Correct media, price, variants, inventory.
3. Assigned to the intended collections.
4. SEO filled (optional but recommended).
5. Intelligence data populated where real content exists.
6. PDP opens and renders correctly on desktop + mobile.
7. Product card renders on homepage/collection/search.
8. Search surfaces the product.
9. Variant selection updates price/ATC.
10. ATC works; cart drawer updates; cart page correct.
11. Sticky ATC works.
12. Checkout handoff opens Shopify checkout.
13. No fabricated content appears anywhere.
14. Blank optional fields produce no broken/empty sections.

## 21. Product count stress (conceptual verification)

Repository state supports all counts with **no code change** because nothing
counts, names, or limits products:

- **0 products:** homepage product list + category routing self-hide/empty;
  collections/search show native empty states; CTAs remain valid.
- **1–4 products:** standard rendering; routing shows populated slots.
- **8 / 20+ products:** product-list `max products` settings and collection grid
  are merchant display controls, not catalog caps; pagination is native.

No maximum-product setting exists or may be added.

## 22. Desktop/mobile independence

Theme Editor exposes independent desktop/mobile controls for sections and
blocks (e.g., columns, card size, spacing, mobile media layout). Use them to
tune per breakpoint. The theme does **not** duplicate DOM or create separate
code paths per device — it uses Horizon's responsive architecture.

## 23. Launch checklist

**PRE-LAUNCH**

- [ ] Store details, currency, timezone
- [ ] Payments configured
- [ ] Shipping zones/rates + carrier/locations
- [ ] Taxes and duties
- [ ] Customer accounts mode chosen
- [ ] Policies written from real business terms (shipping, returns, privacy, terms, refund)
- [ ] Contact information + support email live
- [ ] Domain active and primary (thea2zcart.com)
- [ ] Navigation menus built (main + footer)
- [ ] Collections created and products assigned
- [ ] Products complete (media, price, variants, inventory, shipping, SEO)
- [ ] Product intelligence populated where real; no placeholders
- [ ] Homepage configured (hero, Current finds, category routing slots)
- [ ] PDP blocks confirmed for all products
- [ ] Awareness pages configured (product + field per block) + CTAs
- [ ] Search verified
- [ ] Cart + cart drawer + checkout handoff verified
- [ ] Analytics channels (GA4, Meta) added; purchase event verified on a test order
- [ ] Consent banner configured and verified
- [ ] Email notifications (order, shipping, refund) configured
- [ ] SEO basics + favicon + social links
- [ ] Footer (menus, policies, newsletter, account)
- [ ] Password/launch state decided (store currently protected by password page)

**LIVE QA**

- [ ] Desktop, tablet, mobile, small mobile (matrix, section 20)
- [ ] Chrome, Safari, Firefox, Edge where available
- [ ] Homepage, collection, search, PDP, ATC, cart, checkout, 404, contact
- [ ] Awareness routes, blog/article, generic pages, password page
- [ ] Per-product live journey passed (section 20)

**FINAL**

- [ ] Test order end-to-end (product → ATC → cart → checkout → payment → confirmation email)
- [ ] Shipping flow verified
- [ ] Refund/cancellation flow verified
- [ ] Analytics events verified (page_view → … → purchase)
- [ ] Consent behavior verified
- [ ] Search indexing: Google Search Console connected; sitemap submitted
- [ ] Remove the password page when ready
- [ ] Final storefront review at desktop + mobile

## 24. New-product replacement SOP

**Remove old product**

1. Unpublish/retire the old product in Admin.
2. Remove it from its collections (replace with the new product there).
3. Update merchandising references: homepage "Current finds" collection, category
   routing slots, any Awareness-page Product settings that pointed at it.
4. If the old product has public URLs worth keeping, create redirects (Admin →
   URL redirects) to the replacement.

**Add new product**

1. Products → Add product → core data, media, price, variants, inventory,
   shipping, SEO.
2. Add to the appropriate collection(s).
3. Populate optional theA2Zcart intelligence fields (real content only).
4. Publish.

**Then verify**: homepage, collection, search, PDP, mobile, desktop, ATC, cart,
checkout handoff.

**No theme code change is required.**

## 25. Protected theme layer

Do not modify these unless a proven, blocking defect exists (theme-engineering
work is separate from store operation):

`blocks/buy-buttons.liquid`, `assets/product-form.js`,
`assets/variant-picker.js`, `assets/sticky-add-to-cart.js`,
`assets/cart-drawer.js`, `assets/cart-icon.js`,
`assets/component-cart-items.js`, `sections/product-information.liquid`,
`snippets/scripts.liquid`, `assets/page-view-event.js`,
`assets/view-event-elements.js`, `assets/standard-actions-override.js`,
`layout/theme.liquid`, `sections/thea2zcart-category-routing.liquid`.

## 26. Product-agnostic ground truth

Everything a visitor sees for a product comes from: (1) Shopify native product
data, (2) `thea2zcart` metafields, (3) Theme Editor block configuration. The
theme contains **no** hard-coded product names, handles, IDs, categories,
types, industries, use cases, journeys, claims, or product counts.

## 27. India operating model (launch configuration)

Merchant-specific operating configuration for the business's actual Indian
setup. All values below are **MERCHANT MUST PROVIDE** — nothing here invents
rates, tax registration, or payment capability. Where external input is needed
(CA/accountant), it is marked CONFIGURATION REQUIRED. Everything is Admin
configuration; no theme code changes.

### Shipping (domestic India)

Admin → Settings → Shipping and delivery:

1. Create an **India domestic shipping zone**.
2. Add the merchant's actual rates/carriers (standard, expedited as actually offered).
3. If free shipping is genuinely offered, add the matching rate rule (e.g., free over a threshold) so the expressed threshold matches checkout exactly — no surprise shipping charge.
4. Delivery expectations: state realistic windows only where the merchant stands behind them, in the Shipping policy and/or the product `shipping_message` metafield. Never invent courier promise times.
5. COD: enable Cash on Delivery **only** if the merchant actually accepts it; otherwise leave disabled and say so in the policy. COD charges must match policy text.

### Tax (GST)

Admin → Settings → Taxes and duties. **CONFIGURATION REQUIRED — CA/accountant input}:**

1. Set up the merchant's actual tax situation (e.g., registered GST: IGST across states, CGST+SGST within a state; or tax-exempt/pending registration).
2. If product HSN codes are recorded, add them per product (product → variants → HS/HTS code).
3. If tax registration is pending or inapplicable, keep tax collection off accordingly and confirm with the accountant before launch.
4. Never display an unverified tax registration number on the storefront.

### Payments (India)

Admin → Settings → Payments:

1. **Prepaid:** enable UPI, cards, and netbanking through the merchant's chosen provider (Shopify Payments where available in India, or an accredited payment gateway).
2. **COD:** enable only if actually offered (see shipping).
3. Payment-failure handling: confirm provider messages/retry behavior at checkout.
4. Currency: set INR under Store details; markets/presentment only if multi-currency is used.
5. **Never claim a payment method works until it is tested** with a real end-to-end order (external live-QA gate).

### Storefront consistency

Every policy statement (shipping, returns, payments, refunds) must match the
configured settings — checkout behavior, rates, taxes, and payment methods must
never contradict the policy text. Support email and response expectations are
merchant-defined.**