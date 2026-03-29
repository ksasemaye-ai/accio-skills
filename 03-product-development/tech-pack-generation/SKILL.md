---
name: tech-pack-generation
description: |
  Generate production-ready tech pack with technical annotations for an EXISTING product image.
  
  **When to use**: ONLY trigger when user EXPLICITLY requests tech pack / technical drawing / spec sheet
    ✓ "Create a tech pack for this"
    ✓ "Generate a technical drawing / spec sheet"
    ✓ "制作工艺图 / 规格图 / 技术图纸"
    ✓ "Include manufacturing/process diagrams"
    ✓ "Include assembly diagrams"
  
  **DO NOT trigger for general product design requests:**
    ✗ "Design a product for..." → Use new-product-development-design
    ✗ "Create designs for my product" → Use new-product-development-design
    ✗ "Generate product images" → Use image-prompt-guide
    ✗ Any request without explicit mention of "tech pack", "technical drawing", "specs", "dimensions"
  
  **Prerequisites**: User must already have a product image to annotate.
enabled: true
---

# Tech Pack Generation

Generate production-ready tech packs with orthographic views, manufacturing diagrams, and assembly diagrams.

---

## 📋 Output Structure

| Output Type | Default? | Format | Trigger Keywords |
|-------------|----------|--------|------------------|
| **Three-View Drawing** | ✅ YES | ONE combined image | Always |
| **Manufacturing Diagram** | ❌ NO | Separate image | "manufacturing diagram", "process", "工艺图" |
| **Assembly Diagram** | ❌ NO | Separate image | "assembly", "exploded view", "组装图", "BOM" |
| **Compliance & Safety Analysis** | ✅ YES | Text | Always (auto-included) |

---

## 🔴 Critical Rules

| Rule | Requirement |
|------|-------------|
| **Subject Integrity** | Product must be 100% IDENTICAL to source — no changes to shape, color, texture |
| **Three-View** | Always ONE combined image with 3+ views (Front, Side, Top/Back) |
| **Separate Diagrams** | Manufacturing & Assembly diagrams are SEPARATE images when requested |
| **Detail Callouts** | Use zoom callouts for critical features (stitching, joints, hardware) |

---

## 📐 Diagram 1: Three-View Drawing (Always Required)

**ONE combined image** with Front + Side + Top/Back views.

### Prompt Template

```
Create a tech pack for this [PRODUCT].

【SUBJECT PRESERVATION】
Keep product EXACTLY as source — do NOT change shape, color, texture. Only ADD annotations.

【LAYOUT】
ONE image with 3+ views: Front, Side, Top/Back on white background.

【ANNOTATIONS PER VIEW】
- Dimensions: H: __cm, W: __cm, D: __cm (with unit)
- Materials: specific grade (e.g., "600D Polyester")
- Surface finish: (e.g., "Matte", "Powder-coated")

【STYLE】
Technical drawing, thin outlines, title block with Product Name + Scale.
```

---

## 🔧 Diagram 2: Manufacturing Diagram (Only When Requested)

**SEPARATE image** showing production workflow and craft details.

### Prompt Template

```
Create a manufacturing process diagram for this [PRODUCT].

【SUBJECT PRESERVATION】
Keep product appearance EXACTLY as source. Only ADD process flow, callouts, and annotations around it.

【LAYOUT】
Main product in center with numbered process steps around it (①②③...).

【CONTENT】
- Process Flow: Raw Material → Step 1 → Step 2 → ... → Finished
- Material specs at each stage
- Zoom callouts for critical craft (welding, stitching, molding)
- Tolerances and quality inspection points

【STYLE】
Technical illustration, numbered callouts, arrows showing sequence.
```

### Required Elements
- Process flow with sequential steps
- Material callouts (thickness, grade)
- Craft detail zooms (joints, finishes)
- Tolerances (±0.5mm) and quality points

---

## 🔩 Diagram 3: Assembly Diagram (Only When Requested)

**SEPARATE image** showing exploded view and component connections.

### Prompt Template

```
Create an assembly diagram for this [PRODUCT].

【SUBJECT PRESERVATION】
Keep each component's appearance EXACTLY as source. Only SEPARATE components along axis to show assembly relationship.

【LAYOUT】
Exploded view with components separated along axis.
- Part numbers (①②③...) next to each component
- Dashed assembly lines connecting mating parts
- BOM table in corner

【BOM FORMAT】
| # | Part | Material | Qty |

【DETAIL CALLOUTS】
- Zoom into connection points (screws, snap fits, adhesive)
- Show fastener types and sizes

【STYLE】
Isometric view, consistent spacing between exploded parts.
```

### Required Elements
- Exploded view with part numbering
- Assembly lines (dashed) showing connections
- BOM table with materials and quantities
- Fastener/connection detail zooms

---

## 📜 Compliance & Safety Analysis (Always Required, Text Output)

Based on product type and target market, provide text analysis covering:
- **Applicable Regulations**: Mandatory regulations for target market
- **Certification Requirements**: Required/recommended product certifications
- **Material Compliance**: Restricted substances, testing requirements
- **Labeling Requirements**: Country of origin, composition, warning labels

### Quick Reference by Product Type

| Product Type | Key Standards |
|--------------|---------------|
| Electronics | FCC, CE, UL, RoHS |
| Toys/Children | CPSIA, ASTM F963, EN 71 |
| Food-contact | FDA 21 CFR, EU 1935/2004 |
| Textiles/Apparel | Flammability, OEKO-TEX |
| Furniture | BIFMA, CA TB 117 |

---

## 📝 Workflow

| User Request | Action |
|--------------|--------|
| "tech pack" / "dimension drawing" only | Three-View + Compliance Analysis |
| + "manufacturing" / "process" / "工艺图" | Add Manufacturing Diagram (separate) |
| + "assembly" / "exploded" / "BOM" / "组装图" | Add Assembly Diagram (separate) |

**Order**: Three-View → Manufacturing (if requested) → Assembly (if requested) → Compliance & Safety Analysis (always)

---

## 📏 Dimension Reference

| Product | Typical Size |
|---------|-------------|
| Mug/Cup | H: 8-12cm, Ø: 7-10cm |
| Backpack | H: 40-50cm × W: 28-35cm × D: 12-18cm |
| Tote Bag | H: 35-45cm × W: 30-40cm |
| Candle/Warmer | H: 15-30cm, Ø: 8-15cm |
| Table Lamp | H: 30-50cm, Base Ø: 12-20cm |

---

## ❌ Common Mistakes

| Wrong | Correct |
|-------|---------|
| Product looks different from source | Keep IDENTICAL — edit, don't regenerate |
| Only 1 view in three-view | Must have 3+ views in ONE combined image |
| All diagrams in one image | Three-view = combined; Manufacturing & Assembly = SEPARATE |
| Missing dimensions units | Always include units: `H: 14.5cm` |
| No BOM when assembly requested | Include parts list with materials and quantities |
| Only text output | Must call image edit tool for each diagram |

---

## ✅ Output Example (Full Tech Pack)

**User**: "Create a production-ready tech pack with dimension drawings, BOM, and manufacturing/assembly diagrams."

**Output**:
1. **Image 1 (Three-View)**: Front + Side + Top views with dimensions, materials, finish annotations
2. **Image 2 (Manufacturing)**: Process flow ①②③, craft zooms, quality points
3. **Image 3 (Assembly)**: Exploded view, BOM table, connection detail zooms
4. **Text - Specifications**: Dimensions, materials, manufacturing notes
5. **Text - Compliance & Safety**: Applicable regulations, required certifications, material compliance, labeling requirements, recommended testing
