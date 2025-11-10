## tools/srcpack-template.tex

```tex
% tools/srcpack-template.tex
\documentclass[a4paper,10pt]{article}
\usepackage[margin=2cm]{geometry}
\usepackage[T1]{fontenc}
\usepackage[utf8]{inputenc}
\usepackage{lmodern}
\usepackage[hidelinks]{hyperref}
\usepackage{listings}
\usepackage{xcolor}

\lstdefinestyle{src}{
  basicstyle=\ttfamily\small,
  numbers=left, numberstyle=\tiny, numbersep=8pt,
  breaklines=true, breakatwhitespace=true,
  showstringspaces=false, tabsize=2
}

% safer: only include if file exists; otherwise print a warning section
\newcommand{\SrcFile}[2]{%
  \InputIfFileExists{#1}{%
    \section*{#2}%
    \lstinputlisting[style=src]{#1}%
  }{%
    \section*{#2 (missing)}%
    \noindent\textit{File not found: \texttt{#1}}%
  }%
}

\newcommand{\PackTitle}{(set in pack file)}
\newcommand{\PackFiles}{\relax}

\begin{document}
\begin{center}
  {\Large \textbf{\PackTitle}}\\[6pt]
  \small Source listing (auto-generated for review)
\end{center}
\tableofcontents\newpage

\PackFiles
\end{document}
```

