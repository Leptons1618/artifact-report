---
name: artifact-report
description: Generate a styled, self-contained HTML report artifact for AI agents and humans, in one of three variants (paper / terminal / editorial). Use when the user asks to save a report, audit, review, checkup, smell scan, QA pass, or investigation as an artifact, a shareable HTML file, or a human-readable record with machine-readable data.
---

# Artifact Report

Single-file HTML reports. No build step, no external assets, no CDN, no
webfonts. Readable by humans in a browser, parseable by agents from the
embedded JSON block.

Pick a **variant** first ("Variant selection"), then build the shared anatomy
inside that variant's skin. All variants share one contract: verbatim
evidence, text-labeled severity, and a machine-readable JSON block that
agrees with the human text 1:1.

## Design concepts

Every variant obeys the same seven principles. Distilled from the Portfolio
design system (`DESIGN.md`) and the reference reports; non-negotiable.

1. **Documented, not decorated.** A report is a spec sheet, not a landing
   page. Flat surfaces, square corners (`border-radius: 0`), hairline rules.
   No gradients, no soft shadows. The dark variant's tinted finding cards are
   status signaling, not decoration.
2. **Data-shaped text is monospace.** IDs, dates, paths, selectors, scores,
   kickers, table headers, metadata, evidence — always the mono face. This is
   the strongest single signal that the document is technical rather than
   decorative.
3. **Severity is never color alone.** Color only reinforces a text label
   (`CRITICAL / MAJOR / MINOR / NOTE / PASS`). Every tinted surface carries
   the word.
4. **One accent per screen.** Each variant has a small fixed palette; status
   colors are the only chroma, and each marks a *class* of thing
   (block / warn / info / ok) — never a random highlight.
5. **Whitespace is load-bearing.** Large, even gaps between major blocks
   (`56–64px`); spacing in multiples of `8px`. Dense cards sit inside generous
   sections.
6. **Ornaments are structural.** Corner boxes, dashed cut-lines, `§` marks,
   and `//` kickers say "this document is specified and cut to size." Use
   them where the variant defines them; never invent more.
7. **Evidence over adjectives.** Every claim carries a verbatim quote or a
   measured value in a mono block. No evidence → severity `NOTE` and state
   what would confirm it.

## Variant selection

| Need | Variant | Look |
| --- | --- | --- |
| QA audit, investigation, evidence-first record; the print-friendly default | **paper** | white ground, ink, `§` dashed separators, solid-border cards with dashed cut-lines |
| Scored audit — checkup, review, smell scan, verdict + fixes | **terminal** | black ground, corner-boxed panels, giant verdict numeral, severity-tinted finding cards |
| Technical documentation, explainer, long-form record | **editorial** | warm paper, serif display, 3px masthead rule, hairline section rules |

When the user's phrasing maps to a CommandCode report type (checkup, review,
smell), use **terminal**. When unsure, use **paper**.

## Shared report anatomy (in order)

1. **Header:** kicker (`REPORT · <topic>`, or `// <topic>` in terminal),
   `h1` title, meta line (date, scope, author/agent, method, sources).
2. **Verdict / summary card:**
   - terminal → score band (giant numeral + verdict chip) plus a two-column
     TL;DR (`0.8fr / 1.2fr`): prose verdict, then a "Next:" line.
   - paper / editorial → 3–8 bullet takeaways. Conclusions only, no evidence.
3. **Contents:** anchor list of sections (`§1 … §n`).
4. **Sections:** one `h2` per section, each opened the variant's way.
5. **Findings** (issues/observations), each carrying, in this order:
   `ID`, `Severity`, `Observation` (one sentence), `Evidence` (mono block,
   verbatim quotes/log lines), `Location` (URL, file path, selector) — and in
   terminal, a one-sentence `FIX:` callout with an exact file:line or selector.
6. **Machine block:** `<script type="application/json" id="report-data">`
   with `{"title","date","scope","summary":[],"findings":[{"id","severity",
   "section","observation","evidence","location"}],"stats":{}}`.
   Human sections and JSON findings must agree 1:1.

## Shared rules

