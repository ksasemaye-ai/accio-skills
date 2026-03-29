---
name: amz-product-optimizer
description: |
  Amazon product optimization end-to-end automation - A one-stop solution from hot keyword analysis to product detail image generation.
  Trigger phrases: "optimize product", "generate product images", "get hot keywords", "cat food optimization", "dog bed listing"
---

# Amazon Product Optimizer (AMZ Product Optimizer)

A one-stop Amazon product optimization solution that helps sellers automate the entire workflow from keyword analysis to product detail image generation.

## Core Capabilities

1. **AMZ123 Hot Keyword Scraping** - Real-time retrieval of Amazon US Top 250K search term ranking data
2. **Smart Product Title Optimization** - Optimize product titles based on hot keywords, following the [Brand Name] + [Core Keyword] + [Product Feature] + [Specifications] structure
3. **AI Detail Image Generation** - Automatically generate 5 scene-based product detail images using the Taobao MCP service
4. **Main Image CTR Monitoring** - Scheduled monitoring of main image click-through rates, auto-identifying products needing optimization
5. **Local Data Auto-save** - Optimization results synced in real-time to local JSON/CSV files

## Use Cases

### Typical Scenarios
- "Help me optimize products in the cat food category"
- "Get Amazon hot keywords and optimize product titles"
- "Auto-generate 5 detail images for a product"
- "Monitor main image CTR and find underperforming products"
- "Batch optimize product information from 1688 sourcing"

### Target Users
- Amazon cross-border e-commerce sellers
- 1688 supplier product selection operators
- E-commerce product managers
- Cross-border e-commerce operations staff

## Quick Start

### Basic Usage

**Example 1: Optimize products for a specific keyword**
```
User: Help me optimize cat food products
Assistant: Fetching hot keywords related to cat food...
     Found 2 products to optimize
     Generating product detail images...
     ✅ Done! Optimized 2 products, generated 10 images
```

**Example 2: Hot keywords only**
```
User: Check search popularity for dog bed
Assistant: Fetching hot keywords for dog bed from AMZ123...
     Retrieved 50 hot keywords, top 3:
     1. dog bed (Rank 1, Rising ↑)
     2. orthopedic dog bed (Rank 5, Rising ↑)
     3. washable dog bed (Rank 12, Falling ↓)
```

**Example 3: CTR monitoring**
```
User: Check main image click-through rates
Assistant: Analyzing main image CTR for all products...
     📊 Monitoring Report:
     - Total products: 4
     - Ideal CTR (≥5%): 0
     - Needs optimization (<5%): 4
     Recommendation: Generate new main images for low-CTR products
```

## Workflow

### Full Flow (full mode)

```
Step 1: Get Hot Keywords
  ↓ Scrape TOP 50 hot keywords from AMZ123
  ↓ Includes: search term, current week rank, last week rank, trend change
  
Step 2: Read Product Data
  ↓ Query products to optimize from local product_file (JSON/CSV)
  ↓ Extract: original product name, original image link
  
Step 3: Optimize Product Titles
  ↓ Generate fluent e-commerce titles based on hot keywords
  ↓ Strictly follow: [Brand Name] + [Core Keyword] + [Product Feature] + [Specifications]
  ↓ Prohibited: keyword stuffing, "Hot Search" and similar marker words
  
Step 4: Generate 5 Detail Images
  ↓ Each image uses a different scene-based prompt:
    - Main image: Cozy living room scene (showcase overall effect)
    - Detail 1: Bedroom bedside scene
    - Detail 2: Product detail close-up
    - Detail 3: Pet usage scene (marketing conversion)
    - Detail 4: Sunny reading corner scene
    - Detail 5: Reuse main image (maintain visual consistency)
  
Step 5: Write to Local File
  ↓ Update fields:
    - Optimized product title (text)
    - Main image (link)
    - Detail images 1-5 (links)
  
Step 6: Set Up Monitoring Task
  ↓ Create scheduled task, check CTR daily at 10:00
  ↓ Threshold: < 5% flagged for optimization
```

## Input Parameters

### Required Parameters

| Parameter | Type | Description | Example |
|-----------|------|-------------|---------|
| keyword | string | Keyword to optimize | `"cat food"` |
| product_file | string | Product data file path | `"products.json"` |

### Optional Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| mode | string | `"full"` | Execution mode (see table below) |

### Execution Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| `full` | Full flow: hot keywords + optimization + image generation | First-time comprehensive optimization |
| `keywords_only` | Hot keywords retrieval only | Keyword analysis |
| `optimize_names` | Product title optimization only | Already have hot keywords, just need title expansion |
| `generate_images` | Detail image generation only | Titles already optimized, need images |
| `monitor` | CTR monitoring only | Daily data monitoring |

## Output

### Standard Output

```json
{
  "status": "success",
  "mode": "full",
  "keywordsCount": 50,
  "optimizedProductsCount": 2,
  "generatedImagesCount": 10,
  "duration": "45.2s"
}
```

### Field Descriptions

- **status**: Execution status (`success` / `error`)
- **keywordsCount**: Number of hot keywords retrieved
- **optimizedProductsCount**: Number of products optimized
- **generatedImagesCount**: Number of images generated
- **monitorReport**: CTR monitoring report (monitor mode only)

