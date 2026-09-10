# theA2Zcart product intelligence

## Scope

This document defines the theme-side contract for optional product conversion content. It does not create Shopify Admin definitions, populate product data, or change any storefront surface. All fields use the product metafield namespace `thea2zcart`; no product identifier, title, or handle is embedded in the theme.

The contract intentionally keeps short display copy native, uses rich text where merchants need formatted editorial copy, and uses metaobjects only when an entry needs its own structure. Do not replace these fields with JSON: Shopify Admin can validate and edit the chosen native types, and Theme Editor dynamic sources can select them.

## Recommended Shopify Admin definitions

Create the following Product metafield definitions in **Settings → Custom data → Products**. Every field is optional. “Empty” means no theme output; the normal Shopify product title, description, media, price, and availability remain the responsibility of their existing theme components.

| Field | Recommended type | Purpose | Required? | Fallback |
| --- | --- | --- | --- | --- |
| `promise` | Rich text | Primary product value proposition; permits emphasis and links. | No | Render nothing. Use the existing product title/description where appropriate. |
| `short_promise` | Single line text | Compact, card- or headline-sized value proposition. | No | Render nothing; do not truncate `promise` automatically. |
| `problem` | Rich text | Customer problem or pain point. | No | Render nothing. |
| `desire` | Rich text | Desired outcome or aspiration. | No | Render nothing. |
| `benefits` | List of metaobject references: `a2z_content_item` | Ordered benefit statements, each with a heading/body and optional icon. | No | Render no benefit list. |
| `mechanism` | Rich text | Concise explanation of why the product delivers its result. | No | Render nothing. |
| `differentiators` | List of metaobject references: `a2z_content_item` | Product-specific distinctions that need a heading and explanation. | No | Render no differentiator list. |
| `how_it_works` | Rich text | Step-by-step or explanatory product usage/process content. | No | Render nothing. |
| `best_for` | List of single line text | Short audience or situation labels; lightweight and easy to reuse. | No | Render no labels. |
| `use_cases` | List of single line text | Short use-case labels. | No | Render no labels. |
| `badges` | List of single line text | Merchant-controlled, non-regulatory merchandising badges. | No | Render no badges. |
| `shipping_message` | Rich text | Product-specific shipping expectation or note. | No | Render nothing; do not infer delivery dates. |
| `guarantee` | Rich text | Product-specific guarantee or risk-reduction copy. | No | Render nothing; do not infer policy terms. |
| `education` | Rich text | Longer educational content relevant to the product. | No | Render nothing. |
| `faq` | List of metaobject references: `a2z_faq_item` | Ordered product FAQ entries. | No | Render no FAQ section. |
| `comparison` | Metaobject reference: `a2z_comparison_table` | A structured comparison table owned by a single reusable record. | No | Render no comparison section. |

Create these metaobject definitions only if a product needs the corresponding field:

| Metaobject | Fields |
| --- | --- |
| `a2z_content_item` | `heading` (single line text, required), `body` (rich text, optional), `icon` (file reference, optional) |
| `a2z_faq_item` | `question` (single line text, required), `answer` (rich text, required) |
| `a2z_comparison_table` | `heading` (single line text, optional), `intro` (rich text, optional), `rows` (list of metaobject references to `a2z_comparison_row`, required) |
| `a2z_comparison_row` | `label` (single line text, required), `product_value` (rich text, optional), `alternative_value` (rich text, optional) |

Keep entries published to the storefront and constrain reference metafields to their named definition. If comparison requirements later need more than product-versus-alternative columns, revise the comparison metaobject deliberately rather than storing table markup or JSON in the product.

## Theme consumption

`snippets/product-intelligence-field.liquid` is the reusable renderer for populated scalar/rich-text product fields. It receives a product explicitly and accesses `product.metafields.thea2zcart[field]`. It uses `metafield_tag`, preserving Shopify rich-text formatting and dynamic-source compatibility, and emits no markup for missing or blank values.

Example for a future component:

```liquid
{% capture promise %}
  {% render 'product-intelligence-field', product: product, field: 'promise' %}
{% endcapture %}
{% if promise != blank %}
  <div class="product-promise">{{ promise }}</div>
{% endif %}
```

For a structured reference, assign it in the consuming component and guard it before iterating. A Liquid snippet cannot return an object into its caller’s scope, so direct assignment is the appropriate, standard pattern for this case:

```liquid
{% assign faqs = product.metafields.thea2zcart.faq.value %}
{% if faqs != blank %}
  {% for faq in faqs %}
    {% if faq.question != blank and faq.answer != blank %}
      {# Render the component-specific FAQ markup here. #}
    {% endif %}
  {% endfor %}
{% endif %}
```

Components should similarly check each `a2z_content_item` or comparison row before rendering it. The helper is intentionally presentation-neutral: it adds no classes, wrappers, styling, section schema, or product layout.

## Existing reviews and disclosures

Horizon already renders ratings in `blocks/review.liquid` from the app-populated standard product metafields `reviews.rating` (rating object with `rating` and `scale_max`) and `reviews.rating_count`. When no rating exists, the block renders no rating; the only preview defaults are limited to Shopify visual-preview mode. This contract does not alter those fields or create review data.

Horizon also reads native regulatory/safety disclosures from `product.metafields.shopify.disclosure.value` in its disclosure block and cart tooltip. Those remain separate from `thea2zcart.badges`, `guarantee`, and `shipping_message`: the latter are merchandising copy, never a substitute for a required disclosure.

## Empty-state rules

- A product may have complete, partial, or no intelligence data.
- Missing, unpublished, or blank scalar fields render nothing through the helper.
- Empty lists and missing references render no list/table/FAQ container; do not emit headings, dividers, or placeholders before confirming populated entries.
- Never synthesize claims, shipping times, guarantees, ratings, comparisons, or badges from another product or a global default.