- Single `.html` file, inline `<style>` only. No `<link>`, no `<script>`
  except the `report-data` JSON block. No CDN (no Tailwind runtime), no
  remote images, no webfonts — system stacks only.
- Every claim carries verbatim evidence in a mono block. No evidence → mark
  severity `NOTE` and say what would confirm it.
- Severity scale: `CRITICAL` (down / data loss / blocks use) · `MAJOR`
  (broken on a supported viewport or flow) · `MINOR` (polish / copy /
  weight) · `NOTE` (observation, needs confirmation) · `PASS` (verified
  working — use a "verified" section or green-labeled list, never a silent
  omission).
- Dark variant must print sensibly: force `@media print` to swap to
  `background:#fff; color:#111`; keep evidence blocks `white-space:pre-wrap`.
- Every color on the page must pass 4.5:1 contrast against its surface;
  status colors never carry meaning alone (the word is always present).

## Variant: paper (default)

White paper, ink, hairlines. One `1px solid` outer border per card, one
`1px dashed` inner cut-line after each card header. Sections open with the
`§` separator (a mono `§` glyph + dashed rule). Severity is text-only — no
color anywhere. Evidence blocks are light gray wells. Close with a mono
footer line (date, method, boundary, pointer to `#report-data`).

```css
:root{--ink:#111;--sub:#666;--line:#ddd;--paper:#fff;--mono:ui-monospace,SFMono-Regular,Menlo,Consolas,monospace}
*{box-sizing:border-box}body{margin:0;background:var(--paper);color:var(--ink);font:16px/1.6 -apple-system,"Segoe UI",Roboto,sans-serif}
.wrap{max-width:1120px;margin-inline:auto;padding:0 24px}
@media(max-width:640px){.wrap{padding:0 20px}}
header.report{padding:64px 0 0}.kicker{font:12px/1 var(--mono);letter-spacing:.1em;text-transform:uppercase;color:var(--sub)}
h1{font-size:32px;line-height:1.2;margin:8px 0}h2{font-size:22px;margin:0 0 8px}
.meta{font:13px var(--mono);color:var(--sub)}
.sep{display:flex;align-items:center;gap:12px;margin:56px 0 24px;color:var(--sub)}
.sep::before{content:"§";font-family:var(--mono)}
.sep::after{content:"";flex:1;border-top:1px dashed var(--sub)}
.card{border:1px solid var(--ink);padding:24px;margin:0 0 16px}
.card-head{margin:0 0 12px}.card-id{font:12px var(--mono);letter-spacing:.1em;text-transform:uppercase;color:var(--sub)}
.card-title{font-size:18px;margin:4px 0}
.cut{border:0;border-top:1px dashed var(--sub);margin:12px 0}
.evidence{font:13px/1.6 var(--mono);background:#f6f6f6;border:1px solid var(--line);padding:12px;white-space:pre-wrap;word-break:break-word}
.sev{font:12px var(--mono);letter-spacing:.1em}
.toc{list-style:none;padding:0}.toc a{color:inherit}
footer{padding:0 0 64px;color:var(--sub);font:13px var(--mono)}
```

## Variant: terminal

Black ground `#000`, panels `#0a0a0b` on `1px solid #222226` borders, and the
signature **corner boxes**: two 20px squares straddling a panel's top corners,
half outside the border. Kickers are literal `// TOPIC` in mono. Verdicts are
giant numerals with a tinted status chip; findings are severity-tinted cards
ending in an amber `FIX:` callout; a two-column verification block
(`// RAN` / `// NOT VERIFIED`) precedes a centered mono footer.

Panel skeleton (every panel carries its corner pair):

```html
<section class="panel">
  <i class="corner tl" aria-hidden="true"></i><i class="corner tr" aria-hidden="true"></i>
  <div class="kicker">// TOPIC</div>
  <!-- content -->
</section>
```

Severity tints — fixed mapping, never invent colors:

| Label | Tag / callout color | Card class | Chip |
| --- | --- | --- | --- |
| CRITICAL / BLOCK | `#f43f5e` | `.f-crit` | `.chip.block` |
| MAJOR / MEDIUM | `#fbbf24` | `.f-warn` | `.chip.warn` |
| MINOR / LOW | `#94a3b8` | `.f-info` | — |
| NOTE | `#666` | default panel | — |
| PASS | `#34d399` | — | `.chip.ok` |

