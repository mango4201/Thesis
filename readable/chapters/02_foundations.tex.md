## chapters/02_foundations.tex

```tex
\chapter{Foundations}\label{ch:foundations}
% ==== Chapter 2 Plan (AWU Index — do not compile) ====
% §2.1 Graphs, Trees, Notation
%   [2.1.1] Core objects — defs G=(V,E), δ(X), path/cycle, spanning tree, T in \cT.
%   [2.1.2] Fundamental structures — fundamental cycle C_f (f∉T), fundamental cut δ(X_e) (e∈T).
%   [2.1.3] Tree basics — unique path, |E(T)|=|V|-1, removing a tree edge splits the graph.
%   [2.1.4] Fundamental cycle lemma — adding f∉T creates a unique cycle C_f (label: lem:fund-cycle).
% §2.2 Fundamental Cut Lemma
%   [2.2.1] Lemma + proof  (label: lem:fund-cut).
%   [2.2.2] Remark         (label: rem:fund-cut).
% §2.3 MST Optimality
%   [2.3.0] Safe/unsafe + ties (remark) (label: rem:safe-unsafe).
%   [2.3.1] Cycle criterion (→ necessity)   (label: thm:mst-cycle-necessity).
%   [2.3.2] Cut criterion   (→ necessity)   (label: thm:mst-cut-necessity).
%   [2.3.3] Equivalence (← sufficiency)     (label: thm:mst-equivalence) (+ compat: thm:mst-criteria).
%   [2.3.4] Distinct weights ⇒ unique MST   (label: prop:unique-mst) [optional].
% §2.4 Algorithms (anchors only)
%   [2.4.1] Kruskal recap (label: rem:kruskal).
%   [2.4.2] Prim recap    (label: rem:prim).
% §2.5 Robust Definitions Only
%   [2.5.1] Uncertainty sets (label: def:uncertainty).
%   [2.5.2] Objectives Min–Max / Regret (label: def:robust-objs).
% §2.6 Algorithmic Preliminaries
%   [2.6.1] P/NP, reductions, weak/strong, pseudo-poly    (label: def:complexity).
%   [2.6.2] Approximation notions, PTAS/FPTAS, APX, L-reductions (label: def:approx).
%   [2.6.3] Proof patterns (exchange/extremal/reductions) (label: rem:proof-patterns).
% §2.7 Figure + Notation pointer
%   [2.7.1] TikZ micro-graph (label: fig:micro-graph).
%   [2.7.2] Notation pointer to Appendix B (label: rem:notation-pointer).
% ==== End Plan ====

% =============================
% §2.1 Graphs, Trees, and Notation
% =============================
\section{Graphs, Trees, and Notation}\label{sec:found-notation}

% AWU 2.1.1 — Core objects (180–230w)
\paragraph{AWU 2.1.1 (Core objects; purpose).}
Fix a finite, undirected, simple (no loops or parallel edges) graph \(G=(V,E)\).
Unless stated otherwise, \(G\) is connected. For \(X\subseteq V\), the \emph{cut}
induced by \(X\) is \(\delta(X):=\{\{u,v\}\in E : u\in X, v\in V\setminus X\}\).
A \emph{path} is a sequence of distinct vertices whose consecutive pairs form edges; a
\emph{cycle} is a closed path with at least three vertices. A subgraph \(T=(V, E(T))\)
is a \emph{spanning tree} if it is connected and acyclic. We denote by \(\cT\) the set
of all spanning trees of \(G\). When an edge-weight (cost) vector \(c\in \mathbb{R}_{\ge 0}^{E}\)
is fixed, the cost of a tree \(T\) under \(c\) is \(c(T):=\sum_{e\in E(T)} c_e\).
The minimum spanning tree (MST) value under \(c\) is \(\MSTcost(c):=\min_{T\in\cT} c(T)\).
All symbols in this section are consistent with the global notation file and will be
referenced throughout the thesis.

% AWU 2.1.2 — Fundamental structures (150–200w)
\paragraph{AWU 2.1.2 (Fundamental structures; purpose).}
Fix a spanning tree \(T\in\cT\). For any non-tree edge \(f\in E\setminus E(T)\), the graph \(T\cup\{f\}\)
contains a unique simple cycle; this is the \emph{fundamental cycle} of \(f\) with respect to \(T\) and is
denoted by \(C_f\). For any tree edge \(e\in E(T)\), the graph \(T\setminus\{e\}\) has exactly two connected
components; let \(X_e\subset V\) be the vertex set of one such component (the choice is arbitrary but fixed).
The set \(\delta(X_e)\) is the \emph{fundamental cut} of \(e\) with respect to \(T\). By construction, every edge
in \(\delta(X_e)\) has one endpoint in \(X_e\) and the other in \(V\setminus X_e\). Fundamental cycles and
fundamental cuts are well-defined with respect to \(T\) and will be used to formulate and prove optimality
criteria for MSTs.

% AWU 2.1.3 — Tree basics (facts used later)
\paragraph{AWU 2.1.3 (Tree basics; purpose).}\label{lem:tree-basics}
Let \(T\) be a spanning tree on \(G\). Then: (i) between any two vertices of \(T\) there is a unique simple path;
(ii) \(|E(T)|=|V|-1\); (iii) removing any tree edge disconnects \(T\) into exactly two components. These facts will
be used to justify fundamental cuts/cycles.

% AWU 2.1.4 — Fundamental cycle lemma
\begin{lemma}[Fundamental Cycle Lemma]\label{lem:fund-cycle}
Let \(T\in\cT\) and let \(f\in E\setminus E(T)\) be a non-tree edge with endpoints \(u,v\).
Then the graph \(T\cup\{f\}\) contains a unique simple cycle, namely \(C_f\), obtained by adding \(f\) to
the unique \(u\)-\(v\) path in \(T\).
\end{lemma}
\begin{proof}
By \Cref{lem:tree-basics}, there is a unique simple path in \(T\) between \(u\) and \(v\).
Adding \(f=\{u,v\}\) closes exactly one simple cycle consisting of this path plus \(f\).
No other cycle can appear because \(T\) is acyclic and \(f\) was the only added edge.
\end{proof}

% =============================
% §2.2 Fundamental Cut Lemma
% =============================
\section{Fundamental Cut Lemma}\label{sec:found-fundcut}

% AWU 2.2.1 — Lemma + proof (280–360w)
\begin{lemma}[Fundamental Cut Lemma]\label{lem:fund-cut}
Let \(T\in\cT\) be a spanning tree and let \(e\in E(T)\). Let \(X_e\subset V\) be a component vertex set of
\(T\setminus\{e\}\), and let \(\delta(X_e)\) be its fundamental cut. Then \(E(T)\cap \delta(X_e)=\{e\}\); that is,
\(e\) is the unique tree edge crossing its fundamental cut.
\end{lemma}
\begin{proof}
Deleting \(e\) from \(T\) yields two connected components with vertex sets \(X_e\) and \(V\setminus X_e\).
Any remaining edge of \(T\) lies entirely inside one of these components (otherwise \(T\) would still be connected
after deleting \(e\), contradicting that \(T\) is a tree). Hence no edge of \(E(T)\setminus\{e\}\) has endpoints in
distinct components, i.e., no such edge lies in \(\delta(X_e)\). On the other hand, \(e\) itself has one endpoint in
\(X_e\) and the other in \(V\setminus X_e\) by construction, so \(e\in \delta(X_e)\). Therefore
\(E(T)\cap \delta(X_e)=\{e\}\).
\end{proof}

% AWU 2.2.2 — Short remark (60–100w)
\begin{remark}\label{rem:fund-cut}
The set \(\delta(X_e)\) and the property in \Cref{lem:fund-cut} depend only on \(T\) and the choice of the tree edge \(e\).
This lemma will be cited in the proofs of the MST cut and cycle criteria
(\Cref{thm:mst-cut-necessity,thm:mst-cycle-necessity,thm:mst-equivalence}). Equal edge weights are permitted; uniqueness
in the lemma refers to set membership in \(E(T)\), not to minimum or maximum weights.
\end{remark}

% =============================
% §2.3 MST Optimality: Cycle and Cut Criteria
% =============================
\section{MST Optimality: Cycle and Cut Criteria}\label{sec:found-mst-criteria}

% AWU 2.3.0 — Safe/unsafe edges and ties
\begin{remark}[Safe/unsafe edges and ties]\label{rem:safe-unsafe}
\emph{Cut property (safe edges).} For any cut, every cheapest edge crossing it is safe to add to some MST.
\emph{Cycle property (unsafe edges).} On any cycle, every edge strictly heavier than the minimum-cost edge on that cycle
is unsafe for every MST. With ties, statements are understood as ``one of the cheapest'' or ``no strictly cheaper
alternative'': our theorems use inequalities like \(c_e\le c_{e'}\) to cover equal-cost cases. These formulations
justify the one-paragraph correctness arguments for Kruskal and Prim in \Cref{sec:found-kruskal-prim}.
\end{remark}

% AWU 2.3.1 — Cycle criterion (necessity)
\begin{theorem}[Cycle Criterion: Necessity]\label{thm:mst-cycle-necessity}
Let \(T\) be an MST under edge costs \(c\). For every non-tree edge \(f\in E\setminus E(T)\) with fundamental cycle \(C_f\),
every tree edge \(e\in E(T)\cap C_f\) satisfies \(c_e\le c_f\). In words: no edge of \(T\) on \(C_f\) is more expensive than \(f\).
\end{theorem}
\begin{proof}
Suppose there exists \(f\notin E(T)\) and \(e\in C_f\cap E(T)\) with \(c_e>c_f\). Then \(T' := T\setminus\{e\}\cup\{f\}\) is a
spanning tree (swapping along the fundamental cycle). Its cost is \(c(T')=c(T)-c_e+c_f<c(T)\), contradicting optimality of \(T\).
The exchange is valid because adding \(f\) creates exactly one cycle \(C_f\), and removing any tree edge on that cycle restores acyclicity.
\end{proof}

% AWU 2.3.2 — Cut criterion (necessity)
\begin{theorem}[Cut Criterion: Necessity]\label{thm:mst-cut-necessity}
Let \(T\) be an MST under edge costs \(c\). For every tree edge \(e\in E(T)\) with fundamental cut \(\delta(X_e)\), we have
\(c_e\le c_{e'}\) for all \(e'\in \delta(X_e)\). Equivalently, \(e\) is among the cheapest edges crossing its fundamental cut.
\end{theorem}
\begin{proof}
Assume there exists \(e'\in \delta(X_e)\) with \(c_{e'}<c_e\). By \Cref{lem:fund-cut}, \(e'\notin E(T)\). Then
\(T':=T\setminus\{e\}\cup\{e'\}\) is a spanning tree whose cost is strictly smaller than \(c(T)\), a contradiction.
\end{proof}

% AWU 2.3.3 — Equivalence (sufficiency from either property)
\begin{theorem}[MST Criteria Equivalence]\label{thm:mst-equivalence}\label{thm:mst-criteria}
A spanning tree \(T\) is an MST under costs \(c\) if and only if it satisfies either of the following equivalent properties:
\begin{enumerate}
  \item (Cut property) For every \(e\in E(T)\), \(e\) is among the cheapest edges in its fundamental cut \(\delta(X_e)\).
  \item (Cycle property) For every \(f\in E\setminus E(T)\) and every \(e\in C_f\cap E(T)\), \(c_e\le c_f\).
\end{enumerate}
\end{theorem}
\begin{proof}
The forward implications are \Cref{thm:mst-cut-necessity,thm:mst-cycle-necessity}. For the converse, assume the cut property holds
and suppose, for contradiction, that there exists a spanning tree \(T^*\) with \(c(T^*)<c(T)\). Consider the symmetric difference
\(E(T)\triangle E(T^*)\); pick \(e\in E(T)\setminus E(T^*)\) that lies on a path to reduce \(c(T)\). The unique cut \(\delta(X_e)\)
separates the components of \(T\setminus\{e\}\); because \(T^*\) is connected, it contains some edge \(e'\in \delta(X_e)\cap E(T^*)\).
By the cut property, \(c_e\le c_{e'}\). Swapping \(e\) for \(e'\) in \(T\) does not increase cost; repeating yields a tree no more
expensive than \(T^*\), contradicting minimality of \(T^*\). The cycle-property-based converse is analogous via exchanges along
fundamental cycles.
\end{proof}

% AWU 2.3.4 — Distinct weights ⇒ unique MST (optional)
\begin{proposition}[Distinct Weights Imply Uniqueness]\label{prop:unique-mst}
If all edge costs are pairwise distinct, the MST is unique.
\end{proposition}
\begin{proof}
Under distinct costs, the minimum edge on any cut is unique, hence \Cref{thm:mst-equivalence}(1) selects a unique edge at each step
of any cut-respecting construction, resulting in a unique MST. Alternatively, if two different MSTs existed, their symmetric difference
would contain a cycle with two different maximum-cost edges, contradicting distinctness.
\end{proof}

% =============================
% §2.4 Kruskal and Prim (brief recap)
% =============================
\section{Algorithmic Anchors: Kruskal and Prim}\label{sec:found-kruskal-prim}

% AWU 2.4.1 — Kruskal recap (120–160w)
\begin{remark}[Kruskal]\label{rem:kruskal}
Kruskal's algorithm scans edges in nondecreasing cost order and adds an edge if and only if it does not create a cycle with the edges
already chosen. Correctness follows from the cut property: when the algorithm considers an edge \(e\) that connects two components of the
current forest, \(e\) is the cheapest edge crossing the cut induced by these components; therefore it is safe to include. The process
terminates with a spanning tree after selecting \(|V|-1\) edges.
\end{remark}

% AWU 2.4.2 — Prim recap (120–160w)
\begin{remark}[Prim]\label{rem:prim}
Prim's algorithm grows a single tree from an arbitrary start vertex by repeatedly adding the cheapest-cost edge leaving the current tree
to a new vertex. By the cut property, the chosen edge is safe at each step (it is cheapest across the cut defined by the current tree and
its complement). After \(|V|-1\) iterations the result is a spanning tree whose cost is minimum.
\end{remark}

% =============================
% §2.5 Uncertainty Sets and Robust Criteria (definitions only)
% =============================
\section{Uncertainty Sets and Robust Criteria (Definitions Only)}\label{sec:found-robust-defs}

% AWU 2.5.1 — Uncertainty sets (150–200w)
\begin{definition}[Uncertainty Sets]\label{def:uncertainty}
We consider two cost-uncertainty models. (i) \emph{Discrete scenarios:} a finite set \(\Scenarios=\{c^{(1)},\dots,c^{(K)}\}\subset
\mathbb{R}_{\ge 0}^{E}\) of edge-cost vectors. (ii) \emph{Interval boxes:} for each \(e\in E\), a closed interval \([\ell_e,u_e]\subseteq
\mathbb{R}_{\ge 0}\), so the admissible costs form the Cartesian product \(\prod_{e\in E}[\ell_e,u_e]\). For any \(c\) in the respective
uncertainty set, the cost of a spanning tree \(T\) is \(c(T)=\sum_{e\in E(T)} c_e\), and the scenario-specific MST value is
\(\MSTcost(c)=\min_{T\in \cT} c(T)\). For a scenario index \(u\in\{1,\dots,K\}\), we write \(\cs{u}\) for the corresponding cost vector
with components \(\cs{u}_e\).
\end{definition}

% AWU 2.5.2 — Objectives (180–240w)
\begin{definition}[Robust Objectives]\label{def:robust-objs}
Given an uncertainty set \(\Scenarios\): (i) The \emph{Min--Max Spanning Tree} problem seeks \(T\in \cT\) minimizing the worst-case cost
\(\max_{c\in \Scenarios} c(T)\). (ii) The \emph{Min--Max Regret Spanning Tree} problem seeks \(T\in \cT\) minimizing the worst-case regret
\(\max_{c\in \Scenarios} \bigl(c(T)-\MSTcost(c)\bigr)\). In the interval model, the maxima are taken over all \(c\in \prod_{e\in E}[\ell_e,u_e]\).
These are the only robust notions used later; structural results and examples are deferred to Chapters~3 and~4.
\end{definition}

% =============================
% §2.6 Algorithmic Preliminaries (complexity & approximation)
% =============================
\section{Algorithmic Preliminaries}\label{sec:found-complexity}

% AWU 2.6.1 — P/NP & reductions (180–230w)
\paragraph{AWU 2.6.1 (Complexity glossary).}\label{def:complexity}
An \emph{optimization problem} associates a cost to each feasible solution and seeks a minimum (or maximum). A related \emph{decision version}
asks if there exists a solution of cost at most a given threshold. Class \(\mathbf{P}\) contains decision problems solvable in polynomial time;
class \(\mathbf{NP}\) contains problems verifiable in polynomial time. A problem is \emph{NP-hard} if every problem in \(\mathbf{NP}\) reduces to it via
a polynomial-time many-one reduction; it is \emph{NP-complete} if it is both in \(\mathbf{NP}\) and NP-hard. An NP-hard problem is \emph{weakly}
NP-hard if it admits a pseudo-polynomial-time algorithm; otherwise it is \emph{strongly} NP-hard. A running time is \emph{pseudo-polynomial} if it
is polynomial in the numeric value of inputs (e.g., cost magnitudes) rather than only in input length.

% AWU 2.6.2 — Approximation notions (180–230w)
\paragraph{AWU 2.6.2 (Approximation glossary).}\label{def:approx}
For a minimization problem with optimal value \(\mathrm{OPT}\), an algorithm is an \(\alpha\)-\emph{approximation} if it returns a solution of cost at most
\(\alpha\cdot \mathrm{OPT}\) for all inputs (ratio \(\alpha\ge 1\)). A \emph{PTAS} (polynomial-time approximation scheme) is a family of algorithms that, for any fixed
\(\varepsilon>0\), yields a \((1+\varepsilon)\)-approximation in time polynomial in the input size (but possibly exponential in \(1/\varepsilon\)); an \emph{FPTAS} is
polynomial in both input size and \(1/\varepsilon\). Class \(\mathbf{APX}\) contains problems admitting some constant-factor approximation; a problem is \emph{APX-hard}
if it is at least as hard to approximate as every problem in \(\mathbf{APX}\) under appropriate approximation-preserving reductions (e.g., L-reductions).
Inapproximability statements are typically conditioned on \(\mathbf{P}\ne \mathbf{NP}\).

% AWU 2.6.3 — Proof patterns we will use (130–180w)
\paragraph{AWU 2.6.3 (Proof patterns).}\label{rem:proof-patterns}
We rely on three recurring proof patterns. (i) \emph{Exchange arguments} on trees: modify a tree along a fundamental cut or cycle to obtain a cheaper tree,
contradicting optimality (used in \Cref{sec:found-mst-criteria}). (ii) \emph{Extremal arguments} for interval uncertainty: identify a scenario in the uncertainty
box that maximizes a given linear functional, avoiding enumeration (details appear in Chapters~3--4). (iii) \emph{Reductions}: classical NP-hardness via many-one
reductions and approximation lower bounds via gap-preserving reductions; full proofs are cited or deferred to Appendix~A if reproduced.

% =============================
% §2.7 Running Micro-Graph & Notation Table
% =============================
\section{Micro-Graph and Notation Pointer}\label{sec:found-micro}

% AWU 2.7.1 — TikZ micro-graph (single figure)
\begin{figure}[t]
  \centering
  % Simple reusable micro-graph (5 nodes, 6 edges). Weights are placeholders.
  \begin{tikzpicture}[scale=1, every node/.style={circle, draw, inner sep=1.2pt}]
    \node (a) at (0,0) {\small a};
    \node (b) at (2,0) {\small b};
    \node (c) at (4,0) {\small c};
    \node (d) at (1,1.6) {\small d};
    \node (e) at (3,1.6) {\small e};
    % edges with example weights
    \draw (a) -- node[below] {\small 2} (b);
    \draw (b) -- node[below] {\small 3} (c);
    \draw (a) -- node[left]  {\small 4} (d);
    \draw (d) -- node[above] {\small 1} (e);
    \draw (e) -- node[right] {\small 5} (c);
    \draw (b) -- node[above] {\small 6} (e); % non-tree edge candidate
  \end{tikzpicture}
  \caption{Reused micro-graph for illustrating fundamental cuts/cycles.}
  \label{fig:micro-graph}
\end{figure}

% AWU 2.7.2 — Notation pointer
\begin{remark}\label{rem:notation-pointer}
All symbols introduced in \Cref{sec:found-notation,sec:found-robust-defs,sec:found-complexity} are summarized in Appendix~B (Notation Table).
We use \texttt{\string\Cref} for all internal cross-references.
\end{remark}
```

