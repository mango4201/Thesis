## chapters/03_minmax.tex

```tex
%═══════════════════════════════════════════════════════════
% CHAPTER 3: MIN-MAX SPANNING TREE
%
% Prerequisites: Ch2 (MST criteria, the three uncertainty
%   definitions including the non-negativity convention, complexity
%   primer).
%
% STATUS
%   chapter opening  LOCKED
%   §3.1             LOCKED
%   §3.2             LOCKED
%   §3.3             LOCKED (opening, §3.3.1, §3.3.2)
%   §3.4             LOCKED
%   Summary          LOCKED (unnumbered, matching Ch2)
%
% Labels live in this chapter:
%   ch:minmax, sec:mm-formulation, eq:mm-objective, eq:mm-evaluator,
%   sec:mm-extremal, lem:interval-extremal-cost,
%   cor:mm-interval-polynomial, sec:mm-complexity, thm:mm-k2-hard,
%   fig:partition-grid, thm:mm-kconst-pseudo, thm:mm-kconst-fptas,
%   thm:mm-kunbdd-hard, fig:sat-gadget, eq:mm-aggregate-bracket,
%   thm:mm-kunbdd-approx, sec:mm-budgeted, lem:budgeted-extremal-cost,
%   eq:mm-budgeted-inner, lem:budgeted-threshold-form,
%   thm:mm-budgeted-poly, tab:budgeted-micrograph, fig:budgeted-sweep.
%
% CONVENTIONS
%   Equations referenced with \eqref, named objects with \Cref.
%   Theorem-like environments are numbered per chapter, each on its
%   own counter (main.tex), so numbers repeat across environments:
%   Theorems 3.1 to 3.6, Lemmas 3.1 to 3.3 and Corollary 3.1 coexist,
%   with Figures 3.1 to 3.3 and Table 3.1. Those numbers are given
%   for orientation only; comments reference LABELS, never numbers,
%   so renumbering stays safe.
%   The chapter closes with an unnumbered Summary carrying
%   \addcontentsline. It owns mechanism and interpretation; the
%   result inventory belongs to tab:complexity-landscape in Ch5.
%
% MICRO-GRAPH (every Ch3 number verified by full enumeration):
%   e1[2,4] e2[3,5] e3[1,7] e4[4,6] e5[5,7]; u = (4,5,7,6,7) = c^(2);
%   8 spanning trees; MST(u) = 15, uniquely T2 = {e1,e2,e4};
%   wc(T1) = 16, wc(T2) = 15, wc(T3) = 19; the other seven u-costs
%   run 16, 17, 17, 18, 18, 19, 20.
%   Budgeted instantiation (§3.4): bar_c = (3,4,4,5,6) = c^(3),
%   hat_c = (1,1,3,1,1), bar_c + hat_c = u, Pi = {0,1,3}.
%   Optimum by budget, Gamma = 0..5: 11, 13, 14, 15, 15, 15, attained
%   by T1 at Gamma = 0 and by T2 from Gamma = 1 on; attaining levels
%   3, 1, 1, {0 or 1}, 0, 0. At Gamma = 2 the eight worst-case costs
%   are 14, 15, 16, 16, 17, 17, 18, 19.
%═══════════════════════════════════════════════════════════

\chapter{Min-Max Spanning Tree}\label{ch:minmax}

\Cref{ch:foundations} established that the deterministic minimum spanning tree problem, in which all edge costs are known in advance, is solvable in $O(m \log n)$ time.
This chapter studies the min-max version~\eqref{eq:minmax-objective}, where the costs are not known when the tree is chosen: a single spanning tree must be committed to in advance, an adversary then selects the least favourable cost realisation from a prescribed uncertainty set, and the tree is judged by its cost under that worst case.

\Cref{sec:mm-formulation} writes this objective out at the edge level and examines its structure under each of the three uncertainty models from \Cref{sec:uncertainty}, isolating the inner worst-case evaluator that drives the subsequent analysis.
\Cref{sec:mm-extremal} treats interval uncertainty, where the problem reduces to a minimum spanning tree under the upper-bound cost vector and is therefore polynomial.
\Cref{sec:mm-complexity} treats discrete uncertainty: weakly $\mathsf{NP}$-hard already at $K = 2$, admitting an FPTAS for any constant $K$, and strongly $\mathsf{NP}$-hard when $K$ grows with the input, though still approximable within a logarithmic factor in $K$.
\Cref{sec:mm-budgeted} treats budgeted uncertainty, polynomial via a richer reformulation, and a closing summary draws the three models together.

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
In general no fixed weight vector~$w$ reproduces this tree-dependent choice, so $\wc{T}$ need not collapse into a single sum $\sum_{e \in E(T)} w_e$ across all spanning trees.
Consequently the problem does not reduce to one MST computation, and this obstruction is the source of the hardness established in \Cref{sec:mm-complexity}.

Under \emph{budgeted} uncertainty $\wc{T}$ is in general not linear either, because the shared deviation budget couples the edges of~$T$: which of them the adversary drives upward depends on the tree.
The problem therefore does not reduce to a single MST computation either, but \Cref{sec:mm-budgeted} shows that it nonetheless remains polynomial.

%─────────────────────────────────────────────────────────
% SECTION 3.2: INTERVAL WORST-CASE CHARACTERISATION -- LOCKED
%
% Goal: wc(T) = sum of upper bounds (lem:interval-extremal-cost);
%   min-max = MST on u (cor:mm-interval-polynomial, the spanning
%   tree instance of Goerigk Thm 4.8), hence polynomial. Worked
%   example on the micro-graph.
% Prereqs: def:interval-uncertainty, eq:mm-evaluator, \wc{T},
%   eq:mst-definition, sec:kruskal-prim, rem:cost-ties,
%   tab:micro-graph-costs, fig:micro-graph, sec:complexity.
% Source: Goerigk Theorem 4.8, cited on the corollary; the per-tree
%   separability is elementary and proved here in full.
% Labels: sec:mm-extremal, lem:interval-extremal-cost,
%   cor:mm-interval-polynomial.
% Numerics (re-verified by enumeration at lock time): u = (4,5,7,6,7);
%   wc(T1) = 16, wc(T2) = 15, wc(T3) = 19, matching the c^(2) column
%   of tab:micro-graph-costs since c^(2) = u; MST(u) = 15 at T2 and
%   unique (next best 16); Kruskal order e1(4), e2(5), e4(6), after
%   which the tree is complete and the tied pair e3, e5 (both 7) is
%   never examined. u is not all-distinct, so rem:cost-ties cannot
%   supply the uniqueness; the enumerated cost gap does.
% Final-pass change: the closing sentence of the worked example, one
%   60-word chain, split into three.
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
\;=\; \sum_{e \in E(T)}\; \max_{c_e \in [\ell_e, u_e]} c_e.
\]
Each cost $c_e$ ranges over the interval $[\ell_e, u_e]$ and is maximised at $u_e$.
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

%─────────────────────────────────────────────────────────
% §3.2 WORKED EXAMPLE -- CORRECTED FOR THE NEW MICRO-GRAPH
%
% Replaces the "\paragraph{Worked Example.}" block of §3.2 (the
% stale block still using u = (8,5,7,6,9) from the old example;
% currently 03_minmax lines ~162-178). Everything else in §3.2
% (Lemma, Corollary, Role of the Lower Bounds) is number-free and
% needs no change.
%
% All numbers verified by full spanning-tree enumeration against
% the NEW micro-graph (e1[2,4], e2[3,5], e3[1,7], e4[4,6],
% e5[5,7]; e5 = {1,3}):
%   u = (4,5,7,6,7); wc(T1)=16, wc(T2)=15, wc(T3)=19; these equal
%   the cs{2} column of tab:micro-graph-costs (cs{2} = u holds);
%   8 spanning trees; MST(u) = T2 = {e1,e2,e4} at 15, UNIQUE
%   (remaining seven u-costs: 16,17,17,18,18,19,20); Kruskal order
%   e1(4), e2(5), e4(6), then e3,e5 (tie at 7, never examined).
% Uniqueness note: u is NOT all-distinct (e3, e5 share 7), so
%   rem:cost-ties no longer applies as a justification; the new
%   final sentence states this and certifies uniqueness by the
%   enumerated cost gap instead (sufficient-not-necessary teaching
%   point).
%─────────────────────────────────────────────────────────

\paragraph{Worked Example.}

We apply \Cref{lem:interval-extremal-cost} to the micro-graph of \Cref{fig:micro-graph}, whose upper-bound cost vector is $u = (4, 5, 7, 6, 7)$.
By the lemma, the worst-case cost of a tree is the sum of its edges' upper bounds, so for the three representative trees
\[
\wc{T_1} = 4 + 5 + 7 = 16, \qquad
\wc{T_2} = 4 + 5 + 6 = 15, \qquad
\wc{T_3} = 5 + 7 + 7 = 19.
\]
These values coincide with the scenario-$\cs{2}$ column of \Cref{tab:micro-graph-costs}, as they must: the micro-graph's scenario $\cs{2}$ was defined as the all-upper-bounds vector, so $\cs{2} = u$.

To find the min-max spanning tree, \Cref{cor:mm-interval-polynomial} directs us to a minimum spanning tree under $u$.
Kruskal's algorithm processes the edges in non-decreasing order of $u$, namely $e_1\,(4),\, e_2\,(5),\, e_4\,(6)$, followed by $e_3$ and $e_5$ (both $7$).
It adds $e_1 = \{1,2\}$, $e_2 = \{2,3\}$ and $e_4 = \{3,4\}$; the tree is then complete, and the two remaining edges are not examined.
The result is $T_2 = \{e_1, e_2, e_4\}$ with cost $\MSTcost{u} = 15$.
The three trees evaluated above are only three of the micro-graph's eight spanning trees; what certifies $T_2$ as the min-max tree over all of them is \Cref{cor:mm-interval-polynomial}, applied through this minimum spanning tree computation, rather than the three-way comparison alone.
The entries of $u$ are not all distinct, $e_3$ and $e_5$ sharing the upper bound $7$, so the sufficient condition of \Cref{rem:cost-ties} does not apply here.
The minimum is nonetheless attained by $T_2$ alone: the seven remaining spanning trees have $u$-costs between $16$ and $20$.
Hence $T_2$ is the unique min-max spanning tree, its cost matching the value $\wc{T_2} = 15$ computed above.

\paragraph{The Role of the Lower Bounds.}

\Cref{lem:interval-extremal-cost} shows that the min-max objective depends on the interval data only through the upper bounds $u$; the lower bounds $\ell$ play no role, and the entire interval model compresses to the single cost vector $u$.
Two points are worth drawing out.
First, the separation step in the proof used only that $\Scenarios$ is a product of intervals; it is exactly this absence of coupling between edges that fails under budgeted uncertainty (\Cref{sec:mm-budgeted}), where a shared deviation budget links the edges and the worst case is in general no longer a fixed per-edge sum.
Second, the insensitivity to the lower bounds is special to the min-max objective: the regret objective of \Cref{ch:regret} measures each tree against the scenario-optimal tree, a comparison that does depend on the lower bounds, and there the interval problem becomes substantially harder.

%─────────────────────────────────────────────────────────
% SECTION 3.3: COMPLEXITY AND APPROXIMATION -- LOCKED
%
% Scope of this block: the whole of §3.3, that is the section
% opening, §3.3.1 (constant K: hardness proof, fig:partition-grid,
% small instance, positive results) and §3.3.2 (unbounded K:
% hardness, fig:sat-gadget, O(log K) approximation).
% Replaces everything from this banner down to (not including) the
% banner of §3.4. The §3.4 block is untouched.
%
% Provenance: §3.3 opening + §3.3.1 = Unit 1 v7; §3.3.1 close =
% Unit 2 v5; §3.3.2 = v6. This pass is the final review: grammar,
% redundancy, coherence and prose only, plus the five decisions
% recorded below. No result, label, citation or figure changed.
%
% FINAL-PASS CHANGES
%  (a) Opening roadmap: "no approximation factor below two" ->
%      "no constant-factor approximation exists at all", matching
%      what §3.3.2 actually establishes; "these cited results" ->
%      "these results", since the K=2-style reduction is presented
%      in full there rather than merely cited.
%  (b) §3.3.1 opening: dropped "We prove the hardness in full and
%      record the matching positive results afterwards", which
%      repeated the section roadmap verbatim in substance.
%  (c) Step 2's label shortened to the length of Steps 1 and 3.
%  (d) "the next section" -> "the next subsection" (§3.3.2 is one).
%  (e) Cost-list entries are now bounded by the total cost of all
%      edges "in its own scenario" (the old phrasing implied a
%      single, scenario-independent sum).
%  (f) §3.3.2 theorem: title now names both claims, and the
%      inapproximability carries "unless P = NP", matching the
%      convention already used at the close of §3.3.1.
%  (g) Grammar: "three literals, each a variable or its negation".
%  (h) The three-step approximation chain split into two sentences.
%
% DEPENDS ON (companion fix in chapters/02_foundations.tex):
%   def:discrete-uncertainty must read \R^{|E|}_{\geq 0}. §3.3.2
%   cites it for the non-negativity that eq:mm-aggregate-bracket
%   needs, and §3.3.1's cost-list bound ("between zero and ...")
%   rests on it too.
%
% SOURCES (all verified side by side; see the unit headers in the
% repository history for the full records):
%   §3.3.1  Goerigk Thm 8.4 (construction and proof reproduced),
%           original KouvelisYu1997; KorteVygen2018 Cor 15.28
%           ("PARTITION is NP-complete", verified verbatim);
%           BarahonaPulleyblank1987 (exact spanning tree);
%           Goerigk Thms 8.2, 8.3 (pseudo-polynomial, FPTAS),
%           FPTAS originally AissiBazganVanderpooten2005ESA.
%   §3.3.2  Goerigk Thm 8.5 with Fig 8.2 and the fuller
%           construction of Thm 7.5 (gadget matches edge for edge),
%           original KasperskiZielinski2009; KorteVygen2018
%           Thm 15.22 ("3SAT is NP-complete", Cook 1971);
%           KasperskiZielinski2011 (no constant factor, stated
%           verbatim in Goerigk §8.1); Goerigk Thm 5.21 [Bil+17]
%           and Cor 5.22 [Baa+24] for the O(log K) bound.
%           At p = ln K the factor of Cor 5.22 is exactly e log_2 K,
%           which is what the display derives.
%
% Labels defined here: thm:mm-k2-hard, fig:partition-grid,
%   thm:mm-kconst-pseudo, thm:mm-kconst-fptas, thm:mm-kunbdd-hard,
%   fig:sat-gadget, eq:mm-aggregate-bracket, thm:mm-kunbdd-approx.
% Numbering in the current build: Theorems 3.1 to 3.5, Figures 3.1
%   and 3.2, equation (3.3).
% Local symbols: §3.3.1 v_{i,j}, a_j, b_j, r_j, V_{<j}, W, Q, w(P),
%   T_P; §3.3.2 a_i, b_i, v_{i,j}, L_{i,j}, p. Checked against the
%   thesis-wide symbols (n, m, K, u, \ell_e): no collisions.
% Deliberate choices left standing: no matroid or p-norm machinery
%   ("p-norm" appears once, as a literature signpost); §3.3.2's
%   argument runs as prose rather than a proof environment, marking
%   it as an exposition of a cited proof.
%─────────────────────────────────────────────────────────

\section{Complexity and Approximation}\label{sec:mm-complexity}

Under interval uncertainty the entire model compressed into the single cost vector $u$, and the min-max problem collapsed to one minimum spanning tree computation (\Cref{lem:interval-extremal-cost,cor:mm-interval-polynomial}).
Discrete uncertainty admits no such compression: by~\eqref{eq:mm-evaluator}, the worst-case cost is the largest of the $K$ scenario costs of the tree, and the maximising scenario may change from tree to tree (\Cref{sec:mm-formulation}).
This section turns that obstruction into complexity results, and the price depends on whether the number of scenarios is a fixed constant or part of the input.
For every constant $K$ the problem is weakly $\mathsf{NP}$-hard, already for $K = 2$; this is \Cref{thm:mm-k2-hard}, the hardness result of this chapter that we prove in full, complemented by a pseudo-polynomial algorithm and an FPTAS (\Cref{thm:mm-kconst-pseudo,thm:mm-kconst-fptas}).
When $K$ is part of the input, the hardness becomes strong and no constant-factor approximation exists at all, the best guarantee being an $O(\log K)$-approximation; these results close the section.

\subsection{Discrete Scenarios: Constant \texorpdfstring{$K$}{K}}

Throughout this subsection the number of scenarios $K$ is a constant, fixed independently of the instance.

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
\text{ or }
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

\begin{figure}[htbp]
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

\emph{Step 2: with the vertical edges fixed, a spanning tree is just a choice of one row per column, and its worst-case cost depends on that choice alone.}
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
%   that thm:mm-k2-hard's proof forward-references; G2 explain WHY accessibly; G3
%   coherence (open from 3.2, close into 3.3.2); G4 minimal in-text
%   citation. ONE new named concept (exact spanning tree problem),
%   anchored to knapsack (sec:complexity). No new symbols/macros;
%   notation + Table A unchanged.
% Source (verified side-by-side): Goerigk Thm 8.2 (pseudo-poly, via
%   Thm 5.30) / 8.3 (FPTAS, via Thm 5.33, orig. [ABV05]); exact
%   spanning tree pseudo-poly = Lemma 8.1 [BP87].
% Structure: TWO theorems; thm:mm-kconst-pseudo on the pseudo-poly
%   result (keeps the forward reference from thm:mm-k2-hard exact).
%   New label
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
Each of the $K$ entries is an integer between zero and the total cost of all edges in its own scenario, so when $K$ is fixed only pseudo-polynomially many lists can occur. Searching these few lists replaces the search over the exponentially many spanning trees.
Bundling each edge's $K$ costs into a single number, in a base wide enough that the scenarios cannot interfere, lets the subroutine test whether a prescribed list is realised by some tree. Iterating it over the candidate lists and keeping the realised one of smallest maximum returns the min-max optimum in pseudo-polynomial time \cite[Theorem~8.2]{Goerigk2021RCO}.

The approximation scheme runs the same search on scaled costs.
Dividing every cost by a common factor, chosen from $\varepsilon$ and a bound on the optimum, and then rounding shrinks the magnitudes until only polynomially many lists remain. This turns the pseudo-polynomial search into a polynomial one, while distorting each scenario cost by at most a factor $1 + \varepsilon$.
The returned tree therefore has worst-case cost within $1 + \varepsilon$ of the optimum, in time polynomial in the input and in $1/\varepsilon$ \cite[Theorem~8.3]{Goerigk2021RCO}. This scheme is due to \textcite{AissiBazganVanderpooten2005ESA}.

These results supply the pseudo-polynomial algorithm that \Cref{thm:mm-k2-hard}'s proof invoked to classify its hardness as weak. With an FPTAS available as well, the problem cannot be strongly hard unless $\mathsf{P} = \mathsf{NP}$, since a strongly hard problem would admit neither.
Both guarantees hold only because $K$ is fixed, which keeps the number of cost lists small. This is exactly what the next subsection removes: once $K$ grows with the input, the lists are no longer few, and both the algorithm and the approximation scheme fall away.

%─────────────────────────────────────────────────────────
% §3.3.2  DISCRETE SCENARIOS, UNBOUNDED K
%─────────────────────────────────────────────────────────

\subsection{Discrete Scenarios: Unbounded \texorpdfstring{$K$}{K}}

With $K$ part of the input, the hardness of the problem changes in kind.
For a fixed number of scenarios the difficulty was numerical, the partition problem in disguise, and that is why it was weak and yielded to scaling.
With arbitrarily many scenarios available, a purely logical problem can be encoded instead, one that involves no large numbers at all, and the problem becomes strongly hard and far harder to approximate.

\begin{theorem}[Strong NP-Hardness and Inapproximability]\label{thm:mm-kunbdd-hard}
If $K$ is part of the input, the min-max spanning tree problem under discrete uncertainty is strongly $\mathsf{NP}$-hard, and unless $\mathsf{P} = \mathsf{NP}$ it cannot be approximated within a factor of $2 - \varepsilon$ for any $\varepsilon > 0$.
\end{theorem}

The result is due to \textcite{KasperskiZielinski2009}; the presentation here follows \textcite[Theorem~8.5]{Goerigk2021RCO}.
The reduction starts from 3-SAT, which is $\mathsf{NP}$-complete \cite[Theorem~15.22]{KorteVygen2018}: given a Boolean formula whose clauses each contain three literals, each a variable or its negation, decide whether some truth assignment satisfies every clause.

The graph receives a small gadget for each clause, shown in \Cref{fig:sat-gadget}, and the gadgets are chained one after another, so that every route from one end of the graph to the other must pass through each gadget in turn.
Inside the gadget of clause $i$ lie three parallel two-edge routes from $a_i$ to $b_i$; the first edge $\{a_i, v_{i,j}\}$ of the $j$-th route represents the $j$-th literal $L_{i,j}$ of the clause, while the second edge, like the edges linking consecutive gadgets, costs zero in every scenario.
Crossing the gadget requires at least one of its literal edges, so every spanning tree selects at least one literal in every clause, and the only costs a tree can ever incur sit on the literal edges it selects.
The scenarios then encode the logic: for every complementary pair of occurrences, $L_{i,j} = \neg L_{i',j'}$, a scenario is added that charges cost $1$ to the two edges $\{a_i, v_{i,j}\}$ and $\{a_{i'}, v_{i',j'}\}$ and $0$ to every other edge.

\begin{figure}[htbp]
\centering
\begin{tikzpicture}[
    vertex/.style={circle, draw=RWTHBlue, very thick, fill=RWTHBlue!20, minimum size=8mm, font=\scriptsize\bfseries, inner sep=0pt},
    edge/.style={black, thick},
    elabel/.style={font=\scriptsize}
]
    % chain: ... b_{i-1} -- a_i -- {v_{i,1}, v_{i,2}, v_{i,3}} -- b_i -- a_{i+1} ...
    \node          (dl)    at (-1.4, 0)   {$\cdots$};
    \node[vertex] (bprev) at (0, 0)      {$b_{i-1}$};
    \node[vertex] (ai)    at (2.2, 0)    {$a_i$};
    \node[vertex] (v1)    at (4.6, 1.5)  {$v_{i,1}$};
    \node[vertex] (v2)    at (4.6, 0)    {$v_{i,2}$};
    \node[vertex] (v3)    at (4.6, -1.5) {$v_{i,3}$};
    \node[vertex] (bi)    at (7.0, 0)    {$b_i$};
    \node[vertex] (anext) at (9.2, 0)    {$a_{i+1}$};
    \node          (dr)    at (10.6, 0)  {$\cdots$};
    % connectors between gadgets (free)
    \draw[edge] (dl) -- (bprev);
    \draw[edge] (bprev) -- (ai)  node[midway, above, elabel] {$0$};
    \draw[edge] (bi) -- (anext)  node[midway, above, elabel] {$0$};
    \draw[edge] (anext) -- (dr);
    % literal edges
    \draw[edge] (ai) -- (v1) node[midway, above left, elabel]  {$L_{i,1}$};
    \draw[edge] (ai) -- (v2) node[midway, above, elabel]       {$L_{i,2}$};
    \draw[edge] (ai) -- (v3) node[midway, below left, elabel]  {$L_{i,3}$};
    % free second halves of the routes
    \draw[edge] (v1) -- (bi) node[midway, above right, elabel] {$0$};
    \draw[edge] (v2) -- (bi) node[midway, above, elabel]       {$0$};
    \draw[edge] (v3) -- (bi) node[midway, below right, elabel] {$0$};
\end{tikzpicture}
\caption{The gadget of clause $i$ in the reduction from 3-SAT, chained to its neighbouring gadgets through $b_{i-1}$ and $a_{i+1}$. The edge $\{a_i, v_{i,j}\}$ carries the $j$-th literal $L_{i,j}$ of the clause; a scenario charges cost $1$ to the two literal edges of a complementary pair of occurrences and $0$ everywhere else. All edges labelled $0$ are free in every scenario, so a tree's cost arises only on the literal edges it selects, and crossing the gadget from $a_i$ to $b_i$ forces at least one of them.}
\label{fig:sat-gadget}
\end{figure}

The correspondence is exact in both directions.
If the formula is satisfiable, fix a satisfying assignment and build the tree that takes, in each clause, one literal edge whose literal the assignment makes true, together with all the cost-free edges.
Each gadget contributes five vertices and four selected edges, with one connector joining each consecutive pair, so the selection has one edge fewer than the graph has vertices; being connected as well, it is a spanning tree.
No two selected literals are complementary, both being true under one assignment, so every scenario charges at most one selected edge, and the worst-case cost is at most $1$.
Conversely, suppose some spanning tree avoids both edges of every complementary pair.
Setting each of its selected literals to true is then consistent, and since every clause contributes a selected literal, the formula is satisfied.
For an unsatisfiable formula every spanning tree therefore contains both edges of some pair, and the scenario of that pair costs it $2$; since no scenario has more than two unit-cost edges, no tree costs more than $2$ in any scenario, and the optimal worst-case cost is exactly $2$.

The optimal worst-case cost is thus at most $1$ for a satisfiable formula and exactly $2$ for an unsatisfiable one, so deciding which of the two occurs decides 3-SAT, and the problem is $\mathsf{NP}$-hard.
Every cost in the construction is $0$ or $1$, so the numbers remain polynomially bounded and the hardness is strong, in contrast to the partition reduction, where large weights were essential.

The same gap yields the inapproximability bound.
Suppose that, for some $\varepsilon > 0$, a polynomial-time algorithm always returned a spanning tree of worst-case cost at most $2 - \varepsilon$ times the optimum.
On a satisfiable instance the optimum is at most $1$, so the returned tree would cost at most $2 - \varepsilon < 2$; on an unsatisfiable instance every tree costs exactly $2$, the returned one included.
Reading off whether the returned tree costs less than $2$ would therefore decide 3-SAT in polynomial time, which is impossible unless $\mathsf{P} = \mathsf{NP}$.
The inapproximability is in fact far stronger, no constant factor being achievable at all \cite{KasperskiZielinski2011}; the self-contained $2 - \varepsilon$ bound above is its modest form.

\begin{theorem}[Logarithmic Approximation]\label{thm:mm-kunbdd-approx}
The min-max spanning tree problem under discrete uncertainty can be approximated within a factor of $O(\log K)$.
\end{theorem}

This closes the gap from above, leaving only a logarithmic distance to the no-constant-factor barrier.
The obstacle is the worst-case objective itself: a maximum over the $K$ scenario costs is far harder to minimise than the plain sum that reduced the interval problem to a single spanning tree computation (\Cref{cor:mm-interval-polynomial}).
The remedy is to minimise a sum after all, one built to imitate the maximum.
For a parameter $p \geq 1$, aggregate the $K$ entries of a tree's cost list into the $p$-th root of the sum of their $p$-th powers, in the literature the $p$-norm of the list.
Since the scenario costs are non-negative (\Cref{def:discrete-uncertainty}), the aggregate brackets the worst case,
\begin{equation}\label{eq:mm-aggregate-bracket}
\wc{T}
\;\leq\;
\Biggl(\, \sum_{k=1}^{K} \bigl(\cs{k}(T)\bigr)^{p} \Biggr)^{\!1/p}
\;\leq\;
K^{1/p}\, \wc{T},
\end{equation}
the first inequality because the sum contains the $p$-th power of its largest entry, the second because it consists of $K$ terms, none larger.
The larger $p$ is, the closer the factor $K^{1/p}$ draws to one and the more faithfully the aggregate tracks the maximum.

Unlike the maximum, the aggregate is an objective a greedy method can handle.
Growing the tree edge by edge in the manner of Kruskal's algorithm, but adding at each step, among the edges creating no cycle, one that increases the aggregate the least, returns a spanning tree whose aggregate is at most $p / \ln 2$ times the smallest possible \cite[Theorem~5.21]{Goerigk2021RCO}.
The two error sources now pull against each other: raising $p$ tightens the right-hand inequality of~\eqref{eq:mm-aggregate-bracket} but loosens the greedy guarantee.
Both are paid in succession.
By the left inequality of~\eqref{eq:mm-aggregate-bracket} the greedy tree's worst-case cost is at most its aggregate, and that aggregate is at most $p / \ln 2$ times the smallest aggregate any spanning tree attains, in particular at most $p / \ln 2$ times the aggregate of a min-max optimal tree.
By the right inequality, that last aggregate is at most $K^{1/p}$ times the min-max optimum.
The greedy tree therefore lies within a factor $(p / \ln 2) \cdot K^{1/p}$ of that optimum.
Choosing $p = \ln K$ makes both factors explicit: writing $K = e^{\ln K}$ gives $K^{1/p} = K^{1 / \ln K} = e$, while $p / \ln 2 = \ln K / \ln 2 = \log_2 K$, so that
\[
\frac{p}{\ln 2} \cdot K^{1/p}
\;=\;
e \log_2 K
\;=\;
O(\log K),
\]
which is the guarantee of \Cref{thm:mm-kunbdd-approx} \cite[Corollary~5.22]{Goerigk2021RCO}.
The bound is due to \textcite{Baak2025}, building on the greedy analysis of \textcite{Bilo2017}.

The discrete-uncertainty picture is now complete, and the number of scenarios is its dividing line.
For a fixed number of scenarios the problem is only weakly hard and admits an FPTAS; once that number is part of the input, it becomes strongly hard, approximable within $O(\log K)$ but within no constant factor.
One problem thus carries two kinds of hardness: a numerical one that scaling defeats, and a structural one that it cannot touch.

%─────────────────────────────────────────────────────────
% SECTION 3.4: BUDGETED UNCERTAINTY
%
% VERIFIED NUMERICS (full enumeration, cross-checked two ways):
%   bar_c = midpoints   = (3, 4, 4, 5, 6) = c^(3)
%   hat_c = half-widths = (1, 1, 3, 1, 1); bar_c + hat_c = u
%   Pi = {0} u {hat_c_e : e in E} = {0, 1, 3}
%   Gamma = 2:
%     pi = 0:  c^pi = (4,5,7,6,7) = u,     MST = 15,  +0 = 15
%     pi = 1:  c^pi = (3,4,6,5,6),         MST = 12,  +2 = 14   <-- optimum
%     pi = 3:  c^pi = (3,4,4,5,6) = bar_c, MST = 11,  +6 = 17
%   Optimum 14 at T2; direct cross-check bar_c(T2) = 12 plus 1 + 1.
%   At Gamma = 2 the eight worst-case costs are 14, 15, 16, 16, 17,
%     17, 18, 19.
%   Sweep, Gamma = 0..5: 11, 13, 14, 15, 15, 15; attained by T1 at
%     Gamma = 0 and by T2 from Gamma = 1 onward; attaining levels
%     3, 1, 1, {0 or 1}, 0, 0. Saturation at Gamma = n-1 = 3.
%─────────────────────────────────────────────────────────

\section{Budgeted Uncertainty}\label{sec:mm-budgeted}

The last of the three uncertainty models restores polynomial-time solvability, though by a longer route than interval uncertainty required.
In the budgeted set $\Scenarios^{\Gamma}$ of \Cref{def:budgeted-uncertainty}, each edge may rise from its nominal cost $\bar{c}_e$ by at most its maximum deviation $\hat{c}_e$, but a single budget caps how much deviation the adversary may distribute in total, $\sum_{e \in E} \delta_e \leq \Gamma$.
That shared budget couples the edges of the chosen tree, as \Cref{sec:mm-formulation} observed.
The maximisation therefore no longer separates edge by edge as it did in \Cref{sec:mm-extremal}, because budget spent on one edge is budget another cannot have, and which edges the adversary drives upward depends on the tree.
In general no fixed weight vector reproduces $\wc{T}$, and the collapse to a single minimum spanning tree that settled the interval case (\Cref{cor:mm-interval-polynomial}) is unavailable.

What replaces it is a richer reformulation.
The adversary's tree-dependent choice can be absorbed into a single parameter, though this means solving not one deterministic minimum spanning tree problem but a small family of them, one for each candidate value of that parameter.
As in \Cref{sec:mm-extremal}, we begin with a fixed tree and turn to the minimisation over all trees afterwards.

\paragraph{Worst-Case Cost of a Fixed Tree.}

Fix a spanning tree $T \in \cT$.
Evaluating~\eqref{eq:mm-evaluator} over $\Scenarios^{\Gamma}$ confronts the adversary with a choice its interval counterpart never faced: unless the budget covers the whole tree, it cannot raise every edge of $T$ at once and must decide where a limited allowance does the most damage.
The choice is nevertheless an easy one, namely to spend the budget on the edges of $T$ carrying the largest deviations, raising each of them in full.
The following lemma confirms that this greedy rule is optimal and evaluates the worst case in closed form.

\begin{lemma}[Budgeted Extremal Cost]\label{lem:budgeted-extremal-cost}
Let $\Scenarios^{\Gamma}$ be a budgeted uncertainty set with nominal costs $\bar{c}$, maximum deviations $\hat{c}$, and an integer deviation budget $\Gamma \geq 0$.
Let $T \in \cT$ be a spanning tree, order its edges as $E(T) = \{f_1, \ldots, f_{n-1}\}$ so that $\hat{c}_{f_1} \geq \hat{c}_{f_2} \geq \cdots \geq \hat{c}_{f_{n-1}}$, and set $g := \min\{\Gamma,\, n-1\}$.
Then the worst-case cost of $T$ satisfies
\[
\wc{T} \;=\; \sum_{e \in E(T)} \bar{c}_e \;+\; \sum_{j=1}^{g} \hat{c}_{f_j},
\]
and the maximum in~\eqref{eq:mm-evaluator} is attained at the cost vector $c^{*}$ given by $c^{*}_{f_j} = \bar{c}_{f_j} + \hat{c}_{f_j}$ for $j \leq g$ and $c^{*}_e = \bar{c}_e$ for every other edge $e \in E$.
\end{lemma}

\begin{proof}
Every $c \in \Scenarios^{\Gamma}$ has the form $c_e = \bar{c}_e + \hat{c}_e \delta_e$ with $\delta \in [0,1]^{\abs{E}}$ and $\sum_{e \in E} \delta_e \leq \Gamma$ (\Cref{def:budgeted-uncertainty}).
The nominal part of $c(T)$ is the same for every scenario, so only the deviations are at stake and~\eqref{eq:mm-evaluator} becomes
\begin{equation}\label{eq:mm-budgeted-inner}
\wc{T} \;=\; \sum_{e \in E(T)} \bar{c}_e \;+\; \max\Biggl\{ \sum_{e \in E(T)} \hat{c}_e \delta_e \;:\; \delta \in [0,1]^{\abs{E}},\ \sum_{e \in E} \delta_e \leq \Gamma \Biggr\}.
\end{equation}
It suffices to show that this maximum equals $\sum_{j=1}^{g} \hat{c}_{f_j}$ and is attained at the deviation vector realising $c^{*}$.

The objective in~\eqref{eq:mm-budgeted-inner} involves only the edges of $T$.
Setting $\delta_e := 0$ for every $e \notin E(T)$ therefore leaves the objective unchanged while freeing budget, so some maximiser vanishes outside $E(T)$, and for such $\delta$ the budget constraint reads $\sum_{j=1}^{n-1} \delta_{f_j} \leq \Gamma$.

If $g = 0$, then either $\Gamma = 0$, so that the budget forces every $\delta_e$ to zero, or $T$ has no edges at all; in both cases the maximum is the empty sum, as claimed.
Assume from now on that $g \geq 1$.

The candidate maximiser is the vector $\delta^{*}$ with $\delta^{*}_{f_j} = 1$ for $j \leq g$ and $\delta^{*}_e = 0$ for every other edge, which realises exactly the cost vector $c^{*}$.
Its total deviation is $g \leq \Gamma$, so $\delta^{*}$ is feasible, and its objective value is $\sum_{j=1}^{g} \hat{c}_{f_j}$.
The maximum is therefore at least this value, and it remains to show that no feasible $\delta$ exceeds it.

Let $\delta$ be feasible and vanishing outside $E(T)$.
Comparing its value with that of $\delta^{*}$ and grouping the terms according to whether $j \leq g$,
\[
\sum_{j=1}^{n-1} \hat{c}_{f_j}\, \delta_{f_j} \;-\; \sum_{j=1}^{g} \hat{c}_{f_j}
\;=\;
\sum_{j=1}^{g} \hat{c}_{f_j} \bigl( \delta_{f_j} - 1 \bigr)
\;+\;
\sum_{j = g+1}^{n-1} \hat{c}_{f_j}\, \delta_{f_j}.
\]
We bound the right-hand side by replacing every coefficient $\hat{c}_{f_j}$ with the single value $\hat{c}_{f_g}$, the smallest deviation that $\delta^{*}$ pays for.
By the chosen ordering this replacement can only increase the terms, in each sum for its own reason.
For $j \leq g$ the factor $\delta_{f_j} - 1$ is non-positive while $\hat{c}_{f_j} \geq \hat{c}_{f_g}$, and scaling a non-positive quantity by a smaller coefficient increases it.
For $j > g$ the factor $\delta_{f_j}$ is non-negative while $\hat{c}_{f_j} \leq \hat{c}_{f_g}$, and scaling a non-negative quantity by a larger coefficient increases it.
Both sums then carry $\hat{c}_{f_g}$ as a common factor, so
\[
\sum_{j=1}^{n-1} \hat{c}_{f_j}\, \delta_{f_j} \;-\; \sum_{j=1}^{g} \hat{c}_{f_j}
\;\leq\;
\hat{c}_{f_g} \Biggl( \sum_{j=1}^{n-1} \delta_{f_j} \;-\; g \Biggr).
\]
The bracket is never positive.
If $\Gamma \leq n-1$, then $g = \Gamma$ and the budget constraint gives $\sum_{j=1}^{n-1} \delta_{f_j} \leq \Gamma = g$; if $\Gamma > n-1$, then $g = n-1$ and the bounds $\delta_{f_j} \leq 1$ alone give $\sum_{j=1}^{n-1} \delta_{f_j} \leq n-1 = g$.
Since $\hat{c}_{f_g} \geq 0$ by \Cref{def:budgeted-uncertainty}, the whole right-hand side is at most zero, so $\delta$ does not beat $\delta^{*}$.
The maximum in~\eqref{eq:mm-budgeted-inner} is therefore $\sum_{j=1}^{g} \hat{c}_{f_j}$, attained at $\delta^{*}$.
\end{proof}

The lemma assumes an integer budget, whereas \Cref{def:budgeted-uncertainty} allows any $\Gamma \in [0, \abs{E}]$.
A fractional budget changes little: the adversary fills the largest deviations in the same order, and any budget left over once whole edges are exhausted is spent on the next largest deviation, which adds at most one fractional term to the closed form.
Every budget appearing in what follows is an integer.

Evaluating $\wc{T}$ is now a sorting task.
Ordering the $n-1$ deviations of $E(T)$ and adding the $g$ largest to the nominal cost of $T$ takes $O(n \log n)$ time, which is the polynomial inner evaluation promised for the budgeted model in \Cref{sec:mm-formulation}.

The formula also displays both ends of the budget range.
At $\Gamma = 0$ it returns $\wc{T} = \sum_{e \in E(T)} \bar{c}_e$, so the min-max problem is the deterministic minimum spanning tree problem under the nominal costs $\bar{c}$.
Once $\Gamma \geq n-1$ we have $g = n-1$, every edge of $T$ deviates fully, and $\wc{T} = \sum_{e \in E(T)} (\bar{c}_e + \hat{c}_e)$ is the interval worst case of \Cref{lem:interval-extremal-cost} on the box $\prod_{e \in E} [\bar{c}_e,\, \bar{c}_e + \hat{c}_e]$.
\Cref{def:budgeted-uncertainty} reaches that box at $\Gamma = \abs{E}$, but against spanning trees the interval behaviour sets in earlier, at $\Gamma = n-1$: a tree offers the adversary only $n-1$ edges to spend on, and any further budget is wasted.
Across the whole range $\wc{T}$ is non-decreasing in $\Gamma$, since enlarging the budget enlarges $\Scenarios^{\Gamma}$.
At both ends it is once more a sum of fixed per-edge weights, with $w = \bar{c}$ and $w = \bar{c} + \hat{c}$ respectively.
Only for budgets strictly between them can the coupling described above take effect.

\paragraph{Minimisation over All Trees.}

\Cref{lem:budgeted-extremal-cost} selects deviations by rank, and rank is what stands in the way of minimising over all trees.
Whether an edge belongs to the $g$ largest is a question about the other edges of its tree as much as about the edge itself, so the selection moves with $T$ and cannot be written down edge by edge.

Rank can be traded for a threshold.
Fix a level $\pi \geq 0$ and charge each edge only the part of its deviation that exceeds it, namely $[\hat{c}_e - \pi]^{+}$ with $[z]^{+} := \max\{z, 0\}$, a quantity the edge settles on its own.
An edge whose deviation reaches the level is charged exactly $\pi$ less than that deviation, so a flat payment of $\Gamma\pi$ can make good $\Gamma$ such shortfalls at once.
Placing the level at the $\Gamma$-th largest deviation of $T$, for $1 \leq \Gamma \leq n-1$, balances the account: the $\Gamma$ largest deviations all reach that level and one copy of $\pi$ restores each of them in full, while the remaining edges do not exceed it and are charged nothing.
The account then totals exactly the worst-case cost of \Cref{lem:budgeted-extremal-cost}, and the following lemma shows that no level does better.

\begin{lemma}[Threshold Form of the Worst-Case Cost]\label{lem:budgeted-threshold-form}
Let $\Scenarios^{\Gamma}$ be a budgeted uncertainty set with an integer deviation budget $\Gamma \geq 0$.
Every spanning tree $T \in \cT$ then satisfies
\[
\wc{T} \;=\; \min_{\pi \geq 0} \Biggl[\, \Gamma\pi \;+\; \sum_{e \in E(T)} \bigl( \bar{c}_e + [\hat{c}_e - \pi]^{+} \bigr) \Biggr],
\]
and the minimum is attained at some $\pi \in \{0\} \cup \{\hat{c}_e : e \in E(T)\}$.
\end{lemma}

\begin{proof}
Order $E(T) = \{f_1, \ldots, f_{n-1}\}$ by non-increasing deviations as in \Cref{lem:budgeted-extremal-cost}, and put $g := \min\{\Gamma,\, n-1\}$.
The nominal costs do not involve $\pi$ and move outside the minimisation, so by \Cref{lem:budgeted-extremal-cost} the claim is equivalent to
\[
\min_{\pi \geq 0} \varphi_T(\pi) \;=\; \sum_{j=1}^{g} \hat{c}_{f_j}
\qquad \text{for} \quad
\varphi_T(\pi) \;:=\; \Gamma\pi + \sum_{j=1}^{n-1} \bigl[\hat{c}_{f_j} - \pi\bigr]^{+},
\]
with a minimiser among $0$ and the deviations of $T$.
We treat three ranges of the budget; in each we exhibit a level of the required form that attains the claimed value, and then verify that no level falls below it.

Suppose first that $\Gamma = 0$, so that $g = 0$ and the claimed value is the empty sum.
If $T$ has no edges, then $\varphi_T$ vanishes identically; otherwise the level $\pi = \hat{c}_{f_1}$ makes every bracket vanish, giving $\varphi_T(\hat{c}_{f_1}) = 0$.
No level falls below this, because every summand of $\varphi_T$ is non-negative.

Suppose next that $1 \leq \Gamma \leq n-1$, so that $g = \Gamma$.
At the level $\pi = \hat{c}_{f_g}$ the ordering gives $[\hat{c}_{f_j} - \hat{c}_{f_g}]^{+} = \hat{c}_{f_j} - \hat{c}_{f_g}$ for $j < g$ and $[\hat{c}_{f_j} - \hat{c}_{f_g}]^{+} = 0$ for $j \geq g$, so that
\[
\varphi_T\bigl(\hat{c}_{f_g}\bigr) \;=\; g\, \hat{c}_{f_g} \;+\; \sum_{j=1}^{g-1} \bigl( \hat{c}_{f_j} - \hat{c}_{f_g} \bigr) \;=\; \sum_{j=1}^{g} \hat{c}_{f_j},
\]
the flat payment restoring each of the $g$ largest deviations to its full value.
No level falls below this value either, as two elementary bounds show.
Let $\pi \geq 0$.
The summands of $\varphi_T$ with $j > g$ are non-negative and may be dropped, and each remaining bracket satisfies $[z]^{+} \geq z$, so
\[
\varphi_T(\pi) \;\geq\; \Gamma\pi + \sum_{j=1}^{g} \bigl[\hat{c}_{f_j} - \pi\bigr]^{+}
\;\geq\; \Gamma\pi + \sum_{j=1}^{g} \bigl(\hat{c}_{f_j} - \pi\bigr)
\;=\; \sum_{j=1}^{g} \hat{c}_{f_j},
\]
the final equality because $\Gamma = g$, so that the $g$ subtracted copies of $\pi$ cancel the flat payment.

Suppose finally that $\Gamma > n-1$, so that $g = n-1$.
At the level $\pi = 0$ every bracket returns its deviation unchanged, giving $\varphi_T(0) = \sum_{j=1}^{n-1} \hat{c}_{f_j}$.
For any $\pi \geq 0$, applying $[z]^{+} \geq z$ to all $n-1$ summands,
\[
\varphi_T(\pi) \;\geq\; \Gamma\pi + \sum_{j=1}^{n-1} \bigl(\hat{c}_{f_j} - \pi\bigr) \;=\; \sum_{j=1}^{n-1} \hat{c}_{f_j} + \bigl(\Gamma - (n-1)\bigr)\pi,
\]
and the final term is non-negative because $\Gamma > n-1$ and $\pi \geq 0$.

The three ranges partition the integer budgets $\Gamma \geq 0$, and in each the exhibited level lies in $\{0\} \cup \{\hat{c}_e : e \in E(T)\}$, which proves the lemma.
\end{proof}

The attaining level need not be unique: at $\Gamma = n-1$ the minimum is attained both at $\pi = \hat{c}_{f_{n-1}}$ and at $\pi = 0$, where the brackets already return every deviation in full.
For the minimisation over all trees this does not matter; what matters is that some minimiser lies in a set fixed in advance.
The candidate set of \Cref{lem:budgeted-threshold-form} still depends on $T$, but enlarging it to the deviations of all edges makes it the same for every tree, and the two minimisations can then be interchanged.

\begin{theorem}[Polynomial Solvability under Budgeted Uncertainty]\label{thm:mm-budgeted-poly}
Let $\Scenarios^{\Gamma}$ be a budgeted uncertainty set with an integer deviation budget $\Gamma \geq 0$, put $\Pi := \{0\} \cup \{\hat{c}_e : e \in E\}$, and for $\pi \in \Pi$ let $c^{\pi}$ be the cost vector with $c^{\pi}_e := \bar{c}_e + [\hat{c}_e - \pi]^{+}$.
Then the min-max spanning tree problem under budgeted uncertainty satisfies
\[
\min_{T \in \cT} \wc{T} \;=\; \min_{\pi \in \Pi} \Bigl[\, \Gamma\pi + \MSTcost{c^{\pi}} \Bigr],
\]
and if $\pi^{*}$ attains the right-hand minimum, then every minimum spanning tree under $c^{\pi^{*}}$ is min-max optimal.
The problem is solvable in $O(m^{2} \log n)$ time; in particular, it lies in $\mathsf{P}$.
\end{theorem}

The result is due to \textcite{BertsimasSim2003}, who prove it for min-max problems over an arbitrary set of binary solutions; the presentation here follows \textcite[Theorem~4.21]{Goerigk2021RCO}, in whose derivation the threshold form of \Cref{lem:budgeted-threshold-form} appears as an intermediate reformulation.

\begin{proof}
For every $T \in \cT$ the set $\Pi$ contains $\{0\} \cup \{\hat{c}_e : e \in E(T)\}$ and therefore, by \Cref{lem:budgeted-threshold-form}, a minimiser of the threshold form; enlarging the candidate set in this way is what removes its dependence on $T$ \cite[Lemma~4.20]{Goerigk2021RCO}.
Restricting a minimisation to a subset containing a minimiser leaves its value unchanged, so
\[
\wc{T} \;=\; \min_{\pi \in \Pi} \Biggl[\, \Gamma\pi + \sum_{e \in E(T)} c^{\pi}_e \Biggr]
\qquad \text{for every } T \in \cT.
\]
Minimising over $\cT$ and interchanging the two minimisations, both of which run over finite sets, gives
\[
\min_{T \in \cT} \wc{T}
\;=\; \min_{\pi \in \Pi} \, \min_{T \in \cT} \Biggl[\, \Gamma\pi + \sum_{e \in E(T)} c^{\pi}_e \Biggr]
\;=\; \min_{\pi \in \Pi} \Biggl[\, \Gamma\pi + \min_{T \in \cT} \sum_{e \in E(T)} c^{\pi}_e \Biggr],
\]
the second step because $\Gamma\pi$ does not depend on the tree.
At fixed $\pi$ the vector $c^{\pi}$ is one cost vector for all trees alike, so the inner problem is the deterministic minimum spanning tree problem~\eqref{eq:mst-definition} under $c^{\pi}$, of value $\MSTcost{c^{\pi}}$.
This proves the identity.

For the second claim, let $\pi^{*}$ attain the outer minimum and let $T^{*}$ be a minimum spanning tree under $c^{\pi^{*}}$.
Then
\[
\min_{T \in \cT} \wc{T} \;=\; \Gamma\pi^{*} + \sum_{e \in E(T^{*})} c^{\pi^{*}}_e
\;\geq\; \min_{\pi \in \Pi} \Biggl[\, \Gamma\pi + \sum_{e \in E(T^{*})} c^{\pi}_e \Biggr]
\;=\; \wc{T^{*}}
\;\geq\; \min_{T \in \cT} \wc{T},
\]
so equality holds throughout and $T^{*}$ is min-max optimal.

It remains to bound the running time.
The set $\Pi$ holds at most $m+1$ levels, one deviation per edge together with zero.
A single level costs $O(m \log n)$ time: assembling $c^{\pi}$ needs one subtraction per edge, and Kruskal's algorithm then returns $\MSTcost{c^{\pi}}$ in $O(m \log n)$ time (\Cref{sec:kruskal-prim}).
Sweeping the levels and retaining the smallest value therefore takes $O(m^{2} \log n)$ time in total.
\end{proof}

Under interval uncertainty a single minimum spanning tree computation settled the problem (\Cref{cor:mm-interval-polynomial}); under a budget, at most $m+1$ of them do.
The adversary's choice of where to spend, which changes from tree to tree, has been reduced to a single level, and levels are few enough to enumerate.


\paragraph{Worked Example.}

The micro-graph of \Cref{fig:micro-graph} shows both how the enumeration of \Cref{thm:mm-budgeted-poly} runs and what the budget buys as it grows.
We take nominal costs at the interval midpoints and maximum deviations at the half-widths,
\[
\bar{c} = (3, 4, 4, 5, 6), \qquad \hat{c} = (1, 1, 3, 1, 1),
\]
so that $\bar{c}$ coincides entry by entry with the midpoint scenario $\cs{3}$ of \Cref{tab:micro-graph-costs}, while $\bar{c} + \hat{c} = (4, 5, 7, 6, 7)$ agrees with the upper-bound scenario $\cs{2}$, the vector $u$ of \Cref{sec:mm-extremal}.
Neither coincidence belongs to the budgeted model, which is fixed by $\bar{c}$, $\hat{c}$ and $\Gamma$ alone; both follow from how we have instantiated it here.
They are convenient nonetheless, because the two ends of the budget range will return values that \Cref{tab:micro-graph-costs} has already recorded.
We first work through one budget completely and then let the budget vary.

Fix the budget $\Gamma = 2$.
\Cref{lem:budgeted-extremal-cost} evaluates each tree directly: the worst-case cost of a tree is its nominal cost plus its two largest deviations, so for the three representative trees
\[
\wc{T_1} = 11 + 3 + 1 = 15, \quad
\wc{T_2} = 12 + 1 + 1 = 14, \quad
\wc{T_3} = 14 + 3 + 1 = 18.
\]
The remaining five spanning trees have worst-case costs between 16 and 19, so $T_2$ is the unique min-max tree, at 14.

\Cref{thm:mm-budgeted-poly} reaches the same tree without evaluating any tree individually.
Its candidate levels are
\[
\Pi \;=\; \{0\} \cup \{\hat{c}_e : e \in E\} \;=\; \{0, 1, 3\},
\]
three rather than the six that $m + 1$ permits, since the repeated deviations collapse to one candidate.
\Cref{tab:budgeted-micrograph} performs the three deterministic solves.
Its outer rows hold two cost vectors the chapter has met before: at $\pi = 0$ nothing is discounted and $c^{0} = u$, while at $\pi = 3$ every deviation is absorbed and $c^{3} = \bar{c}$.
The middle row wins: the level $\pi^{*} = 1$ gives the total $12 + 2 \cdot 1 = 14$, with minimum spanning tree $T_2$.
The two routes agree, in the value and in the tree.

\begin{table}[htbp]
\centering
\caption{The reformulation of \Cref{thm:mm-budgeted-poly} on the micro-graph at $\Gamma = 2$. Each row solves one deterministic minimum spanning tree problem under the modified costs $c^{\pi}$; the smallest total identifies the optimal value and an optimal tree.}
\label{tab:budgeted-micrograph}
\begin{tabular}{c c c c c}
\toprule
$\pi$ & $c^{\pi} = \bar{c} + [\hat{c} - \pi]^{+}$ & $\MSTcost{c^{\pi}}$ & $\Gamma\pi$ & Total \\
\midrule
$0$ & $(4, 5, 7, 6, 7)$ & $15$ & $0$ & $15$ \\
$1$ & $(3, 4, 6, 5, 6)$ & $12$ & $2$ & $\mathbf{14}$ \\
$3$ & $(3, 4, 4, 5, 6)$ & $11$ & $6$ & $17$ \\
\bottomrule
\end{tabular}
\end{table}

One budget exercises the machinery; the range of budgets shows the model.
\Cref{fig:budgeted-sweep} records the optimal value at every integer budget, and we read it from left to right.
\Cref{tab:budgeted-micrograph} keeps working throughout: only its flat-payment column depends on $\Gamma$, so the same three rows answer each budget that follows.

\begin{figure}[htbp]
\centering
\begin{tikzpicture}[
    axis/.style={TextGray, thick, -{Stealth[length=2mm]}},
    guide/.style={RuleGray, dashed, thick},
    ptTtwo/.style={circle, fill=RWTHBlue, inner sep=1.8pt},
    ptTone/.style={circle, fill=RWTHRed, inner sep=1.8pt},
    lbl/.style={font=\scriptsize, TextGray},
    xscale=1.55, yscale=0.92
]
    % saturated region Gamma >= n-1
    \fill[RWTHLightBlue!25] (3, -0.45) rectangle (5.55, 4.55);
    % reference optima
    \draw[guide] (-0.15, 4) -- (5.55, 4) node[right, lbl] {$\MSTcost{u} = 15$};
    \draw[guide] (-0.15, 0) -- (5.55, 0) node[right, lbl] {$\MSTcost{\bar{c}} = 11$};
    % axes
    \draw[axis] (-0.15, -0.45) -- (5.85, -0.45) node[right, lbl] {$\Gamma$};
    \draw[axis] (-0.15, -0.45) -- (-0.15, 4.85) node[above, lbl] {$\min_{T \in \cT} \wc{T}$};
    \foreach \x in {0,1,2,3,4,5} {
        \draw[TextGray] (\x, -0.45) -- (\x, -0.6) node[below, lbl] {$\x$};
    }
    % optima: (0,11) (1,13) (2,14) (3,15) (4,15) (5,15), plotted as v - 11
    \node[ptTone] at (0, 0) {};
    \node[ptTtwo] at (1, 2) {};
    \node[ptTtwo] at (2, 3) {};
    \node[ptTtwo] at (3, 4) {};
    \node[ptTtwo] at (4, 4) {};
    \node[ptTtwo] at (5, 4) {};
    % values at the points
    \node[lbl] at (0, 0.34) {$11$};
    \node[lbl] at (1, 2.34) {$13$};
    \node[lbl] at (2, 3.34) {$14$};
    \node[lbl] at (3, 4.32) {$15$};
    \node[lbl] at (4, 4.32) {$15$};
    \node[lbl] at (5, 4.32) {$15$};
    % attaining trees and the switch
    \node[lbl, RWTHRed]  at (0, 0.68) {$T_1$};
    \node[lbl, RWTHBlue] at (1, 2.68) {$T_2$};
    \draw[-{Stealth[length=1.6mm]}, TextGray, thick] (0.18, 0.35) to[bend left=25] (0.82, 1.75);
\end{tikzpicture}
\caption{Optimal budgeted min-max value on the micro-graph at each integer budget $\Gamma$. The dashed lines mark the deterministic optimum under $\bar{c}$ and the interval optimum under $u$; the shaded region is $\Gamma \geq n-1$, where every tree's worst case equals its interval worst case. The red point is the only budget at which $T_1$ is optimal; from $\Gamma = 1$ onwards the optimum is attained by $T_2$ (arrow: the switch).}
\label{fig:budgeted-sweep}
\end{figure}

At the left end, $\Gamma = 0$, the optimum is 11, attained by $T_1$: the deterministic minimum spanning tree under $\bar{c}$, whose value appears in \Cref{tab:micro-graph-costs} as $\MSTcost{\cs{3}}$.
At this budget the flat payments vanish, so the $\MSTcost{c^{\pi}}$ column alone decides: its smallest entry, 11 at the level $\pi = 3$, is the optimum.
The level thus moves opposite to the budget here: with nothing to spend, the cheapest account is the one that absorbs every deviation.
The level is not a budget in disguise, and reading it as one inverts its role.

One unit of budget already moves the tree.
At $\Gamma = 1$ the optimum is 13, attained by $T_2$, while $T_1$ falls to second place: $\wc{T_1} = 11 + 3 = 14$ against $\wc{T_2} = 12 + 1 = 13$.
The reason is $e_3$, an edge of $T_1$ whose deviation 3 is the largest in the graph.
$T_1$ is therefore the cheapest tree when no deviation can occur, and among the most exposed as soon as one can; the optimum switches to $T_2$ at $\Gamma = 1$ and stays there.

At the right end the value saturates.
From $\Gamma = 3$ onwards the optimum is 15, which is $\MSTcost{u}$ from \Cref{sec:mm-extremal}: every tree's worst case now equals its interval worst case.
Saturation arrives at $\Gamma = n-1 = 3$, well before the $\Gamma = \abs{E} = 5$ at which \Cref{def:budgeted-uncertainty} reaches the full box, because a spanning tree of this graph offers only three edges on which to spend.
Exactly at the saturation budget the attaining level is no longer unique, $\pi = 0$ and $\pi = 1$ both yielding 15, the tie anticipated after \Cref{lem:budgeted-threshold-form}; beyond it, nothing changes.

Taken together, the three readings show the budget acting as a dial between the deterministic problem and the interval model.
Its ends reproduce two optima already on record, in \Cref{tab:micro-graph-costs} and \Cref{sec:mm-extremal}, while in between the value climbs and, more tellingly, the optimal tree changes: a single parameter carries the problem from one model to the other and alters the decision on the way.

% END OF SECTION 3.4

%─────────────────────────────────────────────────────────
% CHAPTER SUMMARY
% Unnumbered, matching Ch2. Mechanism and interpretation only:
% the result inventory belongs to tab:complexity-landscape in Ch5,
% and restating it here would duplicate that table.
%─────────────────────────────────────────────────────────

\section*{Summary}
\addcontentsline{toc}{section}{Summary}

The three uncertainty models of this chapter are separated by a single question: how far the adversary's best answer depends on the tree it faces.
Under interval uncertainty it does not depend on the tree at all.
\Cref{lem:interval-extremal-cost} identifies one cost vector, the upper bounds, that is simultaneously worst for every tree, so the worst-case cost is a sum of fixed per-edge weights and the outer minimisation is a single deterministic minimum spanning tree computation (\Cref{cor:mm-interval-polynomial}).

Under discrete uncertainty the dependence is genuine.
A tree that is safe in one scenario may be exposed in another, so which scenario witnesses the worst case is a property of the tree, and in general no fixed vector can stand in for the scenario set.
The trade-off this forces is what the reductions of \Cref{sec:mm-complexity} exploit, and two scenarios already suffice to make the problem weakly $\mathsf{NP}$-hard.

Under budgeted uncertainty the answer depends on the tree as well, since the budget is spent on the chosen tree's own largest deviations, and yet the problem remains polynomial.
The reason is that a single scalar removes the dependence: once a level is fixed, every tree is costed edge by edge again, and at most $m + 1$ levels can matter (\Cref{thm:mm-budgeted-poly}).

The dividing line is therefore not linearity of the worst-case cost, which two of the three models lack in general.
It is whether the dependence on the tree can be stripped away by a bounded amount of extra work: none is needed under intervals, one enumerated parameter suffices under a budget, and under discrete scenarios no such device can exist unless $\mathsf{P} = \mathsf{NP}$.

The extremal characterisations, the two-scenario reduction and the budgeted reformulation are proved in full here; the strong-hardness construction and the logarithmic approximation are developed from their sources in the text; only the pseudo-polynomial algorithm and the approximation scheme for constant $K$ are stated on the authority of the literature.
Two of the three models return in \Cref{ch:regret} under the regret objective, where the ordering changes: the interval case, the straightforward one here, becomes the hard one.

% END OF CHAPTER 3```