## Product Title Optimization Strategy

### Optimization Formula

**Standard Structure**: `[Brand Name] + [Core Keyword] + [Product Feature] + [Material/Attribute] + [Size/Color]`

### Comparison Examples

| ❌ Wrong | ✅ Correct |
|----------|-----------|
| Blue Buffalo Cat Food Hot Search: wet cat food, dry cat food | Blue Buffalo Tastefuls Flaked Wet Cat Food, High Protein Natural Ingredients with Real Fish, 3 oz Cans (Pack of 24) |
| Purina Friskies Cat Food Keyword Stuffing | Purina Friskies Wet Cat Food Variety Pack, Pate and Shredded Textures in Gravy, Assorted Flavors, 5.5 oz Cans (Pack of 30) |

### Optimization Principles

1. **Never include "Hot Search" or similar marker words** — Will be flagged as a violation by the platform
2. **Avoid keyword stuffing** — Titles must be fluent, complete descriptions
3. **Naturally integrate hot keywords** — Embed hot keywords into product feature descriptions
4. **Maintain readability** — Titles should read as natural product descriptions, not keyword lists

## Detail Image Generation Strategy

### 5 Scene-Based Prompts

| Image Position | Scene | Prompt Key Points | Rationale |
|---------------|-------|-------------------|-----------|
| **Main Image** | Cozy Living Room | Modern living room, soft natural lighting, cozy home atmosphere | Showcase overall effect and usage scenario, attract clicks |
| **Detail 1** | Bedroom Bedside | Cozy bedroom scene, warm morning sunlight, peaceful atmosphere | Lifestyle scene, enhance immersion |
| **Detail 2** | Product Detail | Close-up detail shot, material texture, quality highlights | Showcase craftsmanship and material details |
| **Detail 3** | Pet Usage | Happy pet using product, emotional appeal, lifestyle photography | Marketing conversion scene, stimulate purchase intent |
| **Detail 4** | Sunny Reading Corner | Afternoon golden hour lighting, modern home decor | Lifestyle showcase |
| **Detail 5** | Reuse Main Image | Same as main image | Maintain visual consistency |

### Main Image Selection Principles

✅ **Prefer**: Images that showcase overall effect and usage scenarios
❌ **Avoid**: Overly detailed close-up shots

## Dependencies

### MCP Services

The following MCP services must be configured:

| Service Name | Server ID | Purpose |
|-------------|----------|---------|
| **Taobao opc Service** | `19cf03a191f` | Taobao image generation (create_picture_from_tb) |

### Python Dependencies

```bash
pip install requests>=2.28.0
pip install beautifulsoup4>=4.11.0
```

## Best Practices

### 1. Hot Keyword Update Frequency
Recommend updating hot keywords weekly to track ranking trend changes.

### 2. Product Title Optimization Tips
- Select 3 most relevant hot keywords, avoid stuffing
- Maintain title readability, avoid over-optimization
- Prioritize hot keywords with rising rankings

### 3. Image Generation Notes
- Ensure original image links are accessible
- Each image prompt should have distinct scene differences
- Use overall scene images for main image, detail shots for detail pages

### 4. CTR Monitoring
- Set reasonable thresholds (recommended 5%)
- Promptly replace main images for low-CTR products
- A/B test main images with different scenes

## Troubleshooting

### Common Issues

**Q: Hot keyword retrieval failed**
A: Check if the AMZ123 website is accessible; browser automation support may be required.

**Q: Image generation timeout**
A: Taobao MCP service has concurrency limits; consider reducing batch size or adding delays.

**Q: Table write failed**
A: Confirm field types match (url vs attachment), check permission configuration.

**Q: Product title optimization doesn't meet expectations**
A: Check if the standard structure was followed, ensure no "Hot Search" or similar marker words appear.

### Error Codes

| Error Code | Description | Solution |
|-----------|-------------|----------|
| KEYWORD_NOT_FOUND | No hot keyword data for this keyword | Try a more popular keyword |
| IMAGE_GENERATION_FAILED | Image generation failed | Check original image link validity |
| TABLE_WRITE_ERROR | Table write failed | Verify field types and permissions |
| INVALID_TITLE_FORMAT | Product title format error | Regenerate following the standard structure |

## Version History

### v2.0.0 (2026-03-16)
- ✅ Fixed product title optimization strategy, removed "Hot Search" tags
- ✅ Follow e-commerce title standard structure
- ✅ Avoid keyword stuffing, maintain fluent sentences
- ✅ Optimized detail image generation prompts, enhanced scene differentiation
- ✅ Improved main image CTR monitoring mechanism

### v1.0.0 (2026-03-15)
- ✅ AMZ123 hot keyword scraping
- ✅ Smart product title expansion
- ✅ Taobao MCP detail image generation
- ✅ Main image CTR monitoring
- ✅ Local file auto-sync

## Technical Support

- **Author**: Beiyechuan
- **Organization**: Bug Zhuanjia
- **Contact**: Internal DingTalk

## License

This skill package is owned by the creator and may not be used for commercial purposes without permission.
