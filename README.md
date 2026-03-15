# n8n Workflows — Dropship OS & Shopify Automation

This repository contains **6 n8n workflows** for product research, Airtable sync, Shopify publishing, store pages, and an AI agent. Use them with [n8n](https://n8n.io) (self-hosted or n8n Cloud).

---

## Table of contents

- [Requirements](#requirements)
- [Workflows overview](#workflows-overview)
- [Workflow details](#workflow-details)
- [Setup](#setup)
- [License](#license)

---

## Requirements

- **n8n** (self-hosted or [n8n Cloud](https://n8n.io))
- **Credentials** (configure in n8n):
  - Airtable (Personal Access Token)
  - OpenAI API
  - Shopify (Admin API / Access Token)

Airtable bases/tables and Shopify store URLs are referenced in the workflows; update base IDs, table IDs, and store URLs to match your own.

---

## Workflows overview

| # | File | Name | Trigger | Purpose |
|---|------|------|---------|---------|
| 1 | `eUZcAevNrU5x3TFy-01_Product_Research_OpenAI_Airtable_.json` | 01 - Product Research (OpenAI → Airtable) | Cron daily 9am | Fetches settings from Airtable, calls OpenAI for 5 AU-friendly dropshipping products, writes to Airtable. |
| 2 | `YFgS6bsd4Jp5GxBl-Product_research_to_Airtable.json` | Product research to Airtable | Schedule 9am | Settings-driven research: reads Airtable Settings (niche, market, cost, margin), OpenAI with JSON schema, logs to PromptsLog, creates Products with fingerprint/dedup. |
| 3 | `KedIL9WNllh2Pg4U-ad_creatives_and_publisher_of_products.json` | ad creatives and publisher of products | Schedule (hourly) | Placeholder: schedule trigger + HTTP Request; extend for ad creative generation and publishing. |
| 4 | `zmwBZ8JuLRUyxpxm-approved_products_publish.json` | approved products-publish | Schedule | Gets Airtable Products with `status = approved`, generates Shopify listing copy via OpenAI, creates products in Shopify (GraphQL + media), updates Airtable. |
| 5 | `m4pgs8krBpfO8BzH-website_maker_done_.json` | website maker (done) | Manual | Reads brand/settings from Airtable, uses OpenAI to generate About, Shipping & Returns, FAQ, Contact pages (HTML), then creates each as a Shopify page. |
| 6 | `Xr5n8F2Xz66VZsVk-AI_Agent_workflow.json` | AI Agent workflow | Manual / Daily 7am | LangChain-style agent with OpenAI model and tools (e.g. RSS: The Verge, BBC World); can summarize news and answer user prompts. |

---

## Workflow details

### 1. 01 - Product Research (OpenAI → Airtable)

- **Trigger:** Cron daily at 9am.
- **Flow:** Get Settings from Airtable (base `dropship os`, table Settings, `active = TRUE`) → OpenAI product research (5 products, Australia-legal, non-medical, non-branded) → Parse Products (Code) → Create Product Rows in Airtable.
- **Use case:** Simple daily product discovery and storage in Airtable.

### 2. Product research to Airtable

- **Trigger:** Schedule daily at 9am.
- **Flow:** Search Airtable Settings → If `active` → HTTP Request to OpenAI (model `gpt-5-mini`, structured JSON output) using settings: `niche_focus`, `country_market`, `max_product_cost`, `min_margin_multiplier` → Create PromptsLog record → Parse Products JSON (fingerprint, dedup logic) → Loop → Check Products by fingerprint → Create or skip Products in Airtable.
- **Use case:** Configurable, settings-driven research with prompt logging and duplicate prevention.

### 3. ad creatives and publisher of products

- **Trigger:** Schedule (hourly).
- **Flow:** Schedule Trigger → HTTP Request (POST; URL/body to be configured).
- **Use case:** Stub for future ad creative generation and publishing; add your endpoints and logic as needed.

### 4. approved products-publish

- **Trigger:** Schedule (interval in hours).
- **Flow:** Airtable search (Products, `status = 'approved'`) → Split In Batches → For each: Prepare Context (Code: normalizes image_urls, builds context) → OpenAI HTTP (Shopify listing JSON: title, descriptionHtml, tags, SEO, sku, price, compare_at_price, image_urls) → Parse Listing → Shopify GraphQL `productCreate` → Handle response (productId, variantId, handle) → Build Media Inputs → If productId + media → Shopify `productCreateMedia`; loop continues; Airtable update for status/link.
- **Use case:** Publish approved Airtable products to Shopify with AI-generated copy and images.

### 5. website maker (done)

- **Trigger:** Manual (“Execute workflow”).
- **Flow:** Airtable Get Settings (Dropship OS, Settings) → OpenAI Generate Pages (brand_name, tagline, tone, country_market → 4 pages: About, Shipping & Returns, FAQ, Contact) → Split Pages (Code) → For each page: Shopify Create Page (REST: title, body_html, published).
- **Use case:** One-click generation of policy/support pages for a Shopify store from Airtable brand settings.

### 6. AI Agent workflow

- **Trigger:** Manual or schedule daily at 7am.
- **Flow:** OpenAI model (e.g. gpt-4.1-mini) with tools: RSS (The Verge tech, BBC World news). Agent responds to user prompt or scheduled run; output is set in a Set node.
- **Use case:** News-reading agent for summaries or Q&A; extend with more tools as needed.

---

## Setup

1. **Clone or download** this repo.
2. **Import in n8n:**  
   In n8n: Workflows → Import from File → choose each `.json` file.
3. **Credentials:**  
   Create and assign in n8n:
   - Airtable (Personal Access Token)
   - OpenAI API
   - Shopify (Access Token for Admin API)
4. **Configure resources:**
   - Replace Airtable base/table IDs and Shopify store URL in the workflows with your own.
   - Adjust triggers (cron/schedule) and any filters (e.g. `active`, `status = 'approved'`) to match your Airtable schema.
5. **Activate** the workflows you need after testing.

---

## File naming

Each file is named: `{n8n-internal-id}-{human-readable-name}.json`.  
You can rename the files for clarity; n8n identifies workflows by the `name` and `nodes` inside the JSON, not by filename.

---

## License

Use and modify these workflows for your own projects. If you redistribute, keep attribution as you see fit.
