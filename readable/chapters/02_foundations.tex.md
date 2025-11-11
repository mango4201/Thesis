## chapters/02_foundations.tex

```tex
\chapter{Foundations}\label{ch:foundations}
\section{Graphs, Trees, and Notation}\label{sec:found-notation}
% G=(V,E), costs c_e, cuts \delta(X), cycles, paths, spanning trees \mathcal{T}.
% Fundamental cycle C_e for e \notin T; fundamental cut \delta(X_e) for e \in T.

\section{Fundamental Cut Lemma}\label{sec:found-fundcut}
\begin{lemma}[Fundamental Cut Lemma]\label{lem:fund-cut}
Let $T$ be a spanning tree and $e\in E(T)$. Then $\delta(X_e)\cap E(T)=\{e\}$.
\end{lemma}
\begin{proof}
% Full proof here (tree splits into two components; any other tree edge in the cut reconnects them).
\end{proof}

\section{MST Optimality: Cycle and Cut Criteria}\label{sec:found-mst-criteria}
\begin{theorem}\label{thm:mst-criteria}
For a spanning tree $T$, the following are equivalent:
(i) $T$ is an MST; (ii) every non-tree edge is a most expensive edge on its fundamental cycle;
(iii) every tree edge is among the cheapest edges in its fundamental cut.
\end{theorem}
\begin{proof}[Proof sketch]
% Standard exchange arguments (cite textbooks).
\end{proof}

\section{Kruskal and Prim (Brief Recap)}\label{sec:found-kruskal-prim}
% One paragraph each; correctness references \Cref{thm:mst-criteria}.

\section{Uncertainty Sets and Robust Criteria (Definitions Only)}\label{sec:found-robust-defs}
% Discrete scenarios \mathcal{U}, interval bounds; define Min-Max and Min-Max Regret objectives formally.

\section{Running Micro-Graph and Notation Table}\label{sec:found-micro}
% Include small TikZ; if big symbol table, move to App. B and reference here.
```

