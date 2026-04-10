## chapters/02_foundations.tex

```tex
## chapters/02_foundations.tex

```tex
%═══════════════════════════════════════════════════════════
% CHAPTER 2: FOUNDATIONS
% 
% Status: SECTIONS 2.1-2.2 FINALISED
% Date: 2026-04-10
% Final review: 12 changes applied (cost note, cycle def, 
%   property binding, E(P) clarification, only-tree-edge 
%   justification, Remark on Cost Ties, expanded iterations,
%   \Cref fixes throughout)
%═══════════════════════════════════════════════════════════

\chapter{Foundations}\label{ch:foundations}

This chapter establishes the mathematical foundations for analysing robust spanning tree problems.
We prove minimum spanning tree optimality criteria from first principles (\Cref{sec:mst-criteria}), formalise two uncertainty models -- discrete scenarios and interval bounds (\Cref{sec:uncertainty}) -- and define the Min-Max and Min-Max Regret objectives that drive our complexity analyses in \Cref{ch:minmax,ch:regret}.
A fixed micro-graph introduced in \Cref{sec:graph-notation} serves as a running example throughout.

%─────────────────────────────────────────────────────────
% SECTION 2.1: GRAPHS, TREES, AND NOTATION
%─────────────────────────────────────────────────────────
\section{Graphs, Trees, and Notation}\label{sec:graph-notation}

We work exclusively with undirected graphs throughout this thesis.
A \emph{\textcolor{RWTHBlue}{graph}} is a pair $G = (V, E)$, where $V$ is a finite non-empty set of \emph{vertices} (or \emph{nodes}) and $E \subseteq \{\{u, v\} \mid u, v \in V, u \neq v\}$ is a set of \emph{edges}.
Each edge connects exactly two distinct vertices; we write $\{u, v\} \in E$ or equivalently $uv \in E$ to denote an edge between $u$ and $v$.
A graph is called \emph{\textcolor{RWTHBlue}{simple}} if it contains no multiple edges (at most one edge between any pair of vertices) and no loops (edges connecting a vertex to itself).
All graphs considered in this thesis are simple.
We denote $n = |V|$ and $m = |E|$ for the number of vertices and edges, respectively.
Two vertices $u, v \in V$ are \emph{adjacent} (or \emph{neighbours}) if $uv \in E$, and the \emph{degree} of a vertex $v$, written $\deg(v)$, is the number of edges incident to $v$.

A \emph{\textcolor{RWTHBlue}{path}} in $G$ is a sequence of distinct vertices $v_0, v_1, \ldots, v_k$ such that $\{v_{i-1}, v_i\} \in E$ for all $i \in \{1, \ldots, k\}$.
The \emph{length} of this path is $k$ (the number of edges).
A \emph{\textcolor{RWTHBlue}{cycle}} is a sequence of distinct vertices $v_0, v_1, \ldots, v_k$ with $k \geq 2$ such that $\{v_{i-1}, v_i\} \in E$ for all $i \in \{1, \ldots, k\}$ and $\{v_k, v_0\} \in E$.

A graph $G$ is \emph{\textcolor{RWTHBlue}{connected}} if for every pair of vertices $u, v \in V$, there exists a path from $u$ to $v$.
A spanning tree can only exist if $G$ is connected, since the tree must reach every vertex.
\textcolor{RWTHOrange}{\textbf{Throughout this thesis, we assume all graphs are connected.}}

For a non-empty proper subset $X \subseteq V$ (that is, $\emptyset \neq X \subsetneq V$), the \emph{\textcolor{RWTHBlue}{cut}} induced by $X$ is the set of edges crossing the partition $(X, V \setminus X)$:
\[
\cut{X} = \{\{u, v\} \in E \mid u \in X,\, v \in V \setminus X\}.
\]
Intuitively, $\cut{X}$ comprises all edges with exactly one endpoint in $X$.
See \Cref{fig:cut-illustration} for a visual representation.

