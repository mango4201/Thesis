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


\iffalse
Define graph notation, spanning trees; review MST algorithms (Kruskal/Prim). Introduce uncertainty sets \Scenarios\ (discrete scenarios); define Min-Max and Min-Max Regret objectives.


% === Section 2: Foundations (self-contained) ===
% Local safety: define theorem envs if not already available
\makeatletter
\@ifundefined{definition}{\newtheorem{definition}{Definition}[section]}{}
\@ifundefined{lemma}{\newtheorem{lemma}{Lemma}[section]}{}
\@ifundefined{theorem}{\newtheorem{theorem}{Theorem}[section]}{}
\makeatother

% bullets similar to the OptiB script
\setlist[itemize]{label=\textbullet,leftmargin=*}



\subsection{Graphs, Trees, Fundamental Cycles and Cuts}\label{subsec:graphs}
We consider a connected undirected graph \(G=(V,E)\) with edge costs \(c:E\to \mathbb{R}\).
A \emph{tree} is a connected, acyclic graph; a \emph{spanning tree} \(T=(V,E(T))\) contains all vertices.
For a vertex set \(X\subset V\), the (undirected) \emph{cut} is
\[\delta(X)=\{\,uv\in E\mid u\in X,\ v\notin X\,\}.\]

\medskip
\noindent\textbf{Fundamental cycle.}
Let \(T\) be a spanning tree and \(e=uv\in E\setminus E(T)\) a non-tree edge.
Since \(T\) contains a unique \(u\text{--}v\) path \(p\), the graph \(T+e\) has a unique cycle,
called the \emph{fundamental cycle} \(C_e\) of \(e\) with respect to \(T\).
(The uniqueness follows by removing \(e\) and arguing that otherwise two distinct \(u\text{--}v\) paths would remain in \(T\).)
This is the presentation used in the lecture script, cf.\ Fig.~2.4 and Lemma~29 (Cycle Criterion). % OptiB style
%
% (reference only for you; not a citation)
% see Skript OptiB, Sec. 2.2

\medskip
\noindent\textbf{Fundamental cut (OptiB notation).}
Let \(e=uv\in E(T)\) be a tree edge and let \(X_e\subset V\) denote the set of vertices
that are reachable from \(u\) in \(T-e\). The \emph{fundamental cut} of \(e\) with respect
to \(T\) is the cut \(\delta(X_e)\).
We record the two basic facts used later.