```css
:root{--bg:#000;--panel:#0a0a0b;--line:#222226;--row:#1a1a1e;--fg:#fafafa;--body:#a0a0a0;--dim:#666;--faint:#444;--crit:#f43f5e;--warn:#fbbf24;--info:#94a3b8;--ok:#34d399;--mono:ui-monospace,SFMono-Regular,Menlo,Consolas,monospace}
*{box-sizing:border-box}body{margin:0;background:var(--bg);color:var(--fg);font:16px/1.6 Inter,ui-sans-serif,system-ui,sans-serif}
.wrap{max-width:1152px;margin-inline:auto;padding:32px 20px}
@media(min-width:768px){.wrap{padding:48px 40px}}
.panel{position:relative;border:1px solid var(--line);background:var(--panel);padding:24px;margin:0 0 32px}
@media(min-width:768px){.panel{padding:32px}}
.corner{position:absolute;width:20px;height:20px;border:1px solid var(--line);background:#000}
.tl{left:0;top:0;transform:translate(-50%,-50%)}.tr{right:0;top:0;transform:translate(50%,-50%)}
.kicker{font:12px var(--mono);letter-spacing:.18em;color:var(--dim);margin:0 0 8px}
h1{font-size:clamp(36px,6vw,60px);font-weight:700;letter-spacing:-.02em;line-height:1.05;margin:0}
.sub{font:12px var(--mono);letter-spacing:.18em;color:var(--dim);margin-top:12px}
.stamp{font:12px var(--mono);color:var(--dim)}
@media(min-width:768px){.stamp{text-align:right}}
h2{font-size:18px;font-weight:600;margin:0 0 8px}
.lead{font-size:14px;line-height:1.6;color:var(--body)}
.band{display:grid;gap:32px}
@media(min-width:768px){.band{grid-template-columns:minmax(0,.8fr) minmax(0,1.2fr)}}
.score{font-size:clamp(48px,8vw,72px);font-weight:700;letter-spacing:-.02em;line-height:1}
.of{margin-top:4px;font-size:18px;color:var(--dim)}
.chip{display:inline-block;margin-top:16px;padding:4px 12px;border:1px solid;font-size:14px}
.chip.block{color:var(--crit);border-color:rgba(244,63,94,.3);background:rgba(244,63,94,.1)}
.chip.warn{color:var(--warn);border-color:rgba(251,191,36,.3);background:rgba(251,191,36,.1)}
.chip.ok{color:var(--ok);border-color:rgba(52,211,153,.3);background:rgba(52,211,153,.1)}
table{width:100%;min-width:720px;border-collapse:collapse;font-size:14px}
th{border-bottom:1px solid var(--line);text-align:left;font:12px var(--mono);font-weight:400;color:var(--dim);padding:12px 16px 12px 0;text-transform:uppercase;letter-spacing:.08em}
td{border-bottom:1px solid var(--row);padding:16px 16px 16px 0;vertical-align:top}
.num{color:#555}
.finding{border:1px solid var(--line);background:var(--panel);padding:20px}
.finding+.finding{margin-top:16px}
.f-crit{border-color:#2a1420;background:#140a0e}.f-warn{border-color:#2a2416;background:#110e0b}.f-info{border-color:#2a1d22;background:#110b0d}
.f-tag{font:12px var(--mono);color:var(--warn);margin-bottom:8px;letter-spacing:.08em}
.f-crit .f-tag{color:var(--crit)}.f-info .f-tag{color:var(--info)}
.finding h3{font-size:18px;font-weight:600;margin:0 0 8px}
.fix{margin-top:16px;padding-left:16px;border-left:1px solid var(--warn);color:var(--warn);font-size:14px;line-height:1.6}
.f-crit .fix{border-color:var(--crit);color:var(--crit)}
.evidence{margin-top:12px;padding:12px;border:1px solid var(--line);background:#000;font:13px/1.6 var(--mono);color:var(--body);white-space:pre-wrap;word-break:break-word}
.v-grid{display:grid;gap:24px}
@media(min-width:768px){.v-grid{grid-template-columns:1fr 1fr}}
.v-ok .kicker{color:var(--ok)}.v-nv .kicker{color:var(--warn)}
ul{margin:0;padding-left:20px;font-size:14px;color:var(--body)}
footer{padding:32px 0;text-align:center;font:12px var(--mono);letter-spacing:.18em;color:var(--faint)}
@media print{body{background:#fff;color:#111}}
```

