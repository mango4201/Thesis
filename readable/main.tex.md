## main.tex

```tex
\documentclass[12pt,a4paper]{report}

% --- Layout & language ---
\usepackage[margin=3cm]{geometry}
\usepackage[british]{babel}
\usepackage[T1]{fontenc}
\usepackage{lmodern}
\usepackage{microtype}

% --- Page-breaking penalties (report sets \raggedbottom, so pages are never stretched) ---
\clubpenalty=10000
\widowpenalty=10000
\displaywidowpenalty=10000
\brokenpenalty=10000

% --- Math, graphics, tables ---
\usepackage{amsmath,amssymb,amsthm,mathtools}
\usepackage{graphicx}
\usepackage{array}
\usepackage{booktabs}
\usepackage{longtable}
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

% Additional RWTH Corporate Design colors (for pedagogical clarity)
\definecolor{RWTHOrange}{RGB}{246,168,0}        % RWTH Orange (accent)
\definecolor{RWTHRed}{RGB}{204,7,30}            % RWTH Red (emphasis)
\definecolor{RWTHLightBlue}{RGB}{142,186,229}   % Light Blue (backgrounds)
\definecolor{RWTHPetrol}{RGB}{0,97,101}         % Petrol (alternative)
\definecolor{RWTHGreen}{RGB}{87,171,39}         % Green (positive emphasis)

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

% --- Bibliography (UPDATED TO NUMERIC-COMP) ---
\usepackage[backend=biber,style=numeric-comp,sorting=none,maxbibnames=6]{biblatex}
\addbibresource{references.bib}

% --- Hyperlinks & clever references ---
\usepackage[colorlinks=true,linkcolor=RWTHBlue,citecolor=RWTHBlue20,urlcolor=RWTHBlue]{hyperref}
\usepackage[nameinlink,capitalise,noabbrev]{cleveref} % <-- add this after hyperref

% --- Theorems ---
\newtheorem{definition}{Definition}[chapter]
\newtheorem{theorem}{Theorem}[chapter]
\newtheorem{lemma}{Lemma}[chapter]
\newtheorem{corollary}{Corollary}[chapter]
\newtheorem{proposition}{Proposition}[chapter]
\theoremstyle{remark}
\newtheorem{remark}{Remark}[chapter]

% --- Thesis metadata (single source of truth: the title page and the PDF
%     properties both read these, so a title change propagates to both) ---
\newcommand{\thesisTitleMain}{Robust Spanning Trees}
\newcommand{\thesisTitleSubA}{under Discrete and Interval Uncertainty}
\newcommand{\thesisTitleSubB}{Min-Max and Min-Max Regret Models}
\newcommand{\thesisAuthor}{Archit Dhama}
% TODO before submission: replace \today with the actual submission date,
%   e.g. \newcommand{\thesisDate}{15th September 2026}
\newcommand{\thesisDate}{\today}
\newcommand{\thesisTitleFull}{\thesisTitleMain\ \thesisTitleSubA: \thesisTitleSubB}
\hypersetup{pdftitle={\thesisTitleFull},pdfauthor={\thesisAuthor}}

% --- Notation macros ---
\input{notation}


\begin{document}

% ===== TITLE PAGE =====
\input{00_titlepage}

% ===== FRONT MATTER =====
\pagenumbering{roman}
\setcounter{page}{1}

\input{frontmatter/abstract}

\tableofcontents

% ===== MAIN MATTER =====
\cleardoublepage
\pagenumbering{arabic}

\input{chapters/01_introduction}
\input{chapters/02_foundations}
\input{chapters/03_minmax}
\input{chapters/04_regret}
\input{chapters/05_conclusion}    % Merged from old Ch5 (synthesis) and Ch6 (conclusion)

% ===== APPENDICES =====
\appendix
\input{appendices/A_notation_table}         % move big symbol table here if needed

% ===== BIBLIOGRAPHY =====
\cleardoublepage
\printbibliography

% ===== DECLARATION ON THE USE OF AI TOOLS =====
\cleardoublepage
\input{frontmatter/ai_declaration}
\end{document}```

