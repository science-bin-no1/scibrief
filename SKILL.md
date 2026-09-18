---
name: scibrief
description: >
  Summarize scientific papers into graphical-abstract-level structured briefs optimized for
  computational biologists, bioinformaticians, and experimental biologists. Use this skill
  whenever a user uploads a paper (PDF, text, or supplementary files) and wants a concise
  summary, a quick take, a paper overview, a graphical-abstract-style breakdown, or asks
  "what does this paper say" / "summarize this study" / "what's the key finding." Also
  trigger when users paste abstracts, DOIs, or paper titles and ask for distillation.
  Trigger even for casual requests like "tldr on this paper" or "break this down for me."
  Do NOT use for literature review across many papers, systematic reviews, or meta-analyses —
  those need a different workflow.
---

# scibrief — Graphical-Abstract-Level Paper Summarization

## Purpose

Scientists drown in literature. Reading a full paper takes 30–60 minutes; scanning an
abstract misses the actual contribution. scibrief sits in between: it extracts the
intellectual core of a paper and compresses it into a structured brief with the same
information density as a graphical abstract — readable in under 60 seconds, slide-ready,
and precise enough to decide whether the paper deserves a full read.

The output is designed to be visually memorable. Scientists share briefs on Slack, paste
them into slide decks, and scan them during lab meetings. A good brief should be
recognizable at a glance — distinct visual zones for the story, the evidence, the
mechanism, and the caveats — so the reader's eye can jump straight to what they need.

## Workflow

### 1. Ingest the Paper

Read the full text. If figures or supplementary materials are available, examine them —
they often contain the strongest quantitative results. Pay attention to:

- Figure legends (these carry more signal than body text in most biology papers)
- Methods details that reveal the actual experimental strategy
- Supplementary tables with complete statistical results

If only an abstract is available, work with it but flag the brief as `[Abstract-only]`.

### 2. Extract the Core Elements

Work through these extraction targets in order:

**Research Question**: What specific gap or hypothesis does this study address? Strip away
the motivational framing and get to the actual question being tested.

**Experimental Strategy**: What did they actually do? Name the key assays, model systems,
datasets, and cohorts. Be specific — "RNA-seq of patient-derived organoids" not
"transcriptomic analysis."

**Primary Datasets**: What data was generated or reanalyzed? Include sample sizes, species,
tissue types, and any public data accession numbers.

**Key Quantitative Results**: The numbers that matter. Effect sizes, fold changes, p-values,
confidence intervals, AUCs, survival differences. Cap at 3 results — choose the ones that
carry the paper's main claim.

**Mechanistic Finding**: What biological mechanism was revealed or supported? One sentence
that a biologist could act on.

### 3. Assess the Advance

Identify exactly one of each (if present):

- **Conceptual advance**: What new biological understanding does this provide?
- **Methodological innovation**: Any new technique, pipeline, or analytical approach?
- **Translational implication**: Does this move toward therapy, diagnostics, or clinical use?

### 4. Filter Aggressively

Remove before output:
- Background context the reader already knows
- Literature review padding
- Secondary findings that don't support the main conclusion
- Speculative discussion points
- Author self-citations and narrative framing

The goal is maximum insight per word. Every word in the output should earn its place.

## Output Format

The output uses four visually distinct zones. This matters because scientists scan — they
don't read top-to-bottom. Each zone answers a different question, and the visual
separators let the eye jump directly to the relevant section.

