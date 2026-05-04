## appendices/A_notation_table.tex

```tex
%═══════════════════════════════════════════════════════════
% APPENDIX A: NOTATION TABLE
% 
% Prerequisites: All chapters (references notation used throughout)
% Provides: Comprehensive symbol reference
% Labels created: app:notation
% Page budget: 1-2 pages (longtable handles page breaks)
% Status: IN PROGRESS (updated through Section 2.4)
%
% REQUIRES: \usepackage{longtable} in main.tex preamble
%           Add after \usepackage{booktabs}
%═══════════════════════════════════════════════════════════

\chapter{Notation}\label{app:notation}

This appendix provides a reference for the mathematical notation used throughout the thesis.
Symbols are grouped by category with the section of first use indicated.

\begin{longtable}{lll}
\toprule
\textbf{Symbol} & \textbf{Meaning} & \textbf{First Use} \\
\midrule
\endfirsthead

\toprule
\textbf{Symbol} & \textbf{Meaning} & \textbf{First Use} \\
\midrule
\endhead

\midrule
\multicolumn{3}{r}{\textit{Continued on next page}} \\
\endfoot

\bottomrule
\endlastfoot

\multicolumn{3}{l}{\textit{Graph Notation}} \\
$G = (V, E)$ & Undirected graph with vertex set $V$, edge set $E$ & \S\ref{sec:graph-notation} \\
$n = |V|$, $m = |E|$ & Number of vertices and edges & \S\ref{sec:graph-notation} \\
$\cut{X}$ & Cut induced by vertex set $X \subseteq V$ & \S\ref{sec:graph-notation} \\
\midrule

\multicolumn{3}{l}{\textit{Trees and Costs}} \\
$\cT$ & Set of all spanning trees of $G$ & \S\ref{sec:graph-notation} \\
$T$ & Spanning tree, $T \in \cT$ & \S\ref{sec:graph-notation} \\
$c \colon E \to \R$ & Edge cost function & \S\ref{sec:graph-notation} \\
$c(T)$ & Cost of tree $T$, i.e., $\sum_{e \in T} c_e$ & \S\ref{sec:graph-notation} \\
$\MSTcost{c}$ & Minimum spanning tree cost under $c$ & \S\ref{sec:graph-notation} \\
$C_f$ & Fundamental cycle of non-tree edge $f$ & \S\ref{sec:graph-notation} \\
$\cut{X_e}$ & Fundamental cut of tree edge $e$ & \S\ref{sec:graph-notation} \\
\midrule

\multicolumn{3}{l}{\textit{Uncertainty Models}} \\
$\Scenarios$ & Uncertainty set (discrete or interval) & \S\ref{sec:uncertainty} \\
$K$ & Number of discrete scenarios & \S\ref{sec:uncertainty} \\
$\cs{k}$ & Scenario $k$ cost vector, $k \in \{1, \ldots, K\}$ & \S\ref{sec:uncertainty} \\
$[\ell_e, u_e]$ & Interval cost bounds for edge $e$ & \S\ref{sec:uncertainty} \\
\midrule

\multicolumn{3}{l}{\textit{Robust Objectives}} \\
$\Regret{T}{c}$ & Regret of $T$ under $c$: $c(T) - \MSTcost{c}$ & \S\ref{sec:uncertainty} \\
\midrule

\multicolumn{3}{l}{\textit{Complexity}} \\
$\mathsf{P}$ & Polynomial-time complexity class & \S\ref{sec:complexity} \\
$\mathsf{NP}$ & Nondeterministic polynomial-time class & \S\ref{sec:complexity} \\
$\mathsf{NP}$-hard & At least as hard as $\mathsf{NP}$-complete problems & \S\ref{sec:complexity} \\
$\alpha$-approximation & Algorithm guaranteeing $\mathrm{ALG} \leq \alpha \cdot \opt$ & \S\ref{sec:complexity} \\
FPTAS & Fully polynomial-time approximation scheme & \S\ref{sec:complexity} \\

\end{longtable}

% END OF APPENDIX A```

