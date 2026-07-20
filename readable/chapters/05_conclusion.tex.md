## chapters/05_conclusion.tex

```tex
%═══════════════════════════════════════════════════════════
% CHAPTER 5: CONCLUSION
%
% Merged from old Ch5 (Synthesis & Gallery) and old Ch6 (Conclusion).
% Flat structure: \section + \paragraph, no \subsection.
%
% Prerequisites: Ch1-4 (complete thesis)
% Provides:
%   - Synthesis of all complexity/approximation results (table + patterns)
%   - Example gallery: micro-graph under all studied objectives
%   - Honest scope limitations
%   - Outlook: extensions in uncertainty modelling and solution concepts
% Labels created (NEW):
%   - ch:conclusion, sec:synthesis, sec:gallery, sec:limitations, sec:outlook
%   - tab:complexity-landscape, fig:micro-graph-gallery, fig:extremal-visualisation
% Labels retired:
%   - ch:synthesis (replaced by ch:conclusion)
%   - sec:results-table, sec:example-gallery, sec:takeaways (replaced)
%   - sec:summary (old Ch6 §6.1 subsumed into sec:synthesis)
% Page budget: 7.5 pages
% Status: PLACEHOLDER
%═══════════════════════════════════════════════════════════

\chapter{Conclusion}\label{ch:conclusion}

% CHAPTER OVERVIEW:
% Consolidates all complexity and approximation results from Chapters 3-4
% into a unified synthesis, displays the micro-graph under all studied
% objectives, acknowledges scope limitations, and points to active
% research directions in robust combinatorial optimisation.

%─────────────────────────────────────────────────────────
% SECTION 5.1: SYNTHESIS OF RESULTS (2.5 pages)
%─────────────────────────────────────────────────────────
\section{Synthesis of Results}\label{sec:synthesis}

\paragraph{Complexity Landscape.}

% TODO: Comprehensive table (1.5 pg) — Table 5.1
%
% Table 5.1: Complexity and approximation landscape for robust spanning trees
%
% | Objective | Uncertainty | K=constant | K unbounded | Key Observations |
% |-----------|------------|------------|-------------|------------------|
% | Min-Max | Discrete | Weakly NP-hard (K=2, thm:mm-k2-hard) | Strongly NP-hard (thm:mm-kunbdd-hard) | Extremal: max at scenarios |
% |         |          | Pseudo-poly, FPTAS (thm:mm-kconst-pseudo, thm:mm-kconst-fptas) | O(log K)-approx (thm:mm-kunbdd-approx, Baak2025) | Complexity grows with K |
% |         |          |                              | Not (2-eps)-approx; not within O(log^{1-eps} n) (thm:mm-kunbdd-hard, KasperskiZielinski2011) | |
% | Min-Max | Interval | POLYNOMIAL (lem:interval-extremal-cost, cor:mm-interval-polynomial) | POLYNOMIAL | Extremal: chosen edges -> upper |
% |         |          | MST on upper bounds          |                            | Reduces to nominal MST |
% | Min-Max | Budgeted | POLYNOMIAL (thm:mm-budgeted-poly) | POLYNOMIAL            | Bertsimas-Sim enumeration: O(m) nominal MSTs |
% |         |          | O(m^2 log n) total           |                            | Interpolates between deterministic and interval |
% | Regret  | Discrete | Weakly NP-hard (K=2, thm:regret-k2-hard) | Strongly NP-hard (thm:regret-kunbdd-hard) | Reuses Min-Max constructions |
% |         |          | Pseudo-poly (thm:regret-kconst-pseudo; FPTAS thm at Ch4 drafting) | O(K)-approx (midpoint; Goerigk Open Problems 10, 12) | MST(c^k) complicates but |
% |         |          |                              | not within O(log^{1-eps} n) (KasperskiZielinski2011) | doesn't change complexity |
% | Regret  | Interval | NP-hard (thm:regret-interval-hard, AverbakhLebedev2004) | NP-hard | Extremal: strategic boundaries |
% |         |          | 2-approximation (thm:regret-2approx) | 2-approximation       | MST(c) varies -> harder than Min-Max |
% |         |          | via midpoint                 | via midpoint               | Best known guarantee for interval regret ST; tightness of 2 open |
% | Regret  | Budgeted | NP-hard (cited, §3.4)        | NP-hard                    | Inherits interval-case hardness |
% |         |          | (out of scope)               |                            | (Goerigk §4.5.3) |
%
% Plan above uses LABELS, not hard-coded numbers (numbers shift under the
% [chapter] theorem numbering). The live skeleton below keeps Ch1's
% \Cref{tab:complexity-landscape} resolvable until the final pass.
\begin{table}[htbp]
\centering
\caption{Complexity and approximability of robust spanning tree problems. Skeleton; the entries are compiled from \Cref{ch:minmax,ch:regret} in the final pass of this chapter.}
\label{tab:complexity-landscape}
\begin{tabular}{lcc}
\toprule
Uncertainty model & Min-max & Min-max regret \\
\midrule
Discrete, constant $K$ & & \\
Discrete, unbounded $K$ & & \\
Interval & & \\
Budgeted & & \\
\bottomrule
\end{tabular}
\end{table}

\paragraph{Key Patterns.}

% TODO: Pattern synthesis (1.0 pg) — folded from old Ch5 "Key Patterns" and Ch6 "Core Findings"

% PATTERN 1: Extremal behaviour
%   - All interval objectives have worst cases at boundaries (Lemmas 3.1, 4.1)
%   - Min-Max: simple rule (chosen → upper)
%   - Regret: strategic mix (more complex but still extremal)

% PATTERN 2: K matters for discrete
%   - Constant K → pseudo-poly + FPTAS (multicriteria method)
%   - Unbounded K → strongly NP-hard, no constant-factor approx
%   - Threshold at K=2: already weakly NP-hard

% PATTERN 3: Interval dichotomy: Min-Max vs Regret
%   - Min-Max: polynomial via extremal evaluation
%   - Regret: NP-hard, but clean 2-approximation
%   - Fundamental difference: fixed MST vs varying MST(c)

% PATTERN 4: Budgeted as interpolation
%   - Sits between deterministic (Γ=0) and interval (Γ=|E|)
%   - Min-Max remains polynomial across the whole range
%   - Reduces to O(m) nominal MST instances

% PATTERN 5: Midpoint heuristic universality
%   - Works for both objectives (Min-Max and Regret)
%   - Discrete: midpoint (mean scenario) is a K-approximation (regret:
%     Goerigk Open Problems 10, 12; min-max: elementary, max <= K * mean).
%     For min-max the stronger O(log K) (thm:mm-kunbdd-approx, Baak2025)
%     uses a different technique (p-norm/OWA), not the midpoint.
%   - Interval regret: midpoint gives the factor-2 guarantee
%     (thm:regret-2approx).

%─────────────────────────────────────────────────────────
% SECTION 5.2: EXAMPLE GALLERY (2.5 pages)
%─────────────────────────────────────────────────────────
\section{Example Gallery}\label{sec:gallery}

\paragraph{Micro-Graph Solutions Across Objectives.}

% TODO: Figure 5.1 — TikZ gallery with 4-5 subfigures (1.5 pg)
%
% Figure 5.1: Micro-graph solutions under different objectives
%
% (a) Nominal MST(c_mid)
% (b) Min-Max discrete (K=3 scenarios)
% (c) Min-Max interval
% (d) Min-Max budgeted (Γ=2)   ← NEW PANEL
% (e) Regret interval
%
% Below figure: summary table comparing T_1, T_2, T_3 across all objectives.
%
% \label{fig:micro-graph-gallery}

\paragraph{Extremal Behaviour Visualisation.}

% TODO: Figure 5.2 — geometric interpretation of extremal lemmas (0.7 pg)
%
% Illustrate interval box [ℓ,u]^|E| with vertices (extremal points) highlighted.
% Min-Max vertex: all chosen edges at upper bounds.
% Regret vertex: strategic mix.
%
% \label{fig:extremal-visualisation}

%─────────────────────────────────────────────────────────
% SECTION 5.3: LIMITATIONS (1.0 page)
%─────────────────────────────────────────────────────────
\section{Limitations}\label{sec:limitations}

\paragraph{Scope of Uncertainty Models.}

% TODO: 0.4 pg
%   - Three models: discrete, interval, budgeted (§3.4).
%   - Excluded: polyhedral, ellipsoidal, distributional, ambiguity sets.
%   - Choice reflects pedagogical focus on foundational cases.

\paragraph{Scope of Solution Concepts.}

% TODO: 0.3 pg
%   - Two static objectives: Min-Max, Min-Max Regret.
%   - Excluded: two-stage (recourse), recoverable (limited recovery), min-max-min.
%   - Single-stage robustness only.

\paragraph{Proof Selection.}

% TODO: 0.3 pg
%   - Representative proofs (K=2 hardness; 2-approximation) over exhaustive coverage.
%   - Stronger K-unbounded results cited with sketches.
%   - Empirical/computational evaluation outside thesis scope.

%─────────────────────────────────────────────────────────
% SECTION 5.4: OUTLOOK (1.5 pages)
%─────────────────────────────────────────────────────────
\section{Outlook}\label{sec:outlook}

\paragraph{Extensions in Uncertainty Modelling.}

% TODO: 0.5 pg
%
% Beyond the three models studied here, several richer uncertainty
% structures appear in the robust optimisation literature:
%
%   - Polyhedral uncertainty: general polytopes, inner/outer descriptions.
%     Complexity depends on description type (Goerigk §4.6).
%   - Ellipsoidal uncertainty: continuous models from robust LP, less
%     common in combinatorial settings.
%   - Distributional robustness: ambiguity sets over distributions,
%     bridging robust and stochastic optimisation.

\paragraph{Extensions in Solution Concepts.}

% TODO: 0.5 pg
%
%   - Two-stage optimisation: first-stage decision + recourse after
%     scenario revelation (NP-hard even for K=2; Goerigk §8.3).
%   - Recoverable robustness: first-stage + limited modification budget Δ
%     after scenario observed (Goerigk §8.4).
%   - Min-max-min and related nested objectives (Goerigk §10.6).

\paragraph{Open Problems.}

% TODO: 0.5 pg
%
%   - Tightness of 2-approximation for interval regret: no better algorithm
%     known, no inapproximability bound (Goerigk Open Problem 2).
%   - Discrete Min-Max: gap between the O(log K) upper bound
%     (thm:mm-kunbdd-approx, Baak2025) and the lower bound: not approximable
%     within O(log^{1-eps} n) unless NP has quasi-polynomial time algorithms
%     (KasperskiZielinski2011; abstract verified, covers both objectives).
%   - Discrete Regret: gap between the O(K) midpoint upper bound (Goerigk
%     Open Problems 10, 12) and the same O(log^{1-eps} n) lower bound
%     (KasperskiZielinski2011).
%   - Algorithm-specific improvements for discrete Min-Max Regret beyond
%     midpoint heuristic.

% END OF CHAPTER 5```

