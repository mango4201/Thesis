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
% PREAMBLE DEPENDENCY: §3.2 uses a corollary environment.
%   Add to main.tex, sharing the theorem counter:
%     \newtheorem{corollary}[theorem]{Corollary}
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
% SECTION 3.3: COMPLEXITY RESULTS (3.5 pages)
%─────────────────────────────────────────────────────────
\section{Complexity and Approximation}\label{sec:mm-complexity}

\subsection{Discrete Scenarios: Constant K}

% PROOF INVENTORY: 1 full proof (1.2 pg) + 1 cited theorem (0.4 pg)

% SOURCE: Goerigk Theorem 8.4 (Kouvelis-Yu 1997)
\begin{theorem}[Partition Reduction]\label{thm:mm-k2-hard}
% TODO: Statement
% Min-Max spanning tree with K=2 discrete scenarios is weakly NP-hard.
\end{theorem}
\begin{proof}
% TODO: FULL PROOF (1.2 pg) — This is our representative hardness proof
% Reduction from PARTITION problem
% 
% SOURCE VERIFICATION:
% - Goerigk (2021), Theorem 8.4, page 245
% - Original: Kouvelis-Yu (1997)
% - Reduction: PARTITION → Min-Max ST K=2
% - Verified against /mnt/project/24_Robust_Optimization_book-1.pdf
%
% CONSTRUCTION:
% Given PARTITION instance: n items, weights w_i, target Q = (Σw_i)/2
% Build grid graph G=(V,E):
%   - Vertices: V = {v_{i,j} : i∈{1,2}, j∈{0,...,n}}
%   - Edges: E = horizontal + vertical
%   - Two scenarios: c^(1) and c^(2)
%     Scenario 1: row 1 has weights, row 2 has zeros
%     Scenario 2: row 2 has weights, row 1 has zeros
%   - All vertical edges cost zero in both scenarios
%
% EQUIVALENCE:
% PARTITION has solution ⟺ Min-Max ST has value Q
%
% (⟹) If partition P exists with Σ_{i∈P} w_i = Q:
%   Construct T using row 1 edges for i∈P, row 2 for i∉P
%   Then c^(1)(T) = Q and c^(2)(T) = Q
%
% (⟸) If optimal T* has value Q:
%   Let Q₁ = cost in scenario 1, Q₂ = cost in scenario 2
%   Since Q₁ + Q₂ = 2Q and max{Q₁,Q₂} = Q
%   Must have Q₁ = Q₂ = Q → induces partition
%
% Figure 3.1 will show grid graph structure
\end{proof}

% TODO: Figure 3.1 (0.3 pg): Grid graph construction for partition reduction
%   - TikZ figure showing 2×(n+1) grid
%   - Edge labels with scenario costs
%   - Caption: "Grid graph G for reduction from PARTITION..."
%   \label{fig:partition-grid}

% SOURCE: Goerigk Theorem 8.2-8.3 (Aissi-Bazgan-Vanderpooten 2005)
\begin{theorem}[Pseudo-Polynomial Algorithm]\label{thm:mm-kconst-pseudo}
% TODO: Statement + explanation (0.4 pg)
% For constant K, Min-Max ST admits:
%   (i) Pseudo-polynomial algorithm
%   (ii) FPTAS
\end{theorem}

% TODO: Explanation (cite + intuition, NO proof):
%   - Based on multicriteria Exact-P method
%   - Intuition: Treat as K-criteria optimisation
%   - DP on Pareto frontier → pseudo-polynomial in cost magnitudes
%   - FPTAS via scaling technique
%   - SOURCE: Goerigk Thm 8.2-8.3, Aissi-Bazgan-Vanderpooten (2005)

\subsection{Discrete Scenarios: Unbounded K}

% PROOF INVENTORY: 2 cited theorems (sketch + citation, ~1.5 pg)

% SOURCE: Goerigk Theorem 8.5 (Kasperski-Zielinski 2009)
\begin{theorem}[Strong NP-Hardness]\label{thm:mm-kunbdd-hard}
% TODO: Statement
% Min-Max ST with K part of input is strongly NP-hard and
% not approximable within (2-ε) for any ε>0.
\end{theorem}

% TODO: Sketch + citation (0.8 pg, NO full proof):
%   - Reduction from 3-SAT
%   - Gadget construction: each clause → subgraph
%   - Scenarios encode literal conflicts
%   - 3-SAT solution ⟺ Min-Max cost = 1
%   - SOURCE: Goerigk Thm 8.5, Kasperski-Zielinski (2009)
%   - Reference similar construction in Goerigk Thm 7.5 for shortest path

% TODO: Inapproximability (0.4 pg):
%   - If (2-ε)-approximable, could solve 3-SAT
%   - SOURCE: Kasperski-Zielinski (2009)

% TODO: Midpoint O(K)-approximation (0.5 pg):
%   - Midpoint heuristic: solve MST at c_mid = average over scenarios
%   - Achieves O(K) ratio
%   - SOURCE: Goerigk Corollary 5.22
%   - Open gap: O(K) vs O(log^(1-ε) n) lower bound

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