## Variant: editorial

Warm paper `#fcfbf8`, ink `#1a1712`, a single rust accent (`#b4501e`, deep
`#8a3a12`). Serif display (Georgia stack — no webfonts) for the masthead and
section heads; sans body; mono for code, labels, and table headers. The
masthead closes with a 3px ink rule; every `h2` opens with a 1px hairline.
Standfirst ≤ 62ch, body measure ≤ 72ch. Best for documentation and
long-form records where findings are explanatory, not scored.

```css
:root{--ink:#1a1712;--paper:#fcfbf8;--surface:#f5f2ea;--accent:#b4501e;--accent-deep:#8a3a12;--divider:#e5dfd2;--mono:ui-monospace,"SF Mono",Menlo,Consolas,monospace;--sans:system-ui,-apple-system,"Segoe UI",sans-serif}
*{box-sizing:border-box}
body{margin:0;background:var(--paper);color:var(--ink);font-family:var(--sans);font-size:16px;line-height:1.65}
main{max-width:960px;margin:0 auto;padding:48px 24px 96px}
.masthead{border-bottom:3px solid var(--ink);padding-bottom:24px;margin-bottom:40px}
.eyebrow{font-family:var(--mono);font-size:12px;letter-spacing:.1em;text-transform:uppercase;color:var(--accent-deep);margin:0 0 8px}
h1{font-family:Georgia,"Times New Roman",serif;font-weight:400;font-size:clamp(32px,5vw,48px);line-height:1.15;margin:0 0 8px}
.standfirst{font-size:18px;color:#57503f;max-width:62ch}
h2{font-family:Georgia,serif;font-weight:400;font-size:28px;margin:56px 0 4px;padding-top:24px;border-top:1px solid var(--divider)}
h3{font-weight:650;font-size:17px;margin:32px 0 8px}
p,ul,ol{max-width:72ch}
code{font-family:var(--mono);font-size:.85em;background:var(--surface);border:1px solid var(--divider);padding:1px 6px;white-space:nowrap}
pre{background:var(--surface);border:1px solid var(--divider);padding:16px;overflow:auto;font-size:13px;line-height:1.6}
table{border-collapse:collapse;width:100%;margin:16px 0 24px;font-size:14.5px}
th{text-align:left;font-family:var(--mono);font-size:11.5px;letter-spacing:.08em;text-transform:uppercase;color:#7a7261;border-bottom:2px solid var(--ink);padding:8px 16px 8px 0}
td{border-bottom:1px solid var(--divider);padding:10px 16px 10px 0;vertical-align:top}
```

## Modules (compose onto any variant)

- **Score band** (terminal): `// OVERALL` kicker, giant numeral, `/ max`,
  verdict chip. One per report, first card after the header.
- **Verdict table**: mono uppercase headers (`# / LENS / SCORE / KEY
  FINDING`), hairline rows, dimmed leading index column.
- **Verification block**: two columns — `// RAN` (green kicker) and
  `// NOT VERIFIED` (amber kicker). Include whenever the report asserts
  verification: what was executed vs. what was inferred or skipped.
- **FIX callout**: one actionable sentence per finding, referencing an exact
  file:line, selector, or route. Terminal tints it; paper and editorial
  prefix the literal bold `FIX:`.
- **Evidence block**: mono, `white-space:pre-wrap`, verbatim only — never
  paraphrase; truncate only with an explicit ellipsis note.

## Steps

1. Pick the variant from "Variant selection"; collect claims + verbatim
   evidence; decide severity per finding.
2. Write sections, then findings, then verdict/summary, then the JSON block.
3. Verify: file opens from disk, anchors work, JSON parses, findings match
   the human text 1:1, and every colored status carries its word.
4. Report the saved path back to the user.
