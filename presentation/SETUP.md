# Setting up the presentation folder on your laptop

Goal: `presentation/` lives inside the thesis repo, builds locally for
fast click-tuning, and builds in CI like the thesis.

## 1. Folder layout in the repo

```
Thesis/                        (repo root, canonical)
├── main.tex
├── notation.tex
├── references.bib
├── chapters/ ...
└── presentation/
    ├── defence.tex            (the deck, single file)
    ├── notation.tex           (MIRROR of ../notation.tex, never edited here)
    ├── RULES.md               (thread rules, frame checklist, timing)
    ├── SETUP.md               (this file)
    └── figures/               (adapted TikZ copies, when they appear)
```

Steps:
1. `cd` into your local clone of `github.com/mango4201/Thesis`, then
   `git pull`.
2. `mkdir presentation` (and `presentation/figures` when first needed).
3. Copy `defence.tex`, `notation.tex`, `RULES.md`, `SETUP.md` from this
   thread's outputs into `presentation/`.
4. Commit with a `pres:` prefix so slide history stays separable from
   thesis history, e.g. `git commit -m "pres: initial deck skeleton"`.

## 2. Local LaTeX toolchain

You need a full TeX distribution once (Metropolis, beamer, TikZ,
latexmk are all included in the full schemes):

- Windows: install TeX Live (full scheme) from tug.org/texlive, or
  MiKTeX with on-the-fly package installation enabled.
- macOS: install MacTeX.
- Linux (Debian/Ubuntu): `sudo apt install texlive-full latexmk`.

Build commands, always from inside `presentation/`:

```
latexmk -pdf defence.tex        # one-shot build
latexmk -pvc -pdf defence.tex   # watch mode: rebuilds on save,
                                # ideal while tuning clicks
latexmk -C                      # clean auxiliary files
```

Editor: whatever you use for the thesis. If VS Code, the LaTeX
Workshop extension picks up latexmk automatically; point its root to
`presentation/defence.tex` when working here so it does not try to
build the thesis.

If you prefer to stay CI-only (edit, push, download artifact), that
works too, but click-timing iteration is painful without a local
build; local is strongly recommended for slide work.

## 3. The notation mirror (sync rule)

`presentation/notation.tex` must be byte-identical to the repo root
`notation.tex`. After ANY thesis-side notation change:

```
cp ../notation.tex notation.tex          # from inside presentation/
git diff --no-index ../notation.tex notation.tex   # must print nothing
```

The diff check is part of the per-drop acceptance checks (RULES.md).
New macros needed ONLY by slides do not go into the mirror; they go
into the deck preamble under the marked "presentation-only macros"
block, so the mirror stays clean.

## 4. CI build

Add a second job to your existing GitHub Actions workflow so every
push also builds the deck. If the thesis job uses the common
`xu-cheng/latex-action`, the addition looks like:

```yaml
  build-presentation:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: xu-cheng/latex-action@v3
        with:
          root_file: defence.tex
          working_directory: presentation
      - uses: actions/upload-artifact@v4
        with:
          name: defence-pdf
          path: presentation/defence.pdf
```

Adapt names/versions to whatever your current workflow file actually
uses (I have not seen it; it is not among the project files). Keep it
a separate job so a broken slide build never blocks the thesis build.
If you also commit PDF snapshots for the thesis, decide whether
`presentation/defence.pdf` snapshots follow the same convention; until
then, add `presentation/*.pdf` to `.gitignore` alongside the usual
latexmk clutter:

```
presentation/*.aux
presentation/*.fdb_latexmk
presentation/*.fls
presentation/*.log
presentation/*.nav
presentation/*.out
presentation/*.snm
presentation/*.toc
presentation/*.vrb
```

## 5. Working loop with this thread

1. Approve a Flow Card for an act here.
2. I draft the frames; you pull the drop into `presentation/`,
   compile locally, review on your screen (raster review here is a
   preview, your projector view is authoritative).
3. Run the acceptance checks from RULES.md, commit with `pres:`.
4. Nontrivial claims go to the checker thread before the deck freeze.