\begin{lemma}[Fundamental Cut-Lemma]\label{lem:fundcut}
Let \(T\) be a spanning tree and \(e\in E(T)\).
Then the induced cut \(\delta(X_e)\) is well-defined and
\(e\) is the unique tree edge in \(\delta(X_e)\), i.e.\ \(\delta(X_e)\cap E(T)=\{e\}\).
\end{lemma}
\begin{proof}
Deleting \(e=uv\) splits \(T\) into two components, hence \(\emptyset \neq X_e \subsetneq V\) and \(\delta(X_e)\) is a cut.
For uniqueness, assume \(e'\!=\!xy\in E(T)\cap \delta(X_e)\) with \(e'\neq e\) and w.l.o.g.\ \(x\in X_e\), \(y\notin X_e\).
By definition of \(X_e\), there is a path from \(u\) to \(x\) in \(T-e\). Together with the edge \(e'=xy\),
this yields a path from \(u\) to \(y\) in \(T-e\), so \(y\in X_e\), a contradiction.
\end{proof}
% Matches Skript OptiB's Lemma 31, Figs. 2.9--2.11.

\begin{theorem}[Optimality criteria for MST]\label{thm:optcrit}
For a spanning tree \(T\) the following are equivalent:
\begin{itemize}
  \item[(i)] \(T\) is a minimum spanning tree (MST).
  \item[(ii)] \textbf{Cycle-criterion:} For every non-tree edge \(e\), \(e\) is one of the most expensive edges on \(C_e\).
  \item[(iii)] \textbf{Cut-criterion:} For every tree edge \(e\), \(e\) is one of the cheapest edges in the fundamental cut \(\delta(X_e)\).
\end{itemize}
\end{theorem}
\begin{proof}[Proof sketch]
\((i)\Rightarrow(ii)\) and \((ii)\Rightarrow(iii)\) follow by standard exchange arguments using \(C_e\) and \(\delta(X_e)\);
\((iii)\Rightarrow(i)\) is analogous. We include full detailed proofs in Appendix~A to keep the flow of Section~\ref{sec:foundations}.
\end{proof}
% In your final version, you can inline the full proofs if desired.

\subsection{Linear/Integer Programming Background Used Later}\label{subsec:ip}
We shall use two facts: (a) LP duality for compact reformulations, and (b) the integrality
of the MST polytope (spanning-tree LP), which allows us to replace a minimization over trees
by an LP without integrality loss in Section~\ref{subsec:robust}. We refrain from a full primer
and only recall the statements we actually use later.

\subsection{Robustness for Spanning Trees}\label{subsec:robust}
Let \(U\) be an uncertainty set for edge-cost vectors \(c\in \mathbb{R}^E_{\ge 0}\).
We focus on two objectives:
\begin{align*}
  &\textbf{Min--max ST:} && \min_{T\in \mathcal{T}}\ \max_{c\in U}\ \sum_{e\in T} c_e,\\
  &\textbf{Min--max regret ST:} && \min_{T\in \mathcal{T}}\ \max_{c\in U}\ \Big(\sum_{e\in T} c_e - \mathrm{OPT}_{\mathrm{MST}}(c)\Big).
\end{align*}

\paragraph{Uncertainty sets.}
We will consider (i) discrete scenarios \(U=\{c^1,\dots,c^K\}\) and, in depth,
(ii) \emph{interval uncertainty} \(U=\prod_{e\in E}[\underline c_e,\overline c_e]\).

\begin{lemma}[Worst-case scenario for intervals]\label{lem:wcs}
Fix a tree \(T\) with incidence \(x\in\{0,1\}^E\).
For interval uncertainty, a worst-case scenario for the regret of \(T\) is given by
\[
  c^x_e=\begin{cases}
  \overline c_e,&x_e=1,\\
  \underline c_e,&x_e=0,
  \end{cases}
\]
i.e.\ it takes upper bounds on chosen edges and lower bounds on all others.
\end{lemma}
\begin{proof}
Write the regret as \(\sum_e c_e x_e - \min_{y\in X}\sum_e c_e y_e\), where \(X\) is the set of MST-incidence vectors.
For fixed \(x\), this is separable in each \(c_e\) and linear over the box \([\underline c_e,\overline c_e]\).
The term multiplies \(c_e\) by \(x_e-y_e\) for some feasible \(y\in X\), so choosing \(c_e=\overline c_e\) if \(x_e=1\)
and \(c_e=\underline c_e\) if \(x_e=0\) maximizes the difference coordinate-wise. Formalizing yields the claim.
\end{proof}

Using Lemma~\ref{lem:wcs} we obtain a convenient max--min form.
Let \(X\) be the set of MST incidence vectors (integral by the spanning-tree polytope). Then
\begin{equation}\label{eq:regret-reform}
\min_{x\in X}\ \max_{y\in X}\ \sum_{e\in E}\overline c_e\,x_e\ -\ \sum_{e\in E}\big(\underline c_e+(\overline c_e-\underline c_e)x_e\big)\,y_e.
\end{equation}
Because the inner minimization over MSTs is an LP (by integrality), one can dualize it
to obtain a compact MIP for min--max regret ST; we will use this later in Section~\ref{sec:minmaxregret}.

\subsection{Complexity and Approximation Terminology}\label{subsec:complexity}
We briefly recall the terms NP-hardness, approximation ratio \(\alpha\), and the notions PTAS/FPTAS,
only to classify the results presented later. Full proofs are beyond the scope of Section~\ref{sec:foundations}.

\subsection{Notation Table and a Running Micro-Example}\label{subsec:notation}
\paragraph{Notation.}
\begin{center}
\begin{tabular}{@{}ll@{}}
\toprule
Symbol & Meaning \\
\midrule
\(G=(V,E)\) & Connected undirected graph \\
\(\mathcal{T}\) & Set of all spanning trees of \(G\) \\
\(E(T)\) & Edge set of a tree \(T\) \\
\(\delta(X)\) & Cut induced by \(X\subset V\) \\
\(C_e\) & Fundamental cycle of non-tree edge \(e\) wrt.\ \(T\) \\
\(X_e\) & Vertex set reachable from the \(u\)-side in \(T-e\) for tree edge \(e=uv\) \\
\(c(T)=\sum_{e\in T} c_e\) & Cost of \(T\) under costs \(c\) \\
\(\mathrm{OPT}_{\mathrm{MST}}(c)\) & Cost of an MST in scenario \(c\) \\
\(R(T,c)=c(T)-\mathrm{OPT}_{\mathrm{MST}}(c)\) & Regret of \(T\) in scenario \(c\) \\
\(\underline c_e,\overline c_e\) & Interval bounds for edge \(e\) \\
\(c^x\) & Worst-case scenario against \(x\) as in Lemma~\ref{lem:wcs} \\
\bottomrule
\end{tabular}
\end{center}

\paragraph{Micro-example (interval regret).}
Consider four edges \(E=\{a,b,c,d\}\) with intervals
\((\underline c,\overline c)_a=(1,4)\), \((\underline c,\overline c)_b=(1,3)\),
\((\underline c,\overline c)_c=(2,5)\), \((\underline c,\overline c)_d=(2,3)\).
Let \(T=\{a,c\}\). Then \(c^x_a=4\), \(c^x_c=5\), \(c^x_b=1\), \(c^x_d=2\) and thus \(c^x(T)=9\).
By the cut/cycle criteria an MST under \(c^x\) is \(\{b,d\}\) with cost \(3\).
Hence \(R(T,c^x)=9-3=6\).
We reuse this example throughout to illustrate definitions and arguments.
\fi
```