Use this exact template. Do not add sections, narrative, or commentary outside it.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📄 [TITLE LINE — one sentence: what was done → what was found]
   [Journal] | [Year] | [First author et al.]  [Abstract-only] ← if applicable
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔬 THE STORY
   ❓ Gap      → [One line: what we didn't know]
   🧪 Approach → [One line: what they did — name models, assays, datasets]
   💡 Finding  → [One line: the central result]
   🎯 So What  → [One line: why the field should care]

📊 KEY NUMBERS
   ▸ [Quantitative result 1 — with effect size, p-value, n]
   ▸ [Quantitative result 2 — with effect size, p-value, n]
   ▸ [Quantitative result 3 — omit if <3 are impactful]

⚙️ MECHANISM
   [One sentence: the molecular/cellular mechanism, specific enough to
    design a follow-up experiment]

🧭 BOTTOM LINE
   Novel concept   → [One phrase: new biological understanding, or "—" if none]
   New method      → [One phrase: methodological innovation, or "—" if none]
   Clinical angle  → [One phrase: translational implication, or "—" if none]

⚠️ CAVEATS
   [Comma-separated list: statistical concerns, reproducibility flags,
    model limitations, or "None identified"]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Why This Format Works

The template is designed around how scientists actually consume information:

**Zone 1 — Title block**: Answers "what is this paper?" in one glance. The title line
is a complete sentence (not the paper's actual title), so even reading just this line
gives the full story arc. The metadata line (journal, year, author) provides credibility
context without cluttering the science.

**Zone 2 — The Story**: A 4-line narrative arc using emoji anchors as visual landmarks.
The reader's eye can jump to any line independently: ❓ for the gap, 💡 for the finding,
🎯 for significance. Each line is self-contained — no line depends on reading the others.

**Zone 3 — Key Numbers**: Separated from the narrative so a statistician or reviewer can
jump straight to the evidence. Each bullet must contain actual numbers (effect sizes,
p-values, sample sizes) — not vague claims like "significantly increased."

**Zone 4 — Mechanism**: Isolated because it's the highest-value sentence for
experimentalists. A biologist reading just this line should be able to sketch a follow-up
experiment.

**Zone 5 — Bottom Line**: Three-slot classification that answers "what kind of advance
is this?" at a glance. Dashes ("—") for empty slots prevent the reader from wondering
whether something was missed.

**Zone 6 — Caveats**: Always present, always last. Keeps the reader honest. Positioned
last so it doesn't dampen the finding but can't be missed.

## PDF Generation

scibrief supports generating beautifully formatted PDF files with emoji support.

### Method: Using Playwright + HTML

1. **Create HTML with embedded CSS and emoji**:

```python
html_content = '''<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>scibrief - Paper Title</title>
    <style>
        @page { size: A4; margin: 2cm 1.5cm; }
        
        body {
            font-family: "Segoe UI Emoji", "Segoe UI", "Apple Color Emoji", 
                         "Noto Color Emoji", "Microsoft YaHei", sans-serif;
            font-size: 11pt;
            line-height: 1.8;
            color: #333;
        }
        
        /* scibrief Header with Gradient */
        .scibrief-header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 20px;
            border-radius: 10px;
            margin: 15px 0;
            text-align: center;
        }
        
        /* Story Box with Emoji Icons */
        .story-box {
            border: 2px solid #e9ecef;
            border-radius: 10px;
            overflow: hidden;
            margin: 12px 0;
        }
        
        .story-row {
            display: flex;
            border-bottom: 1px solid #e9ecef;
        }
        
        .story-icon {
            width: 60px;
            padding: 12px;
            text-align: center;
            font-size: 18px;
            background: #f8f9fa;
        }
        
        /* Numbers Grid */
        .numbers-grid {
            display: flex;
            gap: 10px;
            margin: 12px 0;
        }
        
        .number-card {
            flex: 1;
            background: #f8f9fa;
            border: 2px solid #e9ecef;
            border-radius: 10px;
            padding: 15px 10px;
            text-align: center;
        }
        
        .number-value {
            font-size: 20px;
            font-weight: bold;
            color: #3498db;
        }
        
        /* Colored Boxes for Different Zones */
        .mechanism-box {
            background: #fff9e6;
            border-left: 4px solid #f39c12;
            padding: 15px;
            margin: 12px 0;
            border-radius: 0 8px 8px 0;
        }
        
        .bottom-line-box {
            background: #f0f9ff;
            border: 2px solid #3498db;
            border-radius: 10px;
            padding: 15px;
            margin: 12px 0;
        }
        
        .caveats-box {
            background: #fff5f5;
            border: 2px solid #e74c3c;
            border-radius: 10px;
            padding: 15px;
            margin: 12px 0;
        }
    </style>
</head>
<body>
    <!-- scibrief Content Here -->
</body>
</html>'''
```

2. **Generate PDF using Playwright**:

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(
        executable_path="/root/.cache/ms-playwright/chromium-1208/chrome-linux64/chrome",
        args=['--no-sandbox', '--disable-setuid-sandbox']
    )
    page = browser.new_page()
    page.goto("file:///path/to/html", wait_until="networkidle")
    
    page.pdf(
        path="/output/path/to/scibrief.pdf",
        format="A4",
        margin={"top": "2cm", "bottom": "2cm", "left": "1.5cm", "right": "1.5cm"},
        print_background=True,
        display_header_footer=True,
        header_template='<div style="font-size:9px; margin-left: 1.5cm; color: #666; font-family: Segoe UI Emoji, sans-serif;">📄 scibrief | Paper Summary</div>',
        footer_template='<div style="font-size:9px; margin: 0 auto; color: #666;"><span class="pageNumber"></span> / <span class="totalPages"></span></div>'
    )
    browser.close()
```

### PDF Design Guidelines

When generating PDFs from scibrief content, follow these design principles:

1. **Emoji Support**: Use emoji-friendly fonts:
   - `"Segoe UI Emoji"` (Windows)
   - `"Apple Color Emoji"` (macOS/iOS)
   - `"Noto Color Emoji"` (Linux)

2. **Visual Hierarchy**:
   - Title: Large font (18-22pt), bold
   - Section headers: Medium font (14-16pt), with left border or color
   - Body text: Regular font (10-11pt)

3. **Color Coding**:
   - 🔬 THE STORY: Use neutral or gradient backgrounds
   - 📊 KEY NUMBERS: Use card-style layouts with metrics highlighted
   - ⚙️ MECHANISM: Use warm colors (yellow/orange accents)
   - 🧭 BOTTOM LINE: Use cool colors (blue accents)
   - ⚠️ CAVEATS: Use warning colors (red accents)

4. **Layout Elements**:
   - Use `border-radius` for rounded corners (8-10px)
   - Use `box-shadow` for depth (optional)
   - Use `page-break-inside: avoid` for tables and important blocks
   - Use `@page` margins for professional appearance

5. **Header/Footer**:
   - Include page numbers
   - Include document title in header
   - Use small, subtle styling (8-9pt, gray color)

## Style Rules

- No narrative paragraphs. Every element is a labeled field.
- No citation markers (no "[1]", no "(Smith et al.)").
- No redundancy between zones. Each zone adds unique information.
- Use precise biological and statistical terminology — the audience knows it.
- Quantitative results include units, statistical tests, and sample sizes where available.
- The emoji anchors are functional landmarks, not decoration — use exactly the emoji
  specified in the template. They create a consistent visual grammar across all briefs.
- The horizontal rules (━━━) create card-like boundaries. Always include them.
- The entire output should fit on a single slide or Slack message (~200 words max).

## Phrasing Guidelines

The title line and story arc should use active, direct phrasing:

**Good title lines** (complete thought, active voice, result included):
- "GR activation in disseminated breast cancer cells represses FAS to evade immune
  killing; GR blockade + anti-PD-1 reduces metastasis"
- "Single-cell atlas of 1.2M cells reveals TREM2+ macrophage subset that predicts
  anti-PD-1 response across 15 cancer types"

**Bad title lines** (vague, passive, or missing result):
- "A study of glucocorticoid receptor signaling in breast cancer metastasis"
- "Single-cell RNA sequencing of tumor-infiltrating immune cells"

**Good Key Numbers** (specific, defensible):
- "▸ GR-KD DTCs: 4.2-fold increase in FAS surface expression (p = 0.003, n = 6/group)"
- "▸ Mifepristone + anti-PD-1: median survival 42 vs 28 days (HR = 0.41, 95% CI 0.22–0.76)"

**Bad Key Numbers** (vague, no evidence weight):
- "▸ GR knockdown increased FAS expression"
- "▸ Combination therapy improved survival significantly"

## Edge Cases

- **Review papers**: Replace 🧪 Approach with "📚 Scope" and 📊 KEY NUMBERS with
  "📌 KEY CLAIMS". Adjust the title line to reflect the synthesis, not a single experiment.
- **Preprints without peer review**: Add "⚠️ Preprint — not peer reviewed" as the
  first item in CAVEATS.
- **Methods papers**: Replace 💡 Finding with "🔧 Capability" in the story arc.
  Lead the title line with the method, not the biology.
- **Multi-omics or large consortium papers**: Focus on the integrative finding,
  not the individual data types. Name the cohort/consortium in the title line.

## Quality Check

Before outputting, verify:
1. Could a PI read this in 60 seconds and know whether to read the full paper?
   If not, compress further.
2. Does the title line alone tell the full story? Test: cover everything else — does the
   title line still make sense on its own?
3. Are the Key Numbers actually quantitative (numbers, not vague claims)?
   If a number isn't available, say "exact values not reported" rather than paraphrasing
   vaguely.
4. Is the Mechanism sentence specific enough to generate a follow-up experiment?
5. Does every zone add information not present in any other zone?
6. Are the Bottom Line slots filled with concrete phrases (not restating the Finding)?
7. Is the total output under 200 words (excluding the border lines)?
8. For PDF generation: Are emoji displaying correctly? Is the layout professional?
   Are colors consistent with scibrief branding?

## Example Output (Text Format)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📄 GR activation in DTCs represses FAS to evade immune killing; 
   GR blockade + anti-PD-1 reduces metastasis
   Nature | 2025 | Vander Steen et al.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔬 THE STORY
   ❓ Gap      → How do DTCs survive immune surveillance during metastatic seeding?
   🧪 Approach → Jedi mice + 4T1 TNBC model, niche-labeling, scRNA-seq, CUT&RUN
   💡 Finding  → GR activation transcriptionally represses FAS, blocking FAS-FASL killing
   🎯 So What  → Reveals GR-FAS axis as immune evasion mechanism; enables combination therapy

📊 KEY NUMBERS
   ▸ GR-KD DTCs: 4.2-fold ↑ FAS expression (p = 0.003, n = 6/group)
   ▸ Mifepristone + anti-PD-1: median survival 42 vs 28 days (HR = 0.41)
   ▸ shGR DTCs: 3.5-fold ↑ immune clearance (p < 0.001)

⚙️ MECHANISM
   GR binds FAS regulatory regions as a transcriptional repressor; FAS downregulation
   blocks FASL-mediated apoptosis, enabling DTCs to evade CD8+ T and NK cell killing.

🧭 BOTTOM LINE
   Novel concept   → GR-FAS axis as metabolic-immune checkpoint in metastasis
   New method      → niche-labeling platform for DTC-microenvironment tracking
   Clinical angle  → GR antagonists (mifepristone) + ICB for TNBC metastasis prevention

⚠️ CAVEATS
   Mouse model validation, limited human samples; long-term GR inhibition side effects
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## PDF Output Example

When generating PDF, the above content should be rendered with:
- 📄 Header: Gradient purple background, white text, centered
- 🔬 THE STORY: Emoji icons in left column (❓ 🧪 💡 🎯), content in right
- 📊 KEY NUMBERS: Three metric cards with large numbers and small p-values
- ⚙️ MECHANISM: Yellow left-border highlight box
- 🧭 BOTTOM LINE: Blue-bordered box with three items
- ⚠️ CAVEATS: Red-bordered warning box
- Horizontal rules (━━━) replaced with visual separators or omitted in PDF

---

*scibrief v2.0 - Now with PDF generation support*  
*Updated: March 2026*
