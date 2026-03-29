---
name: product-supplier-sourcing
description: Product sourcing and supplier discovery. Use when the user wants to find products, compare items, search for suppliers, manufacturers, or factories on B2B platforms. Default source is alibaba.com via the `product_supplier_search` tool. If the user specifies another platform, look for available tools or methods for that platform.
always_apply: true
---

# Product & Supplier Sourcing

Use this skill when the user intent is **product search** (find products, compare, view details) or **supplier search** (find suppliers, manufacturers, factories). This covers keywords in any language related to product sourcing, supplier discovery, factory search, procurement, etc.

## Default Source: alibaba.com

The `product_supplier_search` tool searches **alibaba.com** by default. When the user does not specify a platform, always use this tool.

### If the user specifies another platform

If the user explicitly asks to source from a different platform (e.g. 1688.com, Amazon, Temu, Made-in-China, Global Sources, etc.):

1. **Do NOT blindly call `product_supplier_search`** — it only covers alibaba.com.
2. Check whether there is an available tool or MCP method for that platform (e.g. search for tools containing the platform name).
3. If a matching tool exists, use it. If not, try generic data-fetching approaches in order:
   - Check if MCP provides general-purpose scraping/search tools such as **Apify**, **Exa**, or similar crawling services that can target the specified platform.
   - Fall back to **`web_search`** to search the target platform via web search engine (e.g. `site:1688.com bluetooth earbuds`).
   - If none of the above yields usable results, inform the user that the requested platform is not directly supported and suggest using the browser to search manually.

## When to Use

- **Product search**: User wants to find products, compare, check prices, or view product details (e.g. "find Bluetooth earbuds", "any stainless steel tumblers").
- **Supplier search**: User wants to find suppliers, manufacturers, factories, or company profiles (e.g. "suppliers for apparel", "factories in Dongguan").
- If the user needs both products and suppliers, call the tool twice: once with `intent_type=product`, once with `intent_type=supplier`.

## Tool Call

**Tool name**: `product_supplier_search`

| Parameter | Required | Description |
|-----------|----------|-------------|
| `intent_type` | Yes | `"product"` = product search, `"supplier"` = supplier search |
| `query` | Yes | Search terms, **must be in English**; translate from the user's language if needed. Format example: `[product_name], [attribute1], [attribute2]`, e.g. `red dress, women, summer` |
| `reference_image` | No | Image URL for image-based search; only pass when a real image URL is already in context |

- Product search: use `intent_type: "product"`, `query` with product/category keywords in English.
- Supplier search: use `intent_type: "supplier"`, `query` with industry/product/region keywords in English; **do not** use company names as the query.

## Rules

1. **Call at least once**: If you determine the user intent is product or supplier search, you must call `product_supplier_search` at least once before replying with conclusions.
2. **Query must be English**: When the user input is not in English, translate it to English before passing as `query`.
3. **Do not fabricate images**: Use `reference_image` only when the context already contains a real image URL; never invent URLs.
4. **Sufficient results**: If a single call returns enough results (e.g. 10+ products or 5+ suppliers), do not repeat the search just to pad the count.
5. **Prefer tables**: When presenting search results (products or suppliers), use Markdown tables when possible. **Include product main image** (image URL in a column or inline) for quick visual scanning. **Include product links and supplier/seller links** so the user can click through for details and follow-up actions.