\begin{figure}[htbp]
\centering
\begin{tikzpicture}[
    scale=1.2,
    vertex/.style={circle, draw=black, thick, minimum size=6mm},
    inset/.style={circle, draw=black, thick, fill=RWTHLightBlue!30, minimum size=6mm},
    outset/.style={circle, draw=black, thick, fill=RWTHOrange!20, minimum size=6mm},
    cutedge/.style={very thick, color=RWTHRed},
    normaledge/.style={thick, color=black!40}
]
    % Vertices in X (blue shaded)
    \node[inset] (v1) at (0, 1.5) {$1$};
    \node[inset] (v2) at (0, 0) {$2$};
    
    % Vertices in V\X (orange shaded)
    \node[outset] (v3) at (2, 1.5) {$3$};
    \node[outset] (v4) at (2, 0) {$4$};
    
    % Edges within X
    \draw[normaledge] (v1) -- (v2);
    
    % Edges within V\X
    \draw[normaledge] (v3) -- (v4);
    
    % Cut edges (red, thick)
    \draw[cutedge] (v1) -- (v3);
    \draw[cutedge] (v2) -- (v3);
    \draw[cutedge] (v2) -- (v4);
    
    % Labels
    \node[anchor=east, color=RWTHBlue] at (-0.3, 0.75) {$X$};
    \node[anchor=west, color=RWTHOrange] at (2.3, 0.75) {$V \setminus X$};
    
    % Cut label
    \node[anchor=north, color=RWTHRed, font=\small\bfseries] at (1, -0.7) {$\cut{X} = \{\{1,3\}, \{2,3\}, \{2,4\}\}$};
\end{tikzpicture}
\caption{Illustration of a cut $\cut{X}$ induced by vertex set $X = \{1, 2\}$. The cut $\cut{X}$ consists of the three red edges connecting the blue-shaded vertices in $X$ to the orange-shaded vertices in $V \setminus X$.}
\label{fig:cut-illustration}
\end{figure}

Each edge $e \in E$ has an associated \emph{cost} $c_e \in \R$.
We represent the cost structure either as a \emph{\textcolor{RWTHBlue}{cost function}} $c \colon E \to \R$ or equivalently as a \emph{cost vector} $c \in \R^{|E|}$ indexed by the edges.
Costs may be positive, negative, or zero; the MST algorithms discussed in \Cref{sec:kruskal-prim} handle all cases correctly.
For any subset $F \subseteq E$, we write $c(F) = \sum_{e \in F} c_e$ for the total cost of the edge set $F$.

\paragraph{Spanning Trees.}

A \emph{\textcolor{RWTHBlue}{spanning tree}} of $G$ is a connected acyclic subgraph $T \subseteq G$ that includes all vertices of $G$, that is, $V(T) = V(G)$.
We denote by $\cT$ the set of all spanning trees of $G$.
Since spanning trees are both connected and acyclic, they satisfy precisely $|E(T)| = n - 1$ edges, where $E(T) \subseteq E$ denotes the edge set of $T$.
For a spanning tree $T \in \cT$, we write $c(T) = c(E(T)) = \sum_{e \in E(T)} c_e$ for the \emph{\textcolor{RWTHBlue}{cost}} of $T$.

The \emph{\textcolor{RWTHBlue}{minimum spanning tree problem}} (MST) seeks a spanning tree of minimum cost:
\begin{equation}\label{eq:mst-definition}
\MSTcost{c} = \min_{T \in \cT} c(T).
\end{equation}
Any tree $T^* \in \cT$ satisfying $c(T^*) = \MSTcost{c}$ is called an \emph{MST} under cost vector $c$.
The MST is unique if all edge costs are distinct; when edge costs have ties (equal values), multiple spanning trees may achieve the minimum cost $\MSTcost{c}$.

A spanning tree $T$ possesses three fundamental structural properties that we exploit repeatedly:

\begin{enumerate}[label=\textbf{(P\arabic*)},ref=P\arabic*]
\item\label{prop:tree-unique-path} For any two vertices $u, v \in V$, there exists a \emph{unique} path in $T$ connecting $u$ and $v$.
\item\label{prop:tree-remove-disconnects} Removing any edge $e \in E(T)$ disconnects $T$ into exactly two connected components.
\item\label{prop:tree-add-cycle} Adding any edge $f \in E \setminus E(T)$ to $T$ creates exactly one cycle.
\end{enumerate}

Property~\ref{prop:tree-unique-path} follows from the definition of trees as connected acyclic graphs: connectivity ensures path existence, while acyclicity guarantees uniqueness (a second path would form a cycle).
Properties~\ref{prop:tree-remove-disconnects} and~\ref{prop:tree-add-cycle} are immediate consequences: removing an edge from a minimally connected subgraph (a tree on $n$ vertices has exactly $n-1$ edges) must disconnect it, while adding an edge to a maximally acyclic subgraph must create a cycle \cite[Theorem~2.1]{KorteVygen2018}.

\paragraph{Fundamental Structures.}

