---
name: image-prompt-guide
description: |
  Technical reference for crafting effective prompts when generating or editing images.
  **When to Use** (AFTER deciding to generate/edit an image):
    - Simple image generation: "Generate a cute red labubu", "Create an image of...", "Draw a..."
    - Single creative requests: "Make me a logo", "Design a poster", "Generate product mockup"
    - Image editing: "Edit this image to...", "Change the background", "Add text to image"
    - Any request that just wants ONE image output without business/market analysis
    - **Advertising creatives**: Logos, branding materials, posters, banners (even for business use)
    - **Categories NOT covered by new-product-development-design**: Food & beverages, books & media, virtual goods/services, industrial equipment/parts
  **DO NOT use for full product development** (in supported categories):
    - "Design a product line for Gen-Z market" → Use new-product-development-design first (if category is apparel, home, beauty, electronics, etc.)
    - "Develop 3 product proposals with cost analysis" → Use new-product-development-design instead
    - Requests involving market research, multi-proposal comparison, or supplier matching
  **Key differentiator**: This skill is for IMAGE GENERATION technique, not business product strategy.
enabled: true
---

# Image Prompt Guide

A specification for constructing high-quality prompts for AI image generation and editing tools.

## Spec Definition

### Golden Rules

| Rule | Description |
|------|-------------|
| **Edit, Don't Re-roll** | If 80% correct, modify conversationally instead of regenerating |
| **Natural Language** | Use complete sentences, not keyword stacking |
| **Be Specific** | Define subject, environment, lighting, mood explicitly |
| **Provide Context** | Include "why" or "for whom" to guide artistic decisions |
| **Structured Elements** | Treat prompts as design briefs with clear components |
| **Avoid Brand Infringement** | Unless explicitly requested by user, avoid including recognizable brand logos, brand names, or trademarked elements to prevent copyright/trademark issues |

### Prompt Structure Formula

```
[Shot type] of [Subject] in [Setting], [Action/State]. 
[Style], [Composition], [Lighting], [Color], [Quality].
```

### Required Elements

| Element | Description | Examples |
|---------|-------------|----------|
| **Subject** | What to draw (be specific) | "ginger tabby cat", "ergonomic wireless headphones" |
| **Setting** | Where is the subject | "windowsill bathed in afternoon sunlight" |
| **Style** | Overall feeling | "cinematic", "watercolor", "minimalist" |
| **Composition** | Camera placement | "close-up", "wide-angle", "rule-of-thirds" |
| **Lighting** | Light source and mood | "golden hour", "soft diffused", "Rembrandt lighting" |
| **Color** | Color palette | "Morandi palette", "high saturation", "monochromatic" |
| **Quality** | Detail level | "8K", "hyperrealistic", "masterpiece" |

## How to Use

### For Image Generation (No Reference)

Construct comprehensive prompts covering:
- **Core Elements**: Subject or product features
- **Design Philosophy**: Minimalist, luxurious, eco-friendly, futuristic
- **Setting**: Studio lighting, natural environment, lifestyle context
- **Artistic Style**: Modern, vintage, industrial, organic
- **Visual Effects**: Textures, lighting, materials, color schemes
- **Text**: Use quotes for exact text: `"Display 'LIMITED EDITION' in bold serif"`
- **Resolution**: Request 2K/4K for texture-heavy or print materials

**Note on Brand Safety**: When constructing prompts, avoid including recognizable brand logos, brand names, or trademarked visual elements unless the user explicitly requests them. This helps prevent copyright/trademark infringement issues.

### For Image Editing (With Reference)

Use **semantic instructions**—describe changes naturally:

| Action Type | Examples |
|-------------|----------|
| **Core** | Add, Change, Remove, Replace, Make |
| **Creative** | Restore, Colorize, Illustrate as, Retexture |
| **Compositional** | Combine, Isolate, Zoom out, Blur, Overlay |
| **Dimensional** | Convert 2D to 3D, Convert sketch to render |

## Common Scenarios

This guide applies to various image generation scenarios:

- **Product Photography**: E-commerce shots, lifestyle context, studio lighting
- **Logo Design**: Brand identity, wordmarks, icons (see Advanced References for detailed guidance)
- **Marketing Materials**: Posters, banners, social media graphics
- **Character & Illustration**: Stickers, icons, character design, concept art
- **Scene & Environment**: Backgrounds, landscapes, interior design visualizations
- **Image Editing**: Background changes, object removal, style transfers

## Anti-Patterns

| Avoid ❌ | Why | Better ✅ |
|---------|-----|-----------|
| "beautiful design" | Too vague | Describe specific elements |
| "high quality" | Non-visual | Describe textures, materials, lighting |
| "professional look" | Subjective | Reference specific brands or contexts |
| "make it pop" | Meaningless | Specify contrast, saturation, or focal emphasis |
| Tag stacking | Model understands intent | Use natural sentences |
| Including brand logos/names without user request | Copyright/trademark infringement risk | Use generic descriptions or style references: "minimalist tech aesthetic" instead of "Apple logo" |

## Next Steps

For detailed guidance:
- Design aesthetics enhancement → See `## Design Aesthetics` below
- Material & texture precision → See `## Materials` below
- Brand/style anchoring → See `## Brand References` below

---

## Design Aesthetics

### Atmosphere & Mood Keywords
serene, contemplative, ethereal, intimate, bold, sophisticated, melancholic, uplifting, mysterious, tranquil, wistful, inviting, dramatic, peaceful, energetic, nostalgic, whimsical, elegant, cozy, minimalist

### Composition Principles

| Principle | Prompt Keywords |
|-----------|----------------|
| Negative Space | "generous negative space", "breathing room around subject" |
| Rule of Thirds | "subject positioned at rule-of-thirds intersection" |
| Visual Hierarchy | "clear visual hierarchy with X as focal point" |
| Leading Lines | "leading lines drawing eye toward subject" |

### Lighting Quality

| Basic ❌ | Professional ✅ |
|----------|----------------|
| "bright light" | "diffused golden hour light with soft rim lighting" |
| "dark background" | "deep shadows with subtle gradient falloff" |
| "natural light" | "north-facing window light, soft and directional" |

## Materials

| Generic ❌ | Precise ✅ |
|-----------|-----------|
| "metal finish" | "brushed titanium with subtle anodized reflections" |
| "glass bottle" | "frosted borosilicate glass with soft-touch matte coating" |
| "leather" | "vegetable-tanned full-grain leather with natural patina" |
| "wood" | "live-edge walnut with hand-rubbed oil finish" |

## Brand References

| Category | Reference Brands |
|----------|-----------------|
| Beauty/Skincare | SK-II, Aesop, La Mer |
| Tech/Electronics | Apple, Bang & Olufsen, Nothing Phone |
| Lifestyle | Kinfolk magazine, Cereal magazine, Monocle |
| Luxury/Fashion | Hermès, Bottega Veneta, Muji |

**Usage**: "Overall aesthetic inspired by [Brand] advertising style"

**Note**: When referencing brand styles, focus on describing aesthetic qualities (minimalist, luxurious, etc.) rather than including actual brand logos, brand names, or trademarked elements. Only include recognizable brand elements if the user explicitly requests them.