## main.tex

```tex
\documentclass[12pt,a4paper]{report}   % or: scrreprt (KOMA)

\usepackage[margin=3cm]{geometry}
\usepackage[hidelinks]{hyperref}
\usepackage{graphicx}
\usepackage{booktabs}
\usepackage{amsmath,amssymb,amsthm}
\usepackage{mathtools}
\usepackage{enumitem}
\usepackage{csquotes}
\usepackage[backend=biber,style=authoryear,maxbibnames=6]{biblatex}
\addbibresource{references.bib}

% language: English only
\usepackage[english]{babel}

% optional: page numbers centered at bottom (like many examples)
\usepackage{fancyhdr}
\pagestyle{fancy}
\fancyhf{}
\cfoot{\thepage}
\renewcommand{\headrulewidth}{0pt}

% TikZ
\usepackage{tikz}
\usetikzlibrary{arrows.meta,positioning,calc,fit,decorations.pathreplacing}

% ---- colors (RGB, works everywhere)
\usepackage{xcolor} % must be loaded before \definecolor
\definecolor{RWTHBlue}{RGB}{0,84,159}    % primary
\definecolor{RWTHBlue20}{RGB}{46,124,196}% lighter accent
\definecolor{TextGray}{RGB}{51,51,51}
\definecolor{RuleGray}{RGB}{189,189,189}
\definecolor{TOCGray}{RGB}{85,85,85}

% links
\usepackage{hyperref}
\hypersetup{
  colorlinks=true,
  linkcolor=RWTHBlue,
  citecolor=RWTHBlue20,
  urlcolor=RWTHBlue
}

% headings
\usepackage{titlesec}
\titleformat{\section}{\large\bfseries\scshape\color{RWTHBlue}}{\thesection}{0.6em}{}
\titleformat{\subsection}{\normalsize\bfseries\color{RWTHBlue20}}{\thesubsection}{0.6em}{}
\titlespacing*{\section}{0pt}{1.0ex plus .3ex}{0.6ex}
\titlespacing*{\subsection}{0pt}{0.8ex plus .2ex}{0.4ex}

% footer page numbers
\usepackage{fancyhdr}
\pagestyle{fancy}
\fancyhf{}
\cfoot{\color{TOCGray}\thepage}
\renewcommand{\headrulewidth}{0pt}

% toc greys
\usepackage{tocloft}
\renewcommand{\cfttoctitlefont}{\large\bfseries\color{RWTHBlue}}
\renewcommand{\cftsecfont}{\color{TOCGray}}
\renewcommand{\cftsubsecfont}{\color{TOCGray}}

% captions + tables
\usepackage[labelfont=bf,font=small]{caption}
\usepackage{booktabs}
\renewcommand{\arraystretch}{1.15}


% theorem styles
\newtheorem{definition}{Definition}
\newtheorem{theorem}{Theorem}
\newtheorem{lemma}{Lemma}
\newtheorem{proposition}{Proposition}
\theoremstyle{remark}
\newtheorem{remark}{Remark}

\input{notation}

% Bigger, cleaner headings
\usepackage{titlesec}

% Sections ≈ 14.4pt (Large), small caps, RWTHBlue
\titleformat{\section}
  {\Large\bfseries\scshape\color{RWTHBlue}}{\thesection}{0.7em}{}

% Subsections ≈ 12pt (large)
\titleformat{\subsection}
  {\large\bfseries\color{RWTHBlue20}}{\thesubsection}{0.6em}{}

% Subsubsections ≈ 11pt (normalsize)
\titleformat{\subsubsection}
  {\normalsize\bfseries\color{TOCGray}}{\thesubsubsection}{0.6em}{}

% Slightly more breathing room above, tight below
\titlespacing*{\section}{0pt}{1.4ex plus .4ex}{0.8ex}
\titlespacing*{\subsection}{0pt}{1.1ex plus .3ex}{0.6ex}
\titlespacing*{\subsubsection}{0pt}{1.0ex plus .2ex}{0.4ex}


\begin{document}

% ===== TITLE PAGE =====
\input{00_titlepage}

% ===== FRONT MATTER =====
\pagenumbering{roman}
\setcounter{page}{1}

\input{frontmatter/abstract}           % required (EN)
% \input{frontmatter/kurzfassung}      % optional (DE)
% \input{frontmatter/acknowledgements} % optional

\tableofcontents
% \listoffigures                       % enable if many figures
% \listoftables                        % enable if many tables

% ===== MAIN MATTER =====
\cleardoublepage
\pagenumbering{arabic}

\input{chapters/01_introduction}
\input{chapters/02_foundations}
\input{chapters/03_minmax}
\input{chapters/04_regret}
\input{chapters/05_synthesis}
\input{chapters/06_conclusion}

% ===== APPENDICES =====
\appendix
\input{appendices/A_representative_proof}   % optional but recommended
\input{appendices/B_notation_table}         % move big symbol table here if needed
% \input{appendices/C_extra_figs}           % optional

% ===== BIBLIOGRAPHY =====
\cleardoublepage
\printbibliography

% ===== AFFIDAVIT =====
\cleardoublepage
\input{frontmatter/affidavit}
\end{document}






```