We now introduce two graph-theoretic constructs -- fundamental cycles and fundamental cuts -- that play a central role in characterising MST optimality.
Let $T \in \cT$ be a spanning tree and let $f \in E \setminus E(T)$ be an edge not in $T$ with endpoints $u$ and $v$.
By property~\ref{prop:tree-add-cycle}, adding $f$ to $T$ creates a unique cycle.
Since $T$ is a tree, by property~\ref{prop:tree-unique-path}, there exists a unique path $P$ in $T$ from $u$ to $v$.
We call the cycle $C_f = E(P) \cup \{f\}$ (where $E(P)$ denotes the edges of path $P$) the \emph{\textcolor{RWTHBlue}{fundamental cycle}} of $f$ with respect to $T$.

Similarly, let $e \in E(T)$ be an edge in the tree $T$.
By property~\ref{prop:tree-remove-disconnects}, removing $e$ from $T$ partitions $V$ into exactly two connected components.
Let $X_e \subseteq V$ denote one of these components (the choice is arbitrary; either component induces the same cut).
The \emph{\textcolor{RWTHBlue}{fundamental cut}} of $e$ with respect to $T$ is the cut $\cut{X_e}$ induced by $X_e$.
Crucially, $e$ is the \emph{only} tree edge in $\cut{X_e}$; all other edges in the cut belong to $E \setminus E(T)$.
This follows because $X_e$ is a connected component of $T \setminus \{e\}$: any other tree edge crossing the partition would connect $X_e$ to $V \setminus X_e$, contradicting the component structure.
These fundamental structures underpin the MST optimality criteria developed in \Cref{sec:mst-criteria}: fundamental cycles correspond to potential edge exchanges for non-tree edges, while fundamental cuts correspond to exchanges for tree edges.

\paragraph{Running Example: The Micro-graph.}

To provide concrete illustrations of concepts throughout this thesis, we introduce a fixed four-vertex graph that serves as our running example.
Let $G = (V, E)$ with vertex set $V = \{1, 2, 3, 4\}$ and five edges:
\begin{align*}
e_1 &= \{1, 2\}, \quad e_2 = \{2, 3\}, \quad e_3 = \{2, 4\}, \\
e_4 &= \{3, 4\}, \quad e_5 = \{1, 3\}.
\end{align*}%
The structure is depicted in \Cref{fig:micro-graph}.
Vertex~2 serves as a central hub with edges to vertices 1, 3, and 4, while edges $e_5 = \{1, 3\}$ and $e_4 = \{3, 4\}$ provide alternative connections.

In anticipation of the robust optimisation framework of \Cref{sec:uncertainty}, we associate each edge $e_i$ with an \emph{interval cost} $[\ell_{e_i}, u_{e_i}]$ representing uncertainty in edge costs:
\begin{align*}
e_1 &\colon [2, 8], \quad e_2 \colon [1, 5], \quad e_3 \colon [3, 7], \\
e_4 &\colon [2, 6], \quad e_5 \colon [4, 9].
\end{align*}%
From these intervals, we derive three \emph{discrete scenarios} that instantiate specific cost realisations: $c^{(1)} = (2, 1, 3, 2, 4)$ (all lower bounds), $c^{(2)} = (8, 5, 7, 6, 9)$ (all upper bounds), and $c^{(3)} = (5, 1, 7, 2, 4)$ (mixed costs).
Graph $G$ admits eight spanning trees in total.
We highlight three representative trees that exhibit different structural properties: $T_1 = \{e_1, e_2, e_3\}$ (star centred at vertex~2), $T_2 = \{e_1, e_2, e_4\}$ (path $1$--$2$--$3$--$4$), and $T_3 = \{e_2, e_3, e_5\}$ (includes edge $e_5$).
These trees and scenarios provide a consistent testbed for demonstrating how optimal solutions vary across objectives and uncertainty models in subsequent chapters.

