## chapters/03_minmax.tex

```tex
%═══════════════════════════════════════════════════════════
% CHAPTER 3: MIN-MAX SPANNING TREE
%
% Prerequisites: Ch2 (MST criteria, uncertainty definitions,
%                complexity primer)
% Provides:
%   - Min-Max formulation (discrete + interval + budgeted)
%   - Interval extremal lemma + proof  (§3.2)
%   - K=2 weakly NP-hard + FULL proof (partition reduction)
%   - K=const pseudo-poly + FPTAS (cited)
%   - K unbounded strongly NP-hard + O(log K) approximation (cited)
%   - Budgeted uncertainty: polynomial via Bertsimas-Sim
%     enumeration (placeholder for Set 2)
% Labels created in Ch3-A:
%   - ch:minmax, sec:mm-formulation, eq:mm-objective,
%     eq:mm-evaluator, sec:mm-extremal,
%     lem:interval-extremal-cost, cor:mm-interval-polynomial,
%     sec:mm-complexity, thm:mm-k2-hard,
%     thm:mm-kconst-pseudo, thm:mm-kunbdd-hard,
%     fig:partition-grid
% Labels declared but filled by Ch3-B:
%   - sec:mm-budgeted, thm:mm-budgeted-poly,
%     tab:budgeted-micrograph, sec:mm-discussion
% PREAMBLE DEPENDENCY: §3.2 uses a corollary environment, declared
%   on its own counter (not shared with theorem) in main.tex:
%     \newtheorem{corollary}{Corollary}
% CONVENTION: equations referenced with \eqref ("(2.1)");
%   named objects (sec/lem/thm/cor/fig/tab) with \Cref.
% Page budget: 12.1 pages (Ch3-A: 7.0 pp, Ch3-B: 5.1 pp)
% Status: Ch3 opening FINAL; §3.1 FINAL; §3.2 DRAFT; §3.3 next
%═══════════════════════════════════════════════════════════

\chapter{Min-Max Spanning Tree}\label{ch:minmax}

\Cref{ch:foundations} established that the deterministic minimum spanning tree problem, in which all edge costs are known in advance, is solvable in $O(m \log n)$ time.
This chapter studies the min-max version~\eqref{eq:minmax-objective}, where the costs are not known when the tree is chosen: a single spanning tree must be committed to in advance, an adversary then selects the least favourable cost realisation from a prescribed uncertainty set, and the tree is judged by its cost under that worst case.

\Cref{sec:mm-formulation} writes this objective out at the edge level and examines its structure under each of the three uncertainty models from \Cref{sec:uncertainty}, isolating the inner worst-case evaluator that drives the subsequent analysis.
\Cref{sec:mm-extremal} treats interval uncertainty, where the problem reduces to a minimum spanning tree under the upper-bound cost vector and is therefore polynomial.
\Cref{sec:mm-complexity} treats discrete uncertainty: weakly $\mathsf{NP}$-hard already at $K = 2$, admitting an FPTAS for any constant $K$, and strongly $\mathsf{NP}$-hard when $K$ grows with the input, though still approximable within a logarithmic factor in $K$.
\Cref{sec:mm-budgeted} treats budgeted uncertainty, polynomial via a richer reformulation, and \Cref{sec:mm-discussion} synthesises the three models.

%─────────────────────────────────────────────────────────
% SECTION 3.1: PROBLEM FORMULATION (~1.5-1.7 pages)
%─────────────────────────────────────────────────────────
\section{Problem Formulation}\label{sec:mm-formulation}

The min-max objective~\eqref{eq:minmax-objective} introduced in \Cref{sec:uncertainty} is stated with the cost function $c(T)$ left unexpanded.
We now write it out at the edge level using $c(T) = \sum_{e \in E(T)} c_e$.
The resulting \emph{\textcolor{RWTHBlue}{min-max spanning tree problem}} is
\begin{equation}\label{eq:mm-objective}
\min_{T \in \cT}\; \max_{c \in \Scenarios}\; \sum_{e \in E(T)} c_e.
\end{equation}
The uncertainty set $\Scenarios$ takes one of three forms in this thesis (\Cref{def:discrete-uncertainty,def:interval-uncertainty,def:budgeted-uncertainty}), and the algorithmic character of the problem varies sharply with the choice.

\paragraph{Decision Version and Complexity Classification.}

The optimisation problem~\eqref{eq:mm-objective} asks for both the optimal value and a tree achieving it.
The associated \emph{decision version} asks, given a budget $B \in \R$, whether there exists a spanning tree $T \in \cT$ with $\max_{c \in \Scenarios} c(T) \leq B$.
This decision version lies in $\mathsf{NP}$ whenever the inner maximisation $\max_{c \in \Scenarios} c(T)$ is polynomial-time computable for a fixed tree: the certificate is the tree $T$, and verification requires confirming $T \in \cT$ (an $O(n)$ breadth-first search, as in the deterministic MST decision problem of \Cref{sec:complexity}) and then evaluating the inner maximum.
Under discrete uncertainty this evaluation is the explicit maximum $\max_{k \in \{1, \ldots, K\}} \cs{k}(T)$ over the $K$ scenarios and takes $O(Kn)$ time; under interval and budgeted uncertainty, polynomial inner evaluation follows from the extremal characterisations developed in \Cref{sec:mm-extremal,sec:mm-budgeted}.
The complexity terminology used in the remainder of the chapter (polynomial time, weak and strong $\mathsf{NP}$-hardness, FPTAS) is the one fixed in \Cref{sec:complexity}.

\paragraph{Worst-Case Evaluator.}

A central object throughout the chapter is the \emph{\textcolor{RWTHBlue}{worst-case cost}} of a fixed spanning tree~$T \in \cT$:
\begin{equation}\label{eq:mm-evaluator}
\wc{T} \;:=\; \max_{c \in \Scenarios}\; \sum_{e \in E(T)} c_e.
\end{equation}
With this notation the min-max problem~\eqref{eq:mm-objective} reads simply $\min_{T \in \cT} \wc{T}$: among all spanning trees, choose the one whose worst-case cost is smallest.
How difficult this outer minimisation is depends on the form that $\wc{T}$ takes as a function of the tree~$T$.

The most favourable situation is when $\wc{T}$ is a sum of fixed per-edge weights, that is,
\[
\wc{T} = \sum_{e \in E(T)} w_e
\]
for some weight vector $w$ that does not itself depend on the choice of~$T$.
An objective of this form is called \emph{linear}, and minimising a linear objective over all spanning trees is nothing other than a deterministic minimum spanning tree problem under the weights~$w$, solvable by Kruskal's algorithm in $O(m \log n)$ time.
The three uncertainty models differ precisely in whether $\wc{T}$ is linear in this sense.

Under \emph{interval} uncertainty it is.
\Cref{lem:interval-extremal-cost} will show that the adversary does best by setting every edge of~$T$ to its upper bound, so that $\wc{T} = \sum_{e \in E(T)} u_e$.
This is a sum of fixed per-edge weights with $w = u$, and the min-max problem is therefore a minimum spanning tree problem under the upper-bound cost vector~$u$.

Under \emph{discrete} uncertainty it is not.
Here $\wc{T} = \max_{k \in \{1, \ldots, K\}} \cs{k}(T)$ is the largest of the $K$ scenario costs of~$T$, that is, the pointwise maximum of the $K$ linear functions $\cs{1}(T), \ldots, \cs{K}(T)$.
Whereas the interval adversary chooses each $c_e$ independently and is captured once and for all by the fixed vector $w = u$, the discrete adversary instead commits to one full scenario covering all edges, and which scenario is worst can depend on the tree~$T$.
No fixed weight vector~$w$ can encode this tree-dependent choice as per-edge weights, so $\wc{T}$ does not collapse into a single sum $\sum_{e \in E(T)} w_e$ across all spanning trees.
Consequently the problem does not reduce to one MST computation, and this obstruction is the source of the hardness established in \Cref{sec:mm-complexity}.

Under \emph{budgeted} uncertainty $\wc{T}$ is again not linear, because the deviation budget couples the edges of~$T$ to one another.
The problem therefore does not reduce to a single MST computation either, but \Cref{sec:mm-budgeted} shows that it nonetheless remains polynomial.

%─────────────────────────────────────────────────────────
% SECTION 3.2: INTERVAL WORST-CASE CHARACTERISATION (~2.0-2.5 pages)
%
% Goal: wc(T) = sum of upper bounds (Lemma 3.1); min-max = MST on u
%       (Corollary, = Goerigk Thm 4.8 specialised), hence in P.
%       Worked example on micro-graph.
% Prereqs: def:interval-uncertainty, eq:mm-evaluator, wc(T),
%          eq:mst-definition, Kruskal O(m log n) (sec:kruskal-prim),
%          tab:micro-graph-costs, fig:micro-graph, sec:complexity.
% Source: Goerigk Theorem 4.8 (reduction to nominal), cited on the
%         corollary; the per-tree separability is elementary.
% Labels: sec:mm-extremal, lem:interval-extremal-cost,
%         cor:mm-interval-polynomial.
% Notation: no new macros.
%─────────────────────────────────────────────────────────
\section{Interval Worst-Case Characterisation}\label{sec:mm-extremal}

We begin with interval uncertainty, the model identified in \Cref{sec:mm-formulation} as the favourable case.
There the worst-case cost $\wc{T}$ requires maximising over the interval set $\Scenarios = \prod_{e \in E} [\ell_e, u_e]$, which contains a continuum of cost vectors.
The following lemma shows that this maximisation has a transparent solution: confronted with a fixed tree, the adversary simply charges every edge its upper bound.

\begin{lemma}[Interval Extremal Cost]\label{lem:interval-extremal-cost}
Let $\Scenarios = \prod_{e \in E} [\ell_e, u_e]$ be an interval uncertainty set.
For every spanning tree $T \in \cT$, the worst-case cost satisfies
\[
\wc{T} \;=\; \max_{c \in \Scenarios}\; \sum_{e \in E(T)} c_e \;=\; \sum_{e \in E(T)} u_e,
\]
and the maximum is attained at the cost vector $c^{*}$ defined by $c^{*}_e = u_e$ for every edge $e \in E$.
\end{lemma}

\begin{proof}
Fix a spanning tree $T \in \cT$.
Only the edges of $T$ appear in the sum $\sum_{e \in E(T)} c_e$, so the costs assigned to edges outside $T$ do not affect the value of the maximisation.
Because the uncertainty set $\Scenarios = \prod_{e \in E} [\ell_e, u_e]$ is a Cartesian product, the cost of each edge can be chosen from its interval independently of the others.
Since each variable $c_e$ appears in exactly one term of the sum, the maximum of the sum equals the sum of the per-edge maxima:
\[
\max_{c \in \Scenarios}\; \sum_{e \in E(T)} c_e
\;=\; \sum_{e \in E(T)}\; \max_{c_e \in [\ell_e, u_e]} c_e .
\]
Each $c_e$ ranges over $[\ell_e, u_e]$ and is maximised at $u_e$.
Substituting yields $\max_{c \in \Scenarios} \sum_{e \in E(T)} c_e = \sum_{e \in E(T)} u_e$.
The cost vector $c^{*}$ with $c^{*}_e = u_e$ for all $e \in E$ lies in $\Scenarios$ and attains this value; its entries on edges outside $T$ are immaterial.
\end{proof}

\Cref{lem:interval-extremal-cost} reduces the worst-case cost of a tree to a single sum of upper bounds; this is the linear form anticipated in \Cref{sec:mm-formulation}, with weight vector $w = u$.
The consequence for the min-max problem is immediate.

\begin{corollary}[Polynomial Solvability under Interval Uncertainty]\label{cor:mm-interval-polynomial}
Under interval uncertainty $\Scenarios = \prod_{e \in E} [\ell_e, u_e]$, the min-max spanning tree problem satisfies
\[
\min_{T \in \cT}\, \wc{T} \;=\; \min_{T \in \cT}\, \sum_{e \in E(T)} u_e \;=\; \MSTcost{u},
\]
where $\MSTcost{u}$ is the minimum spanning tree cost under the upper-bound cost vector $u = (u_e)_{e \in E}$.
The problem is solved by computing a minimum spanning tree under $u$, which Kruskal's algorithm performs in $O(m \log n)$ time; in particular, it lies in $\mathsf{P}$.
\end{corollary}

\begin{proof}
By \Cref{lem:interval-extremal-cost}, $\wc{T} = \sum_{e \in E(T)} u_e$ for every $T \in \cT$.
Minimising over $T$ gives $\min_{T \in \cT} \wc{T} = \min_{T \in \cT} \sum_{e \in E(T)} u_e$, which is the minimum spanning tree cost $\MSTcost{u}$ by~\eqref{eq:mst-definition}.
Kruskal's algorithm computes a minimum spanning tree under any fixed cost vector in $O(m \log n)$ time (\Cref{sec:kruskal-prim}); applied to $u$, it returns an optimal min-max tree.
\end{proof}

The reduction in \Cref{cor:mm-interval-polynomial} is the spanning tree instance of \cite[Theorem~4.8]{Goerigk2021RCO}, which establishes it for interval min-max problems in general.

\paragraph{Worked Example.}

We apply \Cref{lem:interval-extremal-cost} to the micro-graph of \Cref{fig:micro-graph}, whose upper-bound cost vector is $u = (8, 5, 7, 6, 9)$.
By the lemma, the worst-case cost of a tree is the sum of its edges' upper bounds, so for the three representative trees
\[
\wc{T_1} = 8 + 5 + 7 = 20, \qquad
\wc{T_2} = 8 + 5 + 6 = 19, \qquad
\wc{T_3} = 5 + 7 + 9 = 21 .
\]
These values coincide with the scenario-$\cs{2}$ column of \Cref{tab:micro-graph-costs}, as they must: the micro-graph's scenario $\cs{2}$ was defined as the all-upper-bounds vector, so $\cs{2} = u$.

To find the min-max spanning tree, \Cref{cor:mm-interval-polynomial} directs us to a minimum spanning tree under $u$.
Kruskal's algorithm processes the edges in non-decreasing order of $u$, namely $e_2\,(5),\, e_4\,(6),\, e_3\,(7),\, e_1\,(8),\, e_5\,(9)$.
It adds $e_2 = \{2,3\}$ and $e_4 = \{3,4\}$, rejects $e_3 = \{2,4\}$ because its endpoints are already connected, and adds $e_1 = \{1,2\}$ to complete the tree.
The result is $T_2 = \{e_1, e_2, e_4\}$ with cost $\MSTcost{u} = 19$.
The three trees evaluated above are only three of the micro-graph's eight spanning trees; what certifies $T_2$ as the min-max tree over all of them is \Cref{cor:mm-interval-polynomial}, applied through this minimum spanning tree computation, rather than the three-way comparison alone.
Since the entries of $u$ are distinct, this minimum spanning tree is unique (\Cref{rem:cost-ties}), so $T_2$ is the unique min-max spanning tree under interval uncertainty, and its cost matches the value $\wc{T_2} = 19$ found above.

\paragraph{The Role of the Lower Bounds.}

\Cref{lem:interval-extremal-cost} shows that the min-max objective depends on the interval data only through the upper bounds $u$; the lower bounds $\ell$ play no role, and the entire interval model compresses to the single cost vector $u$.
Two points are worth drawing out.
First, the separation step in the proof used only that $\Scenarios$ is a product of intervals; it is exactly this absence of coupling between edges that fails under budgeted uncertainty (\Cref{sec:mm-budgeted}), where a shared deviation budget links the edges and the worst case is no longer a fixed per-edge sum.
Second, the insensitivity to the lower bounds is special to the min-max objective: the regret objective of \Cref{ch:regret} measures each tree against the scenario-optimal tree, a comparison that does depend on the lower bounds, and there the interval problem becomes substantially harder.

%─────────────────────────────────────────────────────────
% SECTION 3.3: COMPLEXITY AND APPROXIMATION -- UNIT 1, v7
%   (§3.3 opening, §3.3.1 opening, Theorem 3.2 + full proof,
%    fig:partition-grid, small instance)
%
% Replaces the placeholder from this banner down to and including the
% old "Figure 3.1" TODO block. The thm:mm-kconst-pseudo stub and all
% of §3.3.2 remain untouched for Unit 2.
%
% v7 (author choice + checker, small): author selected the PROSE form
%   of Step 3 (symbolic alternative and its comment wrapper removed).
%   One correctness-of-notation fix: in Step 2's converse the running
%   index in V_{<j} is now bound explicitly, ell in {0,1,...,j-1},
%   matching the construction's own vertex-set definition (both
%   indices bound); previously only the constraint ell < j was given
%   and the domain was implied.
% v6: (1) Step 2 opening reworded -- T_P defined directly from a
%   subset P. (2) Step 2 converse uses cut notation \cut{V_{<j}} =
%   {a_j, b_j}. (3) Step 3 symbolic/prose alternatives (prose kept).
% v5: theorem renamed -> "Hardness for Two Scenarios"; attribution via
%   \textcite; Steps 2/3 reframed to lead with their destination.
%
% Goal: weak NP-hardness of the min-max spanning tree problem already
%   at K = 2 (partition reduction); the chapter's one full hardness
%   proof.
% Prereqs: eq:mm-evaluator, \wc{T}, \cs{k}, decision version in NP
%   (sec:mm-formulation); sec:complexity (NP-hardness, weak/strong,
%   pseudo-polynomial); prop:tree-add-cycle (referenced as
%   "property~\ref" per Ch2 house style for list items);
%   lem:interval-extremal-cost, cor:mm-interval-polynomial (opening
%   recap only).
% Source: Goerigk Thm 8.4, construction and proof reproduced;
%   original Kouvelis-Yu 1997. Step 1 (iterated exchange) expands
%   Goerigk's one-clause WLOG "E_c <= E_T as the costs of all
%   vertical edges are zero". Step 2 (cut + four-cycle on the
%   figure) is the declared gap-fill justifying his asserted
%   tree-subset correspondence and Q1 + Q2 = 2Q. His Figure 8.1
%   = our fig:partition-grid.
% Notation vs source: a_j in E_1, b_j in E_2 with col = j; r_j in
%   E_c. Bookkeeping change only; graph (incl. the adjacency
%   formula, reproduced verbatim), scenarios, and threshold are
%   identical.
% Structure: theorem stated at K = 2 (Flow Card F3); the one-line
%   generalisation to all constant K >= 2 stands as its own
%   paragraph immediately after the proof.
% Labels: fills thm:mm-k2-hard; new fig:partition-grid.
% Notation file / Table A: checked, no updates needed; reduction-
%   local symbols (v_{i,j}, a_j, b_j, r_j, V_{<j}, W, Q, w(P)) stay
%   inline.
% Citations: KouvelisYu1997; Goerigk2021RCO (Thm 8.4);
%   KorteVygen2018 (Corollary 15.28, "PARTITION is NP-complete",
%   verified verbatim in source text; KV's sum-equality form is
%   equivalent to the half-total form used here).
% Numerics: instances, bijection (all-vertical trees = the 2^n sets
%   T_P with the stated costs), and min-min identity verified by
%   spanning-tree enumeration.
%─────────────────────────────────────────────────────────
\section{Complexity and Approximation}\label{sec:mm-complexity}

Under interval uncertainty the entire model compressed into the single cost vector $u$, and the min-max problem collapsed to one minimum spanning tree computation (\Cref{lem:interval-extremal-cost,cor:mm-interval-polynomial}).
Discrete uncertainty admits no such compression: by~\eqref{eq:mm-evaluator}, the worst-case cost is the largest of the $K$ scenario costs of the tree, and the maximising scenario may change from tree to tree (\Cref{sec:mm-formulation}).
This section turns that obstruction into complexity results, and the price depends on whether the number of scenarios is a fixed constant or part of the input.
For every constant $K$ the problem is weakly $\mathsf{NP}$-hard, already for $K = 2$; this is \Cref{thm:mm-k2-hard}, the hardness result of this chapter that we prove in full, complemented by a pseudo-polynomial algorithm and an FPTAS (\Cref{thm:mm-kconst-pseudo,thm:mm-kconst-fptas}).
When $K$ is part of the input, the hardness becomes strong and no approximation factor below two is achievable, although an $O(\log K)$-approximation remains available; these cited results close the section.

\subsection{Discrete Scenarios: Constant \texorpdfstring{$K$}{K}}

Throughout this subsection the number of scenarios $K$ is a constant, fixed independently of the instance.
We prove the hardness in full and record the matching positive results afterwards.

\begin{theorem}[Hardness for Two Scenarios]\label{thm:mm-k2-hard}
Under discrete uncertainty, the min-max spanning tree problem is weakly $\mathsf{NP}$-hard, even when restricted to $K = 2$ scenarios.
\end{theorem}

\Cref{thm:mm-k2-hard} is due to \textcite{KouvelisYu1997}; the presentation here follows \textcite[Theorem~8.4]{Goerigk2021RCO}.
The reduction starts from the \emph{\textcolor{RWTHBlue}{partition problem}}, which is $\mathsf{NP}$-complete \cite[Corollary~15.28]{KorteVygen2018}: given positive integers $w_1, \ldots, w_n$, decide whether some subset $P \subseteq \{1, \ldots, n\}$ satisfies $\sum_{i \in P} w_i = \tfrac{1}{2} \sum_{i=1}^{n} w_i$.
Throughout, $W = \sum_{i=1}^{n} w_i$ denotes the total weight and $Q = W/2$ the target value.

From a partition instance we build the grid graph $G = (V, E)$ shown in \Cref{fig:partition-grid}, with two rows and $n + 1$ columns.
Its vertex set is
\[
V = \{v_{i,j} : i \in \{1, 2\},\ j \in \{0, 1, \ldots, n\}\},
\]
and two vertices are adjacent exactly when they agree in one index and differ by one in the other, that is,
\[
\{v_{i,j},\, v_{k,\ell}\} \in E
\quad\Longleftrightarrow\quad
\bigl(i = k \text{ and } |j - \ell| = 1\bigr)
\ \text{ or }\
\bigl(|i - k| = 1 \text{ and } j = \ell\bigr).
\]
For $j \in \{1, \ldots, n\}$ we write $a_j = \{v_{1,j-1}, v_{1,j}\}$ for the \emph{first-row edge of column $j$} and $b_j = \{v_{2,j-1}, v_{2,j}\}$ for the \emph{second-row edge of column $j$}; together these $2n$ edges are the \emph{horizontal edges}, each named after the column of its right endpoint.
The remaining $n + 1$ edges are the \emph{vertical edges} $r_j = \{v_{1,j}, v_{2,j}\}$, one for every column $j \in \{0, 1, \ldots, n\}$.
The two scenarios place each weight once in each row:
\[
\cs{1}_{a_j} = \cs{2}_{b_j} = w_j,
\qquad
\cs{1}_{b_j} = \cs{2}_{a_j} = 0
\qquad
\text{for } j \in \{1, \ldots, n\},
\]
and $\cs{1}_{r_j} = \cs{2}_{r_j} = 0$ for all $j \in \{0, 1, \ldots, n\}$.

The intended mechanism is visible in \Cref{fig:partition-grid}.
A spanning tree must pass from column $j - 1$ to column $j$ through $a_j$ or $b_j$, paying $w_j$ in scenario~1 or in scenario~2 respectively, so choosing a tree distributes the weights $w_1, \ldots, w_n$ between the two scenarios; the worst case is the larger of the two shares, smallest when the split is even.
The proof makes this correspondence exact.

\begin{figure}[ht]
\centering
\begin{tikzpicture}[
    vertex/.style={circle, draw=RWTHBlue, very thick, fill=RWTHBlue!20, minimum size=8mm, font=\scriptsize\bfseries, inner sep=0pt},
    edge/.style={black, thick},
    elabel/.style={font=\scriptsize},
    xscale=1.0, yscale=1.0
]
    % top row (i = 1)
    \node[vertex] (a0) at (0, 2)   {$v_{1,0}$};
    \node[vertex] (a1) at (2.4, 2) {$v_{1,1}$};
    \node[vertex] (a2) at (4.8, 2) {$v_{1,2}$};
    \node          (ad) at (6.4, 2) {$\cdots$};
    \node[vertex] (an) at (8.0, 2) {$v_{1,n}$};
    % bottom row (i = 2)
    \node[vertex] (b0) at (0, 0)   {$v_{2,0}$};
    \node[vertex] (b1) at (2.4, 0) {$v_{2,1}$};
    \node[vertex] (b2) at (4.8, 0) {$v_{2,2}$};
    \node          (bd) at (6.4, 0) {$\cdots$};
    \node[vertex] (bn) at (8.0, 0) {$v_{2,n}$};
    % vertical edges (E_c)
    \draw[edge] (a0) -- (b0) node[midway, left, elabel] {$(0,0)$};
    \draw[edge] (a1) -- (b1) node[midway, right, elabel] {$(0,0)$};
    \draw[edge] (a2) -- (b2) node[midway, right, elabel] {$(0,0)$};
    \draw[edge] (an) -- (bn) node[midway, right, elabel] {$(0,0)$};
    % top horizontal edges (E_1)
    \draw[edge] (a0) -- (a1) node[midway, above, elabel] {$(w_1, 0)$};
    \draw[edge] (a1) -- (a2) node[midway, above, elabel] {$(w_2, 0)$};
    \draw[edge] (a2) -- (ad);
    \draw[edge] (ad) -- (an) node[midway, above, elabel] {$(w_n, 0)$};
    % bottom horizontal edges (E_2)
    \draw[edge] (b0) -- (b1) node[midway, below, elabel] {$(0, w_1)$};
    \draw[edge] (b1) -- (b2) node[midway, below, elabel] {$(0, w_2)$};
    \draw[edge] (b2) -- (bd);
    \draw[edge] (bd) -- (bn) node[midway, below, elabel] {$(0, w_n)$};
\end{tikzpicture}
\caption{The grid graph $G$ built from a partition instance with weights $w_1, \ldots, w_n$. Each edge is labelled with its cost pair $(\cs{1}_e, \cs{2}_e)$: a first-row edge of column $j$ costs $w_j$ in scenario~1, a second-row edge of column $j$ costs $w_j$ in scenario~2, and every vertical edge costs zero in both scenarios.}
\label{fig:partition-grid}
\end{figure}

\begin{proof}[Proof of \Cref{thm:mm-k2-hard}]
The construction is computable in polynomial time: $G$ has $2(n + 1)$ vertices and $3n + 1$ edges, and every cost is zero or one of the given weights.
We claim that $G$ admits a spanning tree of worst-case cost at most $Q$ if and only if the partition instance is a yes-instance; this is the decision version of \Cref{sec:mm-formulation} with budget $B = Q$.

\emph{Step 1: some optimal tree contains every vertical edge.}
Suppose a spanning tree $T$ does not contain the vertical edge $r_j$ of some column $j$.
Adding $r_j$ to $T$ creates exactly one cycle (property~\ref{prop:tree-add-cycle}), the fundamental cycle $C_{r_j}$, consisting of $r_j$ and the unique $T$-path from $v_{1,j}$ to $v_{2,j}$.
This path connects the two rows, so it crosses between them along some vertical edge of $T$; as $r_j \notin E(T)$, that edge is some $r_\ell$ with $\ell \neq j$, and in travelling between columns $j$ and $\ell$ the path uses at least one horizontal edge $h$.
The exchange $T' = (T \setminus \{h\}) \cup \{r_j\}$ is again a spanning tree: deleting $h$ breaks the unique cycle of $T \cup \{r_j\}$, while the remainder of $C_{r_j}$ keeps the endpoints of $h$ connected.
Since $\cs{k}_{r_j} = 0 \leq \cs{k}_{h}$ for $k \in \{1, 2\}$, neither scenario cost increases, and $\wc{T'} \leq \wc{T}$.
Each exchange raises the number of vertical edges by one, so iterating turns any spanning tree into one that contains all vertical edges at no larger worst-case cost; in particular, the minimum worst-case cost is attained by such a tree.

\emph{Step 2: once the vertical edges are fixed, a spanning tree is just a choice of one row per column; the trees are therefore the sets $T_P$ for $P \subseteq \{1, \ldots, n\}$, and the worst-case cost is a function of $P$ alone.}
With every vertical edge present, the two vertices of each column are already joined, and a spanning tree's only remaining decision is, for each column $j$, whether it crosses from column $j - 1$ through the first-row edge $a_j$ or the second-row edge $b_j$.
For a subset $P \subseteq \{1, \ldots, n\}$, let $T_P$ consist of every vertical edge together with the first-row edge $a_j$ in the columns $j \in P$ and the second-row edge $b_j$ in the rest:
\[
T_P := \{r_0, r_1, \ldots, r_n\} \cup \{a_j : j \in P\} \cup \{b_j : j \notin P\}.
\]
We show that the trees containing every vertical edge are exactly the sets $T_P$.
Each $T_P$ is a spanning tree: the vertical edges join the two rows within every column and the $n$ chosen horizontal edges link consecutive columns, so $T_P$ is connected and, with its $(n + 1) + n = |V| - 1$ edges, is a tree.
Conversely, let $T$ be a spanning tree containing every vertical edge, and fix $j \in \{1, \ldots, n\}$.
Let $V_{<j} := \{v_{i,\ell} : i \in \{1, 2\},\ \ell \in \{0, 1, \ldots, j-1\}\}$ be the vertices of the first $j$ columns.
The cut $\cut{V_{<j}} = \{a_j, b_j\}$ consists of the only two edges straddling the boundary between columns $j - 1$ and $j$ (\Cref{fig:partition-grid}), so connectivity forces at least one of $a_j, b_j$ into $E(T)$, while $r_{j-1}, r_j \in E(T)$ forbid both, since together they would close the four-cycle $a_j, r_j, b_j, r_{j-1}$.
Thus $T$ contains exactly one edge of each pair $\{a_j, b_j\}$, and as $G$ has only vertical and horizontal edges, $T = T_P$ with $P = \{j : a_j \in E(T)\}$.
The worst-case cost now follows: scenario~1 charges only first-row edges and scenario~2 only second-row edges, so with $w(P) = \sum_{j \in P} w_j$,
\[
\cs{1}(T_P) = w(P),
\qquad
\cs{2}(T_P) = W - w(P),
\qquad
\wc{T_P} = \max\{w(P),\, W - w(P)\}.
\]

\emph{Step 3: a tree of worst-case cost at most $Q$ exists if and only if the weights split evenly.}
By Steps 1 and 2 the minimisation over trees becomes a minimisation over subsets,
\[
\min_{T \in \cT} \wc{T}
\;=\;
\min_{P \subseteq \{1, \ldots, n\}} \max\{w(P),\, W - w(P)\}.
\]
Since $w(P)$ and $W - w(P)$ sum to $2Q$, their maximum is at least $Q$, with equality if and only if $w(P) = Q$.
The minimum worst-case cost is therefore at least $Q$, and equals $Q$ exactly when some subset satisfies $w(P) = Q$, which is the partition condition.
As the minimum never falls below $Q$, a spanning tree of worst-case cost at most $Q$ exists if and only if the partition instance is a yes-instance, proving the claim.

The partition problem therefore reduces in polynomial time to the decision version of the min-max spanning tree problem with $K = 2$, and the problem is $\mathsf{NP}$-hard.
Since \Cref{thm:mm-kconst-pseudo} provides a pseudo-polynomial algorithm, the hardness is weak in the sense of \Cref{sec:complexity}.
\end{proof}

The restriction to $K = 2$ is the sharpest form of the result rather than a limitation: for any constant $K \geq 2$, duplicating one of the two scenarios $K - 2$ times changes no worst-case cost, so the problem with exactly $K$ scenarios is weakly $\mathsf{NP}$-hard as well.

\paragraph{A Small Instance.}

Consider the weights $w = (3, 1, 2)$, so $n = 3$, $W = 6$ and $Q = 3$.
The subset $P = \{1\}$ satisfies $w(P) = 3 = Q$, and the tree from the proof,
\[
T_P = \{r_0, r_1, r_2, r_3\} \cup \{a_1, b_2, b_3\},
\]
has $\cs{1}(T_P) = w_1 = 3$ and $\cs{2}(T_P) = w_2 + w_3 = 3$, hence $\wc{T_P} = 3 = Q$; by Step~3 no spanning tree does better, and the reduction reports a yes-instance.
The weights $w = (1, 5)$ give the same $W = 6$ and $Q = 3$, but the four subsets $P \subseteq \{1, 2\}$ yield $w(P) \in \{0, 1, 5, 6\}$ and worst-case costs $\max\{w(P),\, W - w(P)\} \in \{5, 6\}$; the optimum is $5 > Q$, so no spanning tree meets the threshold, matching the fact that no subset of $\{1, 5\}$ sums to $3$.

%─────────────────────────────────────────────────────────
% SECTION 3.3.1 CLOSE -- UNIT 2 (v5): CONSTANT-K POSITIVE RESULTS
%
% v5 (fine-tuning pass on the user's approved v4): sentence-level only.
%   Long/compound sentences broken into shorter ones for readability;
%   no change to content, terminology, structure, or citations. ONE
%   correctness refinement: the closing now says "cannot be strongly
%   hard unless P = NP", matching the caveated statements in Ch2
%   sec:complexity (a strongly NP-hard problem admits no pseudo-poly
%   algorithm and no FPTAS *unless P = NP*).
%   Splits applied: lead (partition/structure; knapsack/pattern);
%   engine (pseudo-poly/running time); search (cost-list/optimum;
%   few-lists/search; bundling/iterating); FPTAS (scaling/effect;
%   result/attribution); closing (weak+not-strong; K-fixed/next-sec).
%
% GOALS unchanged from v4: G1 deliver the pseudo-poly algorithm
%   Theorem 3.2 forward-references; G2 explain WHY accessibly; G3
%   coherence (open from 3.2, close into 3.3.2); G4 minimal in-text
%   citation. ONE new named concept (exact spanning tree problem),
%   anchored to knapsack (sec:complexity). No new symbols/macros;
%   notation + Table A unchanged.
% Source (verified side-by-side): Goerigk Thm 8.2 (pseudo-poly, via
%   Thm 5.30) / 8.3 (FPTAS, via Thm 5.33, orig. [ABV05]); exact
%   spanning tree pseudo-poly = Lemma 8.1 [BP87].
% Structure: TWO theorems; thm:mm-kconst-pseudo on the pseudo-poly
%   result (keeps Theorem 3.2's forward ref exact). New label
%   thm:mm-kconst-fptas.
%
% !! TWO OUT-OF-BLOCK ACTIONS (unchanged from v4, still required):
%   (i) references.bib: ADD BarahonaPulleyblank1987 (cited here, else
%       undefined ref). Verified entry in the chat response.
%   (ii) §3.3 opening (sealed): "(\Cref{thm:mm-kconst-pseudo})" ->
%       "(\Cref{thm:mm-kconst-pseudo,thm:mm-kconst-fptas})".
%─────────────────────────────────────────────────────────

\Cref{thm:mm-k2-hard} proves the problem hard, but its reduction reveals the hardness to be of a mild and familiar kind.
All of the difficulty came from splitting a set of weights into two equal halves, the partition problem. None came from the spanning tree structure, which merely carried the two scenarios.
Hardness of this numerical kind is weak: it bites only when the numbers are large and eases once they are kept small.
The knapsack problem of \Cref{sec:complexity} is the canonical example, $\mathsf{NP}$-hard yet admitting an FPTAS. For a constant number of scenarios, the min-max spanning tree problem follows the same pattern.

\begin{theorem}[Pseudo-Polynomial Algorithm]\label{thm:mm-kconst-pseudo}
For every constant $K$, the min-max spanning tree problem under discrete uncertainty is solvable in pseudo-polynomial time.
\end{theorem}

\begin{theorem}[FPTAS]\label{thm:mm-kconst-fptas}
For every constant $K$, the min-max spanning tree problem under discrete uncertainty admits a fully polynomial-time approximation scheme.
\end{theorem}

Both algorithms are built on one subroutine, the \emph{\textcolor{RWTHBlue}{exact spanning tree problem}}: given integer edge costs and a target value, does some spanning tree have exactly that total cost?
This is solvable in pseudo-polynomial time. Its running time grows with the magnitude of the costs, not with the size of the graph alone \cite{BarahonaPulleyblank1987}.

The subroutine turns the min-max problem into a search.
Under $K$ scenarios a tree carries a list of $K$ costs, one per scenario, and its worst-case cost is the largest entry of that list. The min-max optimum is the tree whose largest entry is smallest.
Each of the $K$ entries is an integer between zero and the sum of all edge costs, so when $K$ is fixed only pseudo-polynomially many lists can occur. Searching these few lists replaces the search over the exponentially many spanning trees.
Bundling each edge's $K$ costs into a single number, in a base wide enough that the scenarios cannot interfere, lets the subroutine test whether a prescribed list is realised by some tree. Iterating it over the candidate lists and keeping the realised one of smallest maximum returns the min-max optimum in pseudo-polynomial time \cite[Theorem~8.2]{Goerigk2021RCO}.

The approximation scheme runs the same search on scaled costs.
Dividing every cost by a common factor, chosen from $\varepsilon$ and a bound on the optimum, and then rounding shrinks the magnitudes until only polynomially many lists remain. This turns the pseudo-polynomial search into a polynomial one, while distorting each scenario cost by at most a factor $1 + \varepsilon$.
The returned tree therefore has worst-case cost within $1 + \varepsilon$ of the optimum, in time polynomial in the input and in $1/\varepsilon$ \cite[Theorem~8.3]{Goerigk2021RCO}. This scheme is due to \textcite{AissiBazganVanderpooten2005ESA}.

These results supply the pseudo-polynomial algorithm that \Cref{thm:mm-k2-hard}'s proof invoked to classify its hardness as weak. With an FPTAS available as well, the problem cannot be strongly hard unless $\mathsf{P} = \mathsf{NP}$, since a strongly hard problem would admit neither.
Both guarantees hold only because $K$ is fixed, which keeps the number of cost lists small. This is exactly what the next section removes: once $K$ grows with the input, the lists are no longer few, and both the algorithm and the approximation scheme fall away.

%─────────────────────────────────────────────────────────
% SECTION 3.3.2: DISCRETE SCENARIOS, UNBOUNDED K  (v1)
%
% Replaces the §3.3.2 placeholder (mirror lines 256-283), i.e. the
% \subsection heading through the stale "Midpoint O(K)-approximation"
% TODO. The corrected approximation result is O(log K) (Cor 5.22),
% NOT the placeholder's wrong O(K) midpoint. §3.4 (line 285+) untouched.
%
% GOALS (Flow Card, approved with 4 decisions):
%   G1 establish unbounded-K hardness (strong NP-hard + (2-eps)),
%      delivering §3.3.1's "both fall away" close.
%   G2 give the matching O(log K) upper bound + no-constant-factor
%      lower bound (the approximation gap).
%   G3 coherence: the chapter's discrete spine -- numerical hardness
%      (weak, FPTAS) for fixed K vs structural hardness (strong, no
%      constant factor) for unbounded K; the scenario count is the line.
%   G4 accessible, minimal in-text citation.
% Decisions implemented: (1) light O(log K) intuition built on existing
%   info (max hard vs sum easy, §3.2); (2) no-constant-factor as a
%   strengthening SENTENCE, not its own theorem; (3) short 3-SAT sketch,
%   minimal problem description (3-SAT is classical), cite the rest;
%   (4) both O(log K) primaries verified and cited (Baak2025, Bilo2017).
% Prereqs: sec:complexity (strong NP-hard = NP-hard with poly-bounded
%   numbers; approximation ratio); cor:mm-interval-polynomial (the
%   linear interval case, for the O(log K) contrast); thm:mm-k2-hard
%   and the constant-K results (the partition/weak contrast).
% Source (verified side-by-side this session): Goerigk Thm 8.5 [KZ09]
%   (strong NP-hard + (2-eps); 3-SAT reduction, gadget per clause,
%   scenario per complementary literal pair, opt = 1 iff satisfiable);
%   [KZ11] no constant factor; Goerigk Cor 5.22 [Baa+24] O(log K), via
%   Thm 5.21 [Bil+17] (p-norm; approximate max by softer aggregate,
%   p = ln K gives O(log K)).
% New terms vs Ch2: 3-SAT (minimal gloss, classical); "matroid"/
%   "p-norm" deliberately AVOIDED (undefined in Ch2) -- O(log K)
%   intuition uses "smoother aggregate" instead.
% New labels: thm:mm-kunbdd-hard (exists in placeholder),
%   thm:mm-kunbdd-approx (NEW).
% NEW bib entries required (verified, supplied in chat): Baak2025,
%   Bilo2017. (KasperskiZielinski2009/2011 already in bib.)
%─────────────────────────────────────────────────────────
\subsection{Discrete Scenarios: Unbounded \texorpdfstring{$K$}{K}}

With $K$ part of the input, the hardness of the problem changes in kind.
For a fixed number of scenarios the difficulty was numerical, the partition problem in disguise, and that is why it was weak and yielded to scaling.
With arbitrarily many scenarios available, a purely logical problem can be encoded instead, one that involves no large numbers, and the problem becomes strongly hard and far harder to approximate.

\begin{theorem}[Strong NP-Hardness]\label{thm:mm-kunbdd-hard}
If $K$ is part of the input, the min-max spanning tree problem under discrete uncertainty is strongly $\mathsf{NP}$-hard, and for no $\varepsilon > 0$ can it be approximated within a factor of $2 - \varepsilon$.
\end{theorem}

The result is due to \textcite{KasperskiZielinski2009}; the reduction sketched here follows \cite[Theorem~8.5]{Goerigk2021RCO}.
It starts from 3-SAT, the classical $\mathsf{NP}$-complete satisfiability problem, and writes its logic into the scenarios.
Each clause becomes a small gadget carrying one edge per literal, and a spanning tree must take exactly one of these edges per clause, which reads as choosing a literal that satisfies the clause.
For every pair of contradictory literals, one asserting a variable and the other negating it, a scenario is added that charges cost $1$ to the two corresponding edges and $0$ to all others.
A consistent choice of literals never takes both edges of such a pair, so its worst-case cost is $1$; an inconsistent choice takes both edges of some pair, raising the worst-case cost to $2$.
Since a consistent choice is exactly a satisfying assignment, the optimal worst-case cost is $1$ when the formula is satisfiable and $2$ otherwise.

Deciding which of these holds therefore decides satisfiability, so the problem is $\mathsf{NP}$-hard.
Every cost is $0$ or $1$, so the numbers stay polynomially bounded and the hardness is strong, unlike the partition reduction where large weights were essential.
The gap between $1$ and $2$ also means that an approximation within any factor below $2$ would separate the two cases and hence solve 3-SAT.
The inapproximability is in fact far stronger: no constant factor is achievable at all \cite{KasperskiZielinski2011}.

\begin{theorem}[Logarithmic Approximation]\label{thm:mm-kunbdd-approx}
The min-max spanning tree problem under discrete uncertainty can be approximated within a factor of $O(\log K)$.
\end{theorem}

This closes the gap from above, leaving only a logarithmic factor between what is achievable and the no-constant-factor barrier.
The obstacle is the worst-case objective itself, a maximum over the $K$ scenarios, which is much harder to optimise than the plain sum that reduced the interval problem to a single spanning tree computation (\Cref{cor:mm-interval-polynomial}).
Replacing that maximum by a smoother aggregate of the $K$ costs, one the spanning tree problem can still optimise efficiently, restores tractability, and the aggregate can be tuned so that it never underestimates the true maximum by more than a logarithmic factor.
The resulting bound is \cite[Corollary~5.22]{Goerigk2021RCO}, established by \textcite{Baak2025} on the foundation of \textcite{Bilo2017}.

The discrete-uncertainty picture is now complete, and the number of scenarios is its dividing line.
For a fixed number of scenarios the problem is only weakly hard and admits an FPTAS; once that number is unbounded it becomes strongly hard, inapproximable within any constant factor, and approximable only within $O(\log K)$.
One problem thus carries two kinds of hardness: a numerical one that scaling defeats, and a structural one that it cannot touch.

%─────────────────────────────────────────────────────────
% SECTION 3.4: BUDGETED UNCERTAINTY (1.5 pages)
%
% Goal: Treat the third standard uncertainty model.
% Result: Polynomial-time algorithm via Bertsimas-Sim enumeration
%         (citing Goerigk Theorem 4.21).
% Proof status: cite the framework, instantiate for MST.
% Worked example: micro-graph with bar_c = midpoint, hat_c = half-width.
%
% STATUS: SKETCHED — full draft preserved below in \iffalse...\fi block.
%─────────────────────────────────────────────────────────
\section{Budgeted Uncertainty}\label{sec:mm-budgeted}

% TODO: Section opening (0.1 pg)
%   - Bridge from §2.4 definition: the budgeted set Scenarios^Gamma
%   - Position between discrete and interval, parameter Gamma as conservatism dial
%   - Section roadmap: per-tree evaluation → framework theorem → worked example → regret pointer

% TODO: Per-tree worst-case characterisation (0.3 pg)
%   - Inner max over Scenarios^Gamma is an LP over a bounded polytope
%   - Extreme-point structure: adversary picks the Gamma edges of T with largest hat_c
%   - Per-tree evaluation is polynomial, but tree enumeration is not
%   - Sets up the need for the framework theorem below

% TODO: Theorem thm:mm-budgeted-poly (0.4 pg)
%   - Statement: Min-Max ST under continuous budgeted uncertainty is polynomial
%   - Reformulation via Goerigk Theorem 4.21:
%       min_T max_c c(T) = min_{pi in Pi} ( min_T sum_e (bar_c_e + [hat_c_e - pi]^+) + Gamma*pi )
%     with Pi = {0} ∪ {hat_c_e : e in E}
%   - Inner min is a nominal MST → Kruskal in O(m log n)
%   - |Pi| ≤ m+1 candidates → overall O(m^2 log n)
%   - PROOF: Direct instantiation of Goerigk Thm 4.21 + Lemma 4.20
%     (no derivation needed; cite the framework)

% TODO: Interpretation paragraph (0.1 pg)
%   - π as price per unit deviation budget; Γπ total payment to adversary
%   - [hat_c_e - π]^+ residual marginal cost after pricing
%   - Enumeration explores piecewise-linear break-points

% TODO: Worked example on the micro-graph (0.4 pg)
%   - Use bar_c_e = (l_e + u_e)/2, hat_c_e = (u_e - l_e)/2 from existing intervals
%   - Table of (bar_c, hat_c) values per edge (or reuse derived from Tab. 2.1)
%   - Set Γ = 2
%   - Candidate set Π = {0, 2, 2.5, 3}
%   - Table of modified costs c'_e(π) for each π, MST cost, +Γπ, total
%   - Identify optimum: π=2, tree T_2 = {e1,e2,e4}, objective 17
%   - Cross-check via direct computation: bar_c(T_2) + top-2 hat_c on T_2 = 12+3+2 = 17
%   - NUMERICS ARE VERIFIED (Python enumeration; see thread context)
%   - Label: tab:budgeted-micrograph

% TODO: Cross-model comparison (0.1 pg)
%   - T_2 is optimal under discrete, interval, AND budgeted
%   - Worst-case costs differ: 19 (discrete/interval) vs 17 (budgeted Γ=2)
%   - Γ as smooth interpolation: Γ=0 → deterministic on bar_c (cost 12); Γ=|E| → interval (cost 19)

% TODO: Regret pointer (0.1 pg)
%   - Min-Max regret under budgeted is NP-hard
%   - Argument: interval is the special case Γ=|E|, so regret-interval NP-hardness lifts
%   - Cite AverbakhLebedev2004 for regret-interval, Goerigk §4.5.3 for the general reformulation
%   - State explicitly: not pursued further in this thesis

%─────────────────────────────────────────────────────────
% FULL DRAFT (preserved for later use; uncomment by removing \iffalse...\fi wrapper)
%─────────────────────────────────────────────────────────
\iffalse

The budgeted uncertainty set $\Scenarios^{\Gamma}$ introduced in \Cref{def:budgeted-uncertainty} sits between the discrete and interval models: edge costs may deviate from their nominal values up to a maximum deviation each, but the total deviation across all edges is bounded by~$\Gamma$.
This section shows that the resulting min-max spanning tree problem is solvable in polynomial time, by reduction to a finite family of nominal MST instances.

\paragraph{Worst-Case Cost of a Fixed Tree.}

For a fixed spanning tree $T \in \cT$, the inner maximisation
\[
\max_{c \in \Scenarios^{\Gamma}} c(T) = \max_{\delta \in [0,1]^{|E|},\ \sum_e \delta_e \leq \Gamma} \sum_{e \in T}\bigl(\bar{c}_e + \hat{c}_e\, \delta_e\bigr)
\]
is a linear program over a bounded polytope, hence attains its maximum at an extreme point.
At any extreme point of the deviation polytope, the adversary selects the $\Gamma$ edges of $T$ with the largest deviations $\hat{c}_e$ and sets $\delta_e = 1$ on those edges; the remaining deviations are zero.
This evaluation is polynomial per tree, but enumerating all spanning trees is not: the algorithm below avoids enumeration via a duality-based reformulation.

\paragraph{Polynomial-Time Algorithm.}

\begin{theorem}[Min-Max Spanning Tree under Budgeted Uncertainty]\label{thm:mm-budgeted-poly}
The min-max spanning tree problem under continuous budgeted uncertainty $\Scenarios^{\Gamma}$ can be solved in polynomial time.
Specifically, the optimum is attained by
\[
\min_{T \in \cT} \max_{c \in \Scenarios^{\Gamma}} c(T)
\;=\; \min_{\pi \in \Pi}\, \Biggl( \min_{T \in \cT} \sum_{e \in E(T)} \bigl(\bar{c}_e + [\hat{c}_e - \pi]^{+}\bigr) \;+\; \Gamma\,\pi \Biggr),
\]
where $\Pi = \{0\} \cup \{\hat{c}_e : e \in E\}$ and $[x]^{+} = \max\{x, 0\}$.
The inner minimum is a nominal MST instance, solvable by Kruskal's algorithm in $O(m \log n)$ time; with $|\Pi| \leq m + 1$ candidates, the overall running time is $O(m^2 \log n)$.
\end{theorem}

\begin{proof}
The result is a direct instantiation of \cite[Theorem~4.21]{Goerigk2021RCO}, which establishes the reformulation for any min-max combinatorial optimisation problem $\min_{x \in \mathcal{X}} \max_{c \in \Scenarios^{\Gamma}} c^{\top} x$ with $\mathcal{X} \subseteq \{0,1\}^n$.
For min-max spanning trees, $\mathcal{X} = \{\mathbf{1}_T : T \in \cT\}$, and the inner minimisation over $\mathcal{X}$ at fixed~$\pi$ becomes the nominal MST problem with modified edge costs $c'_e(\pi) = \bar{c}_e + [\hat{c}_e - \pi]^{+}$.
Kruskal's algorithm solves each such instance in $O(m \log n)$.
The candidate set $\Pi$ contains at most $m + 1$ values \cite[Lemma~4.20]{Goerigk2021RCO}, giving the stated bound.
\end{proof}

The reformulation has an intuitive interpretation: $\pi$ acts as a price the decision-maker pays per unit of deviation budget. The term $\Gamma\,\pi$ is the total payment to the adversary, while $[\hat{c}_e - \pi]^{+}$ is the residual marginal cost an edge contributes once its deviation has been priced at~$\pi$.
Enumerating $\pi$ over $\Pi$ explores all break-points of this piecewise-linear function and identifies the optimal trade-off.

\paragraph{Worked Example.}

We instantiate the budgeted model on the micro-graph (\Cref{fig:micro-graph}) by setting nominal costs $\bar{c}_e = (\ell_e + u_e)/2$ and maximum deviations $\hat{c}_e = (u_e - \ell_e)/2$; this aligns the budgeted model with the interval data from \Cref{tab:micro-graph-costs}. \Cref{tab:budgeted-micrograph} lists the resulting values.

\begin{table}[h]
\centering
\caption{Budgeted parameters for the micro-graph, derived from \Cref{tab:micro-graph-costs}.}\label{tab:budgeted-micrograph}
\begin{tabular}{lccccc}
\toprule
Edge & $e_1$ & $e_2$ & $e_3$ & $e_4$ & $e_5$ \\
\midrule
$\bar{c}_e$ & $5$ & $3$ & $5$ & $4$ & $6.5$ \\
$\hat{c}_e$ & $3$ & $2$ & $2$ & $2$ & $2.5$ \\
\bottomrule
\end{tabular}
\end{table}

Setting $\Gamma = 2$, the candidate set is $\Pi = \{0, 2, 2.5, 3\}$.
For each $\pi \in \Pi$ we form the modified cost vector $c'(\pi)$, solve the nominal MST on $c'(\pi)$, and add $\Gamma\,\pi$:
\[
\begin{array}{c|cccc|c|c}
\pi & c'_{e_1} & c'_{e_2} & c'_{e_3} & c'_{e_4} & c'_{e_5} & \min_T c'(T) + \Gamma\pi \\
\midrule
0   & 8   & 5 & 7 & 6 & 9   & 19 + 0 = 19 \\
2   & 6   & 3 & 5 & 4 & 7   & 13 + 4 = 17 \\
2.5 & 5.5 & 3 & 5 & 4 & 6.5 & 12.5 + 5 = 17.5 \\
3   & 5   & 3 & 5 & 4 & 6.5 & 12 + 6 = 18 \\
\end{array}
\]
The minimum is attained at $\pi = 2$ with objective value~$17$.
The optimal tree, identified by Kruskal on $c'(2)$, is $T_2 = \{e_1, e_2, e_4\}$.
Direct verification confirms this value: under $T_2$, the adversary selects the two edges with the largest deviations ($e_1$ with $\hat{c} = 3$ and $e_2$ or $e_4$ with $\hat{c} = 2$), yielding a worst-case cost of $\bar{c}(T_2) + 3 + 2 = 12 + 5 = 17$, matching the framework result.

\paragraph{Comparison with the Other Models.}

The micro-graph illustrates how the optimal min-max tree shifts with the uncertainty model.
The same tree $T_2$ is optimal under all three models (discrete with the scenarios of \Cref{tab:micro-graph-costs}, interval with the bounds $[\ell_e, u_e]$, and budgeted with $\Gamma = 2$ as above), but the worst-case costs differ: $19$ for discrete and interval (full upper bounds attained), $17$ for budgeted with $\Gamma = 2$.
As $\Gamma$ increases from~$0$ to~$|E|$, the budgeted objective interpolates smoothly between the deterministic problem on $\bar{c}$ (cost $12$) and the interval problem on $[\bar{c}_e, \bar{c}_e + \hat{c}_e]$ (cost~$19$).

\paragraph{Regret Variant.}

The min-max \emph{regret} spanning tree problem under budgeted uncertainty is $\mathsf{NP}$-hard: since the interval model is the special case $\Gamma = |E|$ of the budgeted model, the $\mathsf{NP}$-hardness of min-max regret under interval uncertainty (\Cref{ch:regret}; see \cite{AverbakhLebedev2004}) carries over.
We do not pursue this variant in detail; \cite[\S4.5.3]{Goerigk2021RCO} provides general reformulation results for min-max regret under budgeted uncertainty.

\fi

% END OF SECTION 3.4

%─────────────────────────────────────────────────────────
% SECTION 3.5: DISCUSSION AND CONTRAST (2.1 pages)
%─────────────────────────────────────────────────────────
\section{Discussion}\label{sec:mm-discussion}

% TODO: Discrete vs Interval vs Budgeted comparison table (0.7 pg)
%   | Model    | K=const | K unbounded | Interval | Budgeted |
%   | Min-Max  | Weak NP-hard, FPTAS | Strong NP-hard, O(K)-approx | Polynomial | Polynomial |

% TODO: Why intervals are easy (0.5 pg)
%   - Extremal lemma makes worst case explicit (Lemma 3.1)
%   - No inter-edge dependencies in product structure
%   - Contrast: discrete requires K worst-case evaluations

% TODO: Comparison with nominal MST (0.5 pg)
%   - Min-Max solution avoids high-upper-bound edges
%   - Micro-graph example: Compare T_MM vs T_MST(c_mid)

% TODO: Modeling remarks (0.4 pg)
%   - LP reformulations exist (cite compact formulation papers)
%   - Not our focus (structural/complexity emphasis)

%─────────────────────────────────────────────────────────
% SECTION 3.6: CHAPTER SUMMARY (0.3 pages)
%─────────────────────────────────────────────────────────
\section*{Summary}

% TODO: Summary paragraph (0.3 pg)
% Min-Max ST complexity depends critically on uncertainty structure.
% Intervals are polynomial (Lemma 3.1 + Goerigk Thm 4.8) via extremal
% evaluation. Discrete scenarios harder: weakly NP-hard even K=2
% (Theorem 3.2, partition reduction), with FPTAS for constant K
% (Theorem 3.3). For unbounded K: strongly NP-hard (Theorem 3.4),
% O(K) approximation, no constant-factor possible (Theorem 3.5).
% Budgeted uncertainty is polynomial (Theorem 3.X, Bertsimas-Sim
% framework via Goerigk Theorem 4.21) by reduction to O(m) nominal
% MST instances.
% Chapter 5 synthesis table compares with Min-Max Regret results.

% END OF CHAPTER 3```

