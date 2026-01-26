## main.tex

```tex
\documentclass[12pt,a4paper]{report}

% --- Layout & language ---
\usepackage[margin=3cm]{geometry}
\usepackage[english]{babel}
\usepackage[T1]{fontenc}
\usepackage{lmodern}

% --- Math, graphics, tables ---
\usepackage{amsmath,amssymb,amsthm,mathtools}
\usepackage{graphicx}
\usepackage{booktabs}
\usepackage{enumitem}
\usepackage{csquotes}
\usepackage{tikz}
\usetikzlibrary{arrows.meta,positioning,calc,fit,decorations.pathreplacing}

% --- Colors ---
\usepackage{xcolor}
\definecolor{RWTHBlue}{RGB}{0,84,159}
\definecolor{RWTHBlue20}{RGB}{46,124,196}
\definecolor{TextGray}{RGB}{51,51,51}
\definecolor{RuleGray}{RGB}{189,189,189}
\definecolor{TOCGray}{RGB}{85,85,85}

% --- Captions & tables ---
\usepackage[labelfont=bf,font=small]{caption}
\renewcommand{\arraystretch}{1.15}

% --- ToC styling (optional, but you had it) ---
\usepackage{tocloft}
\renewcommand{\cfttoctitlefont}{\large\bfseries\color{RWTHBlue}}
\renewcommand{\cftsecfont}{\color{TOCGray}}
\renewcommand{\cftsubsecfont}{\color{TOCGray}}

% --- Headings ---
\usepackage{titlesec}
\titleformat{\section}{\Large\bfseries\scshape\color{RWTHBlue}}{\thesection}{0.7em}{}
\titleformat{\subsection}{\large\bfseries\color{RWTHBlue20}}{\thesubsection}{0.6em}{}
\titleformat{\subsubsection}{\normalsize\bfseries\color{TOCGray}}{\thesubsubsection}{0.6em}{}
\titlespacing*{\section}{0pt}{1.4ex plus .4ex}{0.8ex}
\titlespacing*{\subsection}{0pt}{1.1ex plus .3ex}{0.6ex}
\titlespacing*{\subsubsection}{0pt}{1.0ex plus .2ex}{0.4ex}

% --- Footer page numbers ---
\usepackage{fancyhdr}
\pagestyle{fancy}
\fancyhf{}
\cfoot{\color{TOCGray}\thepage}
\renewcommand{\headrulewidth}{0pt}

% --- Bibliography ---
\usepackage[backend=biber,style=authoryear,maxbibnames=6]{biblatex}
\addbibresource{references.bib}

% --- Hyperlinks & clever references ---
\usepackage[hidelinks,colorlinks=true,linkcolor=RWTHBlue,citecolor=RWTHBlue20,urlcolor=RWTHBlue]{hyperref}
\usepackage[nameinlink,capitalise,noabbrev]{cleveref} % <-- add this after hyperref

% --- Theorems ---
\newtheorem{definition}{Definition}
\newtheorem{theorem}{Theorem}
\newtheorem{lemma}{Lemma}
\newtheorem{proposition}{Proposition}
\theoremstyle{remark}
\newtheorem{remark}{Remark}

% --- Notation macros ---
\input{notation}


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
\input{appendices/A_notation_table}         % move big symbol table here if needed
% \input{appendices/B_extra_figs}           % optional

% ===== BIBLIOGRAPHY =====
\cleardoublepage
\printbibliography

% ===== AFFIDAVIT =====
\cleardoublepage
\input{frontmatter/affidavit}
\end{document}






```

