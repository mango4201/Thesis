## chapters/04_regret.tex

```tex
\chapter{Min-Max Regret Spanning Tree}\label{ch:regret}
\section{Regret Definition and Objective}\label{sec:regret-def}
% Regret(T,u) = c^{(u)}(T) - \min_{T'\in\mathcal{T}} c^{(u)}(T').

\section{Models}\label{sec:regret-models}
\subsection{Discrete Scenarios}\label{sub:regret-discrete}
% Formalization; pointer to compact formulations (cite).
\subsection{Interval Uncertainty}\label{sub:regret-interval}
% Define how the worst-case regret scenario is constructed (bounds).

\section{Short Properties Used Later}\label{sec:regret-properties}
\begin{lemma}[Interval extremal regret for a fixed tree]\label{lem:regret-extremal}
For interval costs, the worst-case regret of a fixed tree is attained at bound assignments.
\end{lemma}
\begin{proof}
% Difference of linear problems over a box has extremal optimum.
\end{proof}

\section{Reported Complexity and Approximability}\label{sec:regret-complexity}
\subsection{Discrete $K$: constant vs.\ unbounded}\label{sub:regret-k}
% State results; cite.
\subsection{Intervals: NP-hardness; 2-approximation}\label{sub:regret-interval-hard}
% State the 2-approx guarantee succinctly; cite.

\section{Modeling Pointers / Reformulations}\label{sec:regret-modeling}
% Cite compact/dual ideas; no derivations.

\section{Worked Micro-Graph Example}\label{sec:regret-example}
% Compute regrets and compare with Min-Max.

\section{Pointer to Appendix A (Representative Full Proof)}\label{sec:regret-appA} % optional
```