\begin{figure}[htbp]
\centering
\begin{tikzpicture}[
    scale=2.0,
    vertex/.style={circle, draw=RWTHBlue, very thick, fill=RWTHBlue!20, minimum size=9mm, font=\bfseries},
    edge/.style={very thick, color=black!70},
    every node/.style={font=\small}
]
    % Vertices positioned for clear layout
    \node[vertex] (v1) at (0, 1.8) {$1$};
    \node[vertex] (v2) at (1.5, 1.8) {$2$};
    \node[vertex] (v3) at (0, 0) {$3$};
    \node[vertex] (v4) at (1.5, 0) {$4$};
    
    % Edges with labels showing intervals
    \draw[edge] (v1) -- (v2) node[midway, above, font=\scriptsize] {$e_1$: $[2,8]$};
    \draw[edge] (v2) -- (v3) node[midway, right, font=\scriptsize] {$e_2$: $[1,5]$};
    \draw[edge] (v2) -- (v4) node[midway, right, font=\scriptsize] {$e_3$: $[3,7]$};
    \draw[edge] (v3) -- (v4) node[midway, below, font=\scriptsize] {$e_4$: $[2,6]$};
    \draw[edge] (v1) -- (v3) node[midway, left, font=\scriptsize] {$e_5$: $[4,9]$};
    
    % Combined horizontal legend (no overlap)
    \node[anchor=north, align=center, font=\scriptsize] at (0.75, -0.7) {
        \textcolor{RWTHBlue}{\textbf{Scenarios:}} $c^{(1)} = (2,1,3,2,4)$, $c^{(2)} = (8,5,7,6,9)$, $c^{(3)} = (5,1,7,2,4)$ \\[1ex]
        \textcolor{RWTHOrange}{\textbf{Representative trees:}} $T_1 = \{e_1, e_2, e_3\}$, $T_2 = \{e_1, e_2, e_4\}$, $T_3 = \{e_2, e_3, e_5\}$
    };
\end{tikzpicture}
\caption{The micro-graph $G$ with interval costs $[\ell_e, u_e]$ on each edge, used throughout this thesis.}
\label{fig:micro-graph}
\end{figure}

% END OF SECTION 2.1
%─────────────────────────────────────────────────────────
% SECTION 2.2: MST OPTIMALITY CRITERIA (PART 1 of 2)
% Fundamental Lemmas - SEAL-PROOF VERSION
% Target: ~1.6 pages
%─────────────────────────────────────────────────────────
\section{MST Optimality Criteria}\label{sec:mst-criteria}

A remarkable feature of minimum spanning trees is that optimality can be verified locally: rather than comparing a candidate tree against all exponentially many spanning trees, we can check simple conditions on individual edges.
This section develops these local characterisations through five results.
We begin with two fundamental exchange lemmas (\Cref{lem:fund-cycle,lem:fund-cut}) that establish necessary conditions for optimality, then prove three equivalent optimality criteria (\Cref{thm:cycle-criterion,thm:cut-criterion,thm:mst-equivalence}) that provide both necessary and sufficient conditions, thereby fully characterising MST optimality.
These characterisations underpin the correctness of classical MST algorithms such as Kruskal's and Prim's greedy methods.

Our first lemma relates non-tree edges to the cycles they create.

\begin{lemma}[Fundamental Cycle Lemma]\label{lem:fund-cycle}
Let $G = (V, E)$ be a connected graph with cost function $c \colon E \to \R$, and let $T$ be a minimum spanning tree of $G$ under $c$.
For any edge $f \in E \setminus E(T)$, the edge $f$ has maximum cost among all edges in its fundamental cycle $C_f$.
That is, $c_f \geq c_e$ for every edge $e \in C_f$.
\end{lemma}

\begin{proof}
Suppose for contradiction that $f$ does not have maximum cost in $C_f$.
Then there exists an edge $e \in C_f$ with $c_e > c_f$.
Since $f \notin E(T)$ and $C_f$ is created by adding $f$ to $T$, the edge $e$ must be a tree edge.

We construct an alternative spanning tree by exchanging $e$ for $f$.
Removing $e$ from $T$ disconnects $T$ into two components; since $e$ lies on the path connecting $f$'s endpoints in $T$, the edge $f$ bridges these components.
Define $T'$ with edge set $E(T') = E(T) \setminus \{e\} \cup \{f\}$.

