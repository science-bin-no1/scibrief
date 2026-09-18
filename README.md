# scibrief

**Graphical-abstract-level summarization of scientific papers — readable in 60 seconds, slide-ready, precise enough to decide whether a paper deserves a full read.**

A [Claude Skill](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview) for computational biologists, bioinformaticians, and experimental biologists.

---

## The problem

Reading a full paper takes 30–60 minutes. Scanning the abstract misses the actual contribution. `scibrief` sits in between: it extracts the intellectual core of a paper and compresses it into a fixed, scannable structure with the same information density as a graphical abstract.

Briefs are designed to be pasted into Slack, dropped into slide decks, and scanned during lab meetings.

## Example output

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

## Why this structure

Scientists scan; they don't read top-to-bottom. Each zone answers a different question, and the emoji anchors act as visual landmarks so the eye can jump straight to what it needs.

| Zone | Answers | Who it's for |
|---|---|---|
| 📄 **Title block** | What is this paper? | Everyone — a complete sentence, not the paper's actual title |
| 🔬 **The Story** | Gap → Approach → Finding → So What | The 4-line narrative arc; each line stands alone |
| 📊 **Key Numbers** | What's the evidence? | Reviewers and statisticians — effect sizes, p-values, n |
| ⚙️ **Mechanism** | What's the biology? | Experimentalists — specific enough to design a follow-up |
| 🧭 **Bottom Line** | What kind of advance is this? | Concept / method / clinical, with `—` for empty slots |
| ⚠️ **Caveats** | What should I doubt? | Always present, always last |

Hard cap: ~200 words, one slide or one Slack message.

## Installation

### Claude Code

```bash
mkdir -p ~/.claude/skills/scibrief
curl -fsSL https://raw.githubusercontent.com/science-bin-no1/scibrief/main/SKILL.md \
  -o ~/.claude/skills/scibrief/SKILL.md
```

Or clone directly:

```bash
git clone https://github.com/science-bin-no1/scibrief.git ~/.claude/skills/scibrief
```

For a project-scoped install, use `.claude/skills/scibrief/` inside the repo instead.

### Claude.ai / Cowork

Settings → **Capabilities** → **Skills** → **Upload skill**, then upload a zip containing `SKILL.md`:

```bash
zip -r scibrief.zip SKILL.md
```

Verify the skill is loaded by asking Claude to list available skills.

## Usage

The skill triggers automatically on paper-summarization requests. All of these work:

- *"Summarize this paper"* (with a PDF attached)
- *"tldr on this"*
- *"What's the key finding here?"*
- *"Break this down for me"*
- Pasting an abstract, DOI, or paper title and asking for distillation

You can also invoke it explicitly with `/scibrief` in Claude Code.

**Not for:** literature reviews across many papers, systematic reviews, or meta-analyses — those need a different workflow.

### Optional: bind it to a keyword

Add a line to your Claude memory or `CLAUDE.md` so a prefix always triggers the skill:

```
When I type `tldr:` followed by a URL or paper, use the scibrief skill.
```

## Edge cases the skill handles

| Paper type | Adaptation |
|---|---|
| **Review** | 🧪 Approach → 📚 Scope; 📊 Key Numbers → 📌 Key Claims |
| **Preprint** | Adds `⚠️ Preprint — not peer reviewed` as the first caveat |
| **Methods paper** | 💡 Finding → 🔧 Capability; title leads with the method |
| **Consortium / multi-omics** | Focuses on the integrative finding; names the cohort |
| **Abstract only** | Flags the brief `[Abstract-only]` |

## PDF output

`SKILL.md` includes a Playwright + HTML recipe for rendering a brief as a styled A4 PDF with full emoji support, color-coded zones (warm for mechanism, cool for bottom line, red for caveats), and page headers/footers.

> **Note:** the PDF section hardcodes a Linux Chromium path
> (`/root/.cache/ms-playwright/...`). On macOS or Windows, drop the
> `executable_path` argument and let Playwright resolve its own browser, or point
> it at your local Chrome.

## Repository contents

```
scibrief/
├── SKILL.md    # the skill — frontmatter, workflow, output template, style rules
└── README.md   # this file
```

`SKILL.md` is self-contained; there are no scripts or reference files to install.

## Quality bar

Before emitting a brief, the skill checks that:

1. A PI could read it in 60 seconds and decide whether to read the full paper
2. The title line alone tells the whole story
3. Key Numbers contain actual numbers — not *"significantly increased"*
4. The Mechanism sentence is specific enough to generate a follow-up experiment
5. No zone repeats information from another zone
6. Total output stays under 200 words

## Contributing

Issues and PRs welcome — particularly new edge-case handling (clinical trial reports, structural biology, single-cell atlases) and additional output renderers.

## License

MIT
