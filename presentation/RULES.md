# Presentation Thread Rules (v1)

Companion to the thesis project rules. Where nothing is stated here, the
thesis project rules apply unchanged (British English, no em dashes, never
invent citations, [CITATION NEEDED] if unsure, repo canonical, no
speculation or overclaims).

## Authority order
1. **Büsing presentation guidelines** (final say).
2. Department example deck (orientation only, not 1:1; known deviations
   from the guidelines, e.g. its figure captions, are NOT copied).
3. Thesis visual identity (RWTH colour palette, heading colours).
4. Taste, in service of presenting the results in the best light.

## Hard constraints
- Talk length: 30 minutes, tolerance -2/+0. Main deck is paced to 28.
  Anything that risks overrun lives in the appendix from the start
  (guidelines: never skip slides live; jump to appendix via hyperref
  only if time remains).
- 15 minutes of questions: appendix slides exist to serve them
  (cut/cycle criteria, FPTAS sketch, worked budgeted table, full
  micro-graph cost table).
- Language: English (thesis is English).
- Theme: Metropolis (explicitly suggested by the guidelines), restyled
  with the thesis palette. Frame numbers WITHOUT total, no progress bar
  (a bar leaks the x/n fraction the guidelines warn about), no
  navigation symbols, name small in the footer.
- Theorems/definitions in blocks, full sentences allowed only there;
  everything else itemised.
- No pseudocode. Algorithms appear as one-line remarks or pictures.
- No figure captions; literature sources appear as a small source line
  on the frame instead.
- Summary is the last main frame; final click adds "Thank you for your
  attention!" on that same frame. Reference slides follow after it.
- No outline slide.

## Content rules (thesis coupling)
- The thesis repo is canonical; slides are downstream. Every
  mathematical claim on a frame traces to a verified thesis result;
  each frame carries a comment with the thesis label and the literature
  source (e.g. `% Source: thm:mm-k2-hard; Goerigk Thm 8.4`).
- Proof stance carried over: what is shown as a proof on slides is a
  compression of a verified thesis proof. No proof structure is
  invented for the talk.
- If presentation work produces a new insight, it becomes an
  out-of-block action for the thesis threads, subject to Archit's
  approval. Never a slide-only claim, never silent.
- All numbers on slides are recomputed or read verbatim from the repo
  files (micro-graph data: chapters/02_foundations.tex). Nontrivial
  numeric claims get an independent Python check before freezing.
- `notation.tex` and (once decided) `references.bib` are mirrored
  copies of the thesis files. Never edited here; re-mirrored from the
  repo. Acceptance check: mirror diff is empty.
- Thesis TikZ figures are never modified in the repo. The presentation
  may keep ADAPTED COPIES (resized, recoloured, overlay-split) inside
  presentation/, each marked with its origin figure label.
- Checker discipline carried over: nontrivial slide claims and the
  frozen deck go through an independent checker thread before the
  rehearsal stage.

## Workflow in this thread
- Unit of work: the **act** (talk section). Flow Card per act, approved
  before frames are drafted. Frame drafting follows the checklist
  below. Flow Summary after each act is integrated.
- Deck freeze order: content frozen -> checker pass -> click-timing
  polish -> rehearsal notes.

## Timing budget (28 min target)
| Act | Frames (main deck) | Minutes |
|---|---|---|
| Title + hook | 2 | 2 |
| S1 Setting (micro-graph, models, objectives) | 4 | 7 |
| S2 Min-max (interval proof, K=2 reduction, landscape, budgeted idea) | 7 | 12 |
| S3 Min-max regret (mirrors S2, blocked on Ch4) | 4 | 6 |
| Summary (+ thank-you click) | 1 | 1 |
| Total | ~18 | 28 |

Reveal density calibrated on the example deck: plan 4-8 clicks on proof
and reduction frames, fewer elsewhere.

## Frame-draft checklist (analogue of the thesis section checklist)
1. State the frame's single takeaway in one sentence; two takeaways
   means two frames.
2. List what the frame assumes; it must already have appeared on an
   earlier frame (no forward references in a talk).
3. Trace every mathematical statement to a thesis label + literature
   source and record both in a frame comment.
4. Plan the clicks: number them, target <= 8. Use \visible / \onslide
   for reveals; \only ONLY for deliberate in-place replacement. Nothing
   may jump between clicks (guidelines).
5. Itemise; full sentences only inside theorem blocks.
6. Visual first: if the frame has no TikZ or table, justify why not.
7. Verify all numbers against repo data; flag anything needing a fresh
   enumeration check.
8. Add the source line if literature is used; no captions.
9. Write the spoken transition to the next frame as a comment (one or
   two sentences); these become the rehearsal script.
10. Acceptance: clean compile, no overfull hbox > 2pt on the frame,
    frame number visible without total, act stays inside its timing
    budget.

## Acceptance checks per drop (whole deck)
- `latexmk -pdf` exits 0; no undefined references; no multiply-defined
  labels.
- No overfull hbox above 2pt in build log. Two warnings are
  whitelisted as verified-benign: (a) the constant 15.6pt overfull
  vbox at \maketitle (upstream Metropolis title-page quirk, nothing
  clipped); (b) pdfplumber reporting characters at coordinates around
  -1800/+2300pt on frames with pending overlays (beamer parks covered
  overlay text off-canvas; it is invisible by design).
- Mirror diff empty (`notation.tex` vs repo).
- Frame count consistent with timing table.
- Visual raster spot-check: title frame, one block frame, one TikZ
  frame, summary frame.

## Open decisions
- D1: bibliography mechanism for the reference slides (proposal: mirror
  thesis references.bib, biblatex author-year, print only slide-cited
  entries). Blocked on the thesis-side numeric-comp vs author-year
  discrepancy being resolved.
- D2: aspect ratio. Default 16:9 (more room for graph-beside-table
  layouts); switch to 4:3 with one option if the seminar room projector
  requires it. Confirm the room.
- D3: exact occasion wording on the title frame (example uses
  "Abschlussvortrag Mathematik Bachelor"); confirm with supervisor.
- D4: whether the budgeted-uncertainty act stays in the main deck or
  moves fully to the appendix once Ch4 content competes for minutes.