To verify that $T'$ is a spanning tree, we check three properties.
First, $|E(T')| = |E(T)| - 1 + 1 = n - 1$, as required for a tree on $n$ vertices.
Second, $T'$ is connected: removing $e$ partitions $V$ into two components, but $f$ crosses this partition (as $f$ completes the cycle $C_f$ containing $e$), so adding $f$ reconnects the graph.
Third, $T'$ is acyclic: adding $f$ to $T$ created exactly one cycle (namely $C_f$), and removing $e \in C_f \setminus \{f\}$ eliminates it.

The cost of $T'$ is
\[
c(T') = c(T) - c_e + c_f < c(T),
\]
where the inequality holds because $c_e > c_f$.
This contradicts the optimality of $T$.
Therefore, $f$ must have maximum cost in $C_f$.
\end{proof}

The dual characterisation holds for tree edges via cuts.

\begin{lemma}[Fundamental Cut Lemma]\label{lem:fund-cut}
Let $G = (V, E)$ be a connected graph with cost function $c \colon E \to \R$, and let $T$ be a minimum spanning tree of $G$ under $c$.
For any edge $e \in E(T)$, the edge $e$ has minimum cost among all edges in its fundamental cut $\cut{X_e}$.
That is, $c_e \leq c_f$ for every edge $f \in \cut{X_e}$.
\end{lemma}

\begin{proof}
Suppose for contradiction that $e$ does not have minimum cost in $\cut{X_e}$.
Then there exists an edge $f \in \cut{X_e}$ with $c_f < c_e$.
By construction of the fundamental cut (\Cref{sec:graph-notation}), edge $e$ is the unique tree edge in $\cut{X_e}$, so $f$ must be a non-tree edge.

We construct an alternative spanning tree by exchanging $e$ for $f$.
Removing $e$ from $T$ splits $V$ into components $X_e$ and $V \setminus X_e$, which $f$ bridges (since $f \in \cut{X_e}$).
Define $T'$ with edge set $E(T') = E(T) \setminus \{e\} \cup \{f\}$.

To verify that $T'$ is a spanning tree, we check three properties.
First, $|E(T')| = |E(T)| - 1 + 1 = n - 1$, as required for a tree on $n$ vertices.
Second, $T'$ is connected: $f$ has one endpoint in each component (by definition of $\cut{X_e}$), so adding $f$ restores connectivity.
Third, $T'$ is acyclic: $T \setminus \{e\}$ is a forest of two trees, and adding a single edge between two trees produces a tree without cycles.

The cost of $T'$ is
\[
c(T') = c(T) - c_e + c_f < c(T),
\]
where the inequality holds because $c_f < c_e$.
This contradicts the optimality of $T$.
Therefore, $e$ must have minimum cost in $\cut{X_e}$.
\end{proof}

These fundamental exchange properties establish necessary conditions for a tree to be an MST.
The optimality criteria developed next strengthen these to necessary and sufficient characterisations.

\begin{remark}[Cost Ties]\label{rem:cost-ties}
The fundamental lemmas use non-strict inequalities: $c_f \geq c_e$ in \Cref{lem:fund-cycle} and $c_e \leq c_f$ in \Cref{lem:fund-cut}.
This correctly handles cost ties, where multiple edges share the same cost.
Strict inequalities would establish uniqueness of the MST, which requires all edge costs to be distinct.
When ties exist, multiple spanning trees may achieve the minimum cost, and the lemmas apply to each of them.
\end{remark}

% END OF PART 1

%─────────────────────────────────────────────────────────
% SECTION 2.2: MST OPTIMALITY CRITERIA (PART 2 of 2)
% Three Equivalent Optimality Theorems
% Target: ~2.7 pages
%─────────────────────────────────────────────────────────

\begin{theorem}[Cycle Criterion]\label{thm:cycle-criterion}
Let $G = (V, E)$ be a connected graph with cost function $c \colon E \to \R$, and let $T$ be a spanning tree of $G$.
Then $T$ is a minimum spanning tree if and only if for every non-tree edge $f \in E \setminus E(T)$, the edge $f$ has maximum cost among all edges in its fundamental cycle $C_f$.
\end{theorem}

\begin{proof}
We prove both directions.

\textbf{$(\Rightarrow)$:}
If $T$ is a minimum spanning tree, then every non-tree edge has maximum cost in its fundamental cycle by \Cref{lem:fund-cycle}.

\textbf{$(\Leftarrow)$:}
Suppose every non-tree edge has maximum cost in its fundamental cycle.
We show $T$ is a minimum spanning tree by proving $c(T) \leq c(T')$ for any spanning tree $T'$.

If $T = T'$, we are done.
Otherwise, there exists an edge $e \in E(T) \setminus E(T')$.
Write $e = \{s, t\}$ and let $X_e \subseteq V$ denote the component containing $s$ after removing $e$ from $T$, so that $t \in V \setminus X_e$.
Since $T'$ is connected, there exists a path $P$ in $T'$ from $s$ to $t$.
Because $s$ and $t$ lie on opposite sides of the partition $(X_e, V \setminus X_e)$, the path $P$ must cross it at least once.

Pick any edge $f$ on $P$ that crosses the partition.
Then $f$ has one endpoint in $X_e$ and the other in $V \setminus X_e$, hence $f \in \cut{X_e}$.
Moreover, $f \in E(T')$ (since $f$ lies on the path $P$) and $f \notin E(T)$ (since $e$ is the unique tree edge in $\cut{X_e}$ by construction of the fundamental cut; see \Cref{sec:graph-notation}).

We construct a new spanning tree $T'' = (V, E(T') \setminus \{f\} \cup \{e\})$ by removing $f$ and adding $e$.
To verify that $T''$ is a spanning tree, note that $f$ lies on the path $P$ from $s$ to $t$ in $T'$, so removing $f$ disconnects $s$ from $t$, while adding $e = \{s, t\}$ reconnects them.
Thus $T''$ is connected.
Furthermore, the fundamental cycle of $e$ in $T'$ is precisely $P \cup \{e\}$, and since $f \in P$, removing $f$ eliminates this unique cycle, ensuring $T''$ is acyclic.
With $|E(T'')| = n - 1$ edges, $T''$ is a spanning tree.

We now verify that $c(T'') \leq c(T')$.
Since $f \notin E(T)$, the edge $f$ creates a fundamental cycle $C_f$ in $T$.
The path in $T$ connecting the endpoints of $f$ must cross the partition $(X_e, V \setminus X_e)$, and because $e$ is the unique tree edge in $\cut{X_e}$, this path contains $e$.
Therefore $e \in C_f$.
By assumption, $f$ has maximum cost in $C_f$, so $c_f \geq c_e$.
Hence
\[
c(T'') = c(T') - c_f + c_e \leq c(T').
\]

Moreover, $|E(T) \cap E(T'')| = |E(T) \cap E(T')| + 1$, since we added $e \in E(T)$ and removed $f \notin E(T)$.

Repeating this exchange, we obtain a sequence of spanning trees $T' = T_0, T_1, T_2, \ldots$ where each $T_{i+1}$ is derived from $T_i$ by exchanging an edge of $E(T) \setminus E(T_i)$ with an edge of $E(T_i) \setminus E(T)$, satisfying $c(T_{i+1}) \leq c(T_i)$ and $|E(T) \cap E(T_{i+1})| = |E(T) \cap E(T_i)| + 1$.
Since spanning trees have exactly $n - 1$ edges, the process terminates after at most $n - 1$ iterations with some $T_k = T$.
The cost satisfies
\[
c(T) = c(T_k) \leq c(T_{k-1}) \leq \cdots \leq c(T_1) \leq c(T_0) = c(T').
\]
Therefore $c(T) \leq c(T')$ for every spanning tree $T'$, which means $T$ is a minimum spanning tree.
\end{proof}

The dual characterisation via cuts is analogous.

\begin{theorem}[Cut Criterion]\label{thm:cut-criterion}
Let $G = (V, E)$ be a connected graph with cost function $c \colon E \to \R$, and let $T$ be a spanning tree of $G$.
Then $T$ is a minimum spanning tree if and only if for every tree edge $e \in E(T)$, the edge $e$ has minimum cost among all edges in its fundamental cut $\cut{X_e}$.
\end{theorem}

\begin{proof}
We prove both directions.

\textbf{$(\Rightarrow)$:}
If $T$ is a minimum spanning tree, then every tree edge has minimum cost in its fundamental cut by \Cref{lem:fund-cut}.

\textbf{$(\Leftarrow)$:}
Suppose every tree edge has minimum cost in its fundamental cut.
We show $c(T) \leq c(T')$ for any spanning tree $T'$.

The argument mirrors the proof of \Cref{thm:cycle-criterion}.
If $T = T'$, we are done.
Otherwise, pick any edge $e \in E(T) \setminus E(T')$ with $e = \{s, t\}$, and let $X_e$ denote the component containing $s$ in $T \setminus \{e\}$.

Since $T'$ is connected, there exists a path $P$ in $T'$ from $s$ to $t$.
Because $s \in X_e$ and $t \in V \setminus X_e$ lie on opposite sides of the partition, the path $P$ crosses $(X_e, V \setminus X_e)$ at least once.
Let $f$ be any edge on $P$ crossing the partition.
Then $f \in \cut{X_e}$, $f \in E(T')$, and $f \notin E(T)$ (since $e$ is the unique tree edge in $\cut{X_e}$ and $f \neq e$).
By the cut criterion, $c_e \leq c_f$.

By the same spanning tree verification as in \Cref{thm:cycle-criterion}, the exchange $T'' = (V, E(T') \setminus \{f\} \cup \{e\})$ yields a spanning tree with
\[
c(T'') = c(T') - c_f + c_e \leq c(T')
\]
and $|E(T) \cap E(T'')| = |E(T) \cap E(T')| + 1$.

Repeating this exchange, we obtain a sequence $T' = T_0, T_1, \ldots, T_k = T$ where each step satisfies $c(T_{i+1}) \leq c(T_i)$ and increases $|E(T) \cap E(T_i)|$ by one.
The process terminates when $T_k = T$, yielding
\[
c(T) = c(T_k) \leq c(T_{k-1}) \leq \cdots \leq c(T_0) = c(T').
\]
Therefore $c(T) \leq c(T')$ for every spanning tree $T'$, so $T$ is a minimum spanning tree.
\end{proof}

The two criteria are equivalent.

\begin{theorem}[Equivalence of Criteria]\label{thm:mst-equivalence}
Let $G = (V, E)$ be a connected graph with cost function $c \colon E \to \R$, and let $T$ be a spanning tree of $G$.
Then $T$ satisfies the cycle criterion if and only if $T$ satisfies the cut criterion.
\end{theorem}

\begin{proof}
By \Cref{thm:cycle-criterion}, $T$ satisfies the cycle criterion if and only if $T$ is a minimum spanning tree.
By \Cref{thm:cut-criterion}, $T$ satisfies the cut criterion if and only if $T$ is a minimum spanning tree.
Therefore, $T$ satisfies the cycle criterion if and only if $T$ satisfies the cut criterion.
\end{proof}

Together, these three theorems fully characterise minimum spanning tree optimality through local edge-level conditions, enabling verification without comparing against all exponentially many spanning trees.
The characterisations extend naturally to the robust spanning tree problems in \Cref{ch:minmax,ch:regret}, where similar exchange arguments establish optimality under uncertainty.
The correctness of classical greedy algorithms such as Kruskal's and Prim's follows from these criteria, as discussed in \Cref{sec:kruskal-prim}.

% END OF SECTION 2.2

%─────────────────────────────────────────────────────────
% SECTION 2.3: KRUSKAL AND PRIM — ALGORITHMIC REMARKS
% 
% Goal: Provide remark-style algorithmic context (no pseudocode)
% Prerequisites: Section 2.2 (optimality criteria)
% Page budget: ~0.8 pages
% Sources: Skript_OptiB §2.3, Korte-Vygen §6.1
%─────────────────────────────────────────────────────────
\section{Kruskal and Prim: Algorithmic Remarks}\label{sec:kruskal-prim}

The optimality criteria developed in \Cref{sec:mst-criteria} directly yield the correctness of the two most classical MST algorithms: Kruskal's algorithm (1956) and Prim's algorithm (1957).
Both are \emph{greedy} methods that build a spanning tree incrementally, maintaining optimality conditions throughout \cite[§2.3]{BuesingOptiB2023}.
We describe each algorithm briefly, emphasising its connection to the criteria rather than implementation details.

\paragraph{Kruskal's Algorithm.}

Kruskal's algorithm constructs an MST by processing edges in non-decreasing order of cost.
Starting with an empty edge set, the algorithm considers each edge in turn and adds it to the current forest if and only if it does not create a cycle.
The process terminates when the forest becomes a spanning tree (after adding exactly $n - 1$ edges).

The correctness follows immediately from the cycle criterion (\Cref{thm:cycle-criterion}).
When the algorithm terminates with spanning tree $T$, consider any non-tree edge $f \in E \setminus E(T)$.
Since $f$ was rejected, adding $f$ would have created a cycle with edges already in $T$.
All edges in this cycle were added before $f$ was considered, hence each has cost at most $c_f$ (by the sorting order).
Therefore $f$ has maximum cost in its fundamental cycle, satisfying the cycle criterion.

With an efficient union-find data structure to track connected components, Kruskal's algorithm runs in $O(m \log n)$ time, dominated by the initial edge sorting \cite[Theorem~6.5]{KorteVygen2018}.

\paragraph{Prim's Algorithm.}

Prim's algorithm grows a single tree from an arbitrary starting vertex.
At each step, it adds the minimum-cost edge crossing the cut between the current tree vertices and the remaining vertices.
The process continues until all vertices are included.

The correctness follows from the cut criterion (\Cref{thm:cut-criterion}).
Each edge $e$ added to the tree is, by construction, the minimum-cost edge in $\cut{V(T)}$ at the moment of addition.
The set $V(T)$ at this moment becomes $X_e$ in the final tree, so $e$ has minimum cost in its fundamental cut $\cut{X_e}$, satisfying the cut criterion.

With a priority queue maintaining the minimum-cost edge to each non-tree vertex, Prim's algorithm achieves $O(m + n^2)$ time with a simple array implementation, or $O(m + n \log n)$ with Fibonacci heaps \cite[Theorem~6.7]{KorteVygen2018}.

\paragraph{Relevance to Robust Problems.}

Both algorithms solve the deterministic MST problem optimally in polynomial time.
However, under uncertainty, the situation changes fundamentally.
When edge costs are uncertain, neither algorithm directly applies: the greedy choices depend on which cost realisation occurs, yet the spanning tree must be chosen before costs are revealed.
The robust spanning tree problems studied in \Cref{ch:minmax,ch:regret} address precisely this challenge, and several variants become NP-hard despite the tractability of the deterministic case.

%─────────────────────────────────────────────────────────
% SECTION 2.4: UNCERTAINTY AND ROBUST OBJECTIVES (1.5 pages)
%─────────────────────────────────────────────────────────
\section{Uncertainty Models and Robust Optimisation}\label{sec:uncertainty}

% TODO: Discrete scenarios (0.4 pg)
%   - Definition: 𝒰 = {c^(1), ..., c^(K)}
%   - Min-Max objective: min_{T∈𝒯} max_{k∈[K]} c^(k)(T)
%   - Min-Max Regret: min_{T∈𝒯} max_{k∈[K]} [c^(k)(T) - MST(c^(k))]

% TODO: Interval uncertainty (0.4 pg)
%   - Definition: 𝒰 = ∏_{e∈E} [ℓₑ, uₑ]
%   - Min-Max objective: min_{T∈𝒯} max_{c∈𝒰} c(T)
%   - Min-Max Regret: min_{T∈𝒯} max_{c∈𝒰} [c(T) - MST(c)]

% TODO: Notation summary (0.3 pg)
%   - Scenario notation: c^(k) or \cs{k}
%   - Macro list: \Scenarios, \cT, \MSTcost{c}
%   - Pointer to Appendix A (notation table)

% TODO: Micro-graph scenarios table (0.4 pg)
%   - Scenario 1: all lower bounds c^(1) = (2,1,3,2,4)
%   - Scenario 2: all upper bounds c^(2) = (8,5,7,6,9)
%   - Scenario 3: mixed c^(3) = (5,1,7,2,4)
%   - Table: c^(k)(T₁), c^(k)(T₂), c^(k)(T₃) for k=1,2,3

%─────────────────────────────────────────────────────────
% SECTION 2.5: COMPLEXITY & APPROXIMATION GLOSSARY (1.5 pages)
%─────────────────────────────────────────────────────────
\section{Algorithmic Preliminaries}\label{sec:complexity}

% TODO: Complexity glossary (0.8 pg)
%   - Decision vs optimisation problems
%   - P, NP (verifiability), NP-hard, NP-complete
%   - Reductions (many-one, polynomial time)
%   - Weak vs strong NP-hardness
%   - Pseudo-polynomial algorithms
%   - Why K matters: K=2 vs K part of input

% TODO: Approximation glossary (0.5 pg)
%   - α-approximation (min: ALG ≤ α·OPT)
%   - PTAS, FPTAS (and why FPTAS needs pseudo-poly nominal algorithm)
%   - APX, APX-hard
%   - Inapproximability statements ("unless P=NP")

% TODO: Proof patterns (0.2 pg)
%   - Exchange arguments (used in §2.2)
%   - Extremal arguments for intervals (Ch3-4)
%   - Reductions for hardness (Ch3-4)

%─────────────────────────────────────────────────────────
% SECTION 2.6: CHAPTER SUMMARY (0.3 pages)
%─────────────────────────────────────────────────────────
\section*{Summary}

% TODO: Summary paragraph (0.3 pg)
% We have established the complete MST toolkit:
%   - Fundamental cycle/cut lemmas (Lemmas 2.1-2.2)
%   - Optimality criteria via exchange arguments (Theorems 2.3-2.5)
%   - Sections 2.4-2.5 define uncertainty models and complexity terminology
%   - Micro-graph (Fig. 2.1) will be reused in all worked examples (§3.4, §4.5, §5.2)
%   - All notation tabulated in Appendix A

% END OF CHAPTER 2`````````

