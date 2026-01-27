## chapters/06_conclusion.tex

```tex
%═══════════════════════════════════════════════════════════
% CHAPTER 6: CONCLUSION AND OUTLOOK
% 
% Prerequisites: Ch2-5 (complete thesis)
% Provides:
%   - Summary of achievements (proofs, results, synthesis)
%   - Limitations of scope (honest assessment)
%   - Outlook to related models (budgeted, polyhedral, two-stage, recoverable)
%   - Open problems from literature
% Labels created: ch:conclusion, sec:summary, sec:limitations, sec:outlook
% Page budget: 3.2 pages
% Status: PLACEHOLDER
%═══════════════════════════════════════════════════════════

\chapter{Conclusion and Outlook}\label{ch:conclusion}

% CHAPTER OVERVIEW:
% Final chapter summarising thesis contributions, acknowledging scope
% limitations, and pointing to active research directions in robust
% combinatorial optimisation. Maintains focus on spanning trees while
% briefly mentioning extensions (two-stage, recoverable, other uncertainty).

%─────────────────────────────────────────────────────────
% SECTION 6.1: SUMMARY (1.2 pages)
%─────────────────────────────────────────────────────────
\section{Summary}\label{sec:summary}

\subsection{Thesis Achievements}

% TODO: What we delivered (0.7 pg)

% ACHIEVEMENT 1: Self-contained MST foundations
%   - Chapter 2: 5 complete proofs from scratch (fundamental cycle/cut,
%     3 MST optimality criteria)
%   - Exchange arguments, matroid properties
%   - Algorithmic preliminaries (complexity, approximation glossary)

% ACHIEVEMENT 2: Unified robust framework
%   - Two objectives: Min-Max (absolute), Min-Max Regret (relative)
%   - Two uncertainty models: discrete scenarios, interval boxes
%   - Formal definitions, worked examples (micro-graph)

% ACHIEVEMENT 3: Representative proofs
%   - Theorem 3.2: Partition → Min-Max K=2 (weakly NP-hard)
%   - Theorem 4.5: 2-approximation for interval regret (with Lemmas 4.6-4.7)
%   - Total: 8 complete proofs across thesis

% ACHIEVEMENT 4: Complete complexity classification
%   - Table 5.1: 2 objectives × 2 models × 2 K cases = 8 entries
%   - Ranges: polynomial (Min-Max intervals) to strongly NP-hard (discrete K unbounded)
%   - Approximability: FPTAS (K=const) to inapproximable (discrete K unbounded)

% ACHIEVEMENT 5: Example gallery
%   - Running micro-graph used throughout (Sections 2.1, 3.1, 4.1, 5.2)
%   - TikZ visualisations (extremal behaviour, partition reduction)
%   - Demonstrates optimal solutions differ across objectives

\subsection{Core Findings}

% TODO: Main insights (0.5 pg)

% FINDING 1: Interval extremal principle
%   Worst cases always at boundaries (Lemmas 3.1, 4.1).
%   Applies universally to both objectives.
%   Geometric interpretation: vertices of uncertainty box.

% FINDING 2: Discrete scenarios: K threshold
%   K constant: weak NP-hard, but FPTAS exists (multicriteria method)
%   K unbounded: strong NP-hard, no constant-factor approximation
%   Threshold behaviour at K=2 (Theorems 3.2, 4.2)

% FINDING 3: Intervals: Min-Max vs Regret dichotomy
%   Min-Max: polynomial (simple extremal evaluation)
%   Regret: NP-hard (varying MST complicates), but 2-approx available
%   Fundamental difference in problem structure

% FINDING 4: 2-approximation remarkably simple
%   Midpoint heuristic: solve MST((ℓ+u)/2)
%   Best known for any robust combinatorial problem with intervals
%   Tightness remains open (Goerigk Open Problem 2)

%─────────────────────────────────────────────────────────
% SECTION 6.2: LIMITATIONS (0.8 pages)
%─────────────────────────────────────────────────────────
\section{Limitations}\label{sec:limitations}

\subsection{Scope Restrictions}

% TODO: Honest assessment (0.4 pg)

% RESTRICTION 1: Graph class
%   - Undirected, connected, simple graphs only
%   - Directed networks excluded (different MST structure)
%   - Multigraphs, weighted vertices excluded

% RESTRICTION 2: Uncertainty models
%   - Two models only: discrete scenarios, interval boxes
%   - Excluded: budgeted-Γ uncertainty, polyhedral sets,
%     ellipsoidal, distributional robustness
%   - Discrete + intervals cover foundational cases

% RESTRICTION 3: Objectives
%   - Two objectives only: Min-Max, Min-Max Regret
%   - Excluded: two-stage (first-stage + recourse),
%     recoverable (first-stage + limited recovery),
%     min-max-min (nested optimisation)
%   - Focus on static single-stage robustness

\subsection{Proofs and Coverage}

% TODO: Proof selection (0.2 pg)

% PROOF CHOICE: Representative over exhaustive
%   - Proved K=2 cases in full (Theorems 3.2, 4.2)
%   - Proved 2-approximation in full (Theorem 4.5 + Lemmas 4.6-4.7)
%   - Cited stronger K-unbounded results with sketches
%   - Prioritises pedagogical clarity over exhaustive coverage

\subsection{Micro-Graph and Empirical Work}

% TODO: Example limitations (0.2 pg)

% EXAMPLE SIZE: 4-vertex graph sufficient for demonstrations
%   - Real networks: hundreds/thousands of nodes
%   - Computational challenges not explored (implementation, benchmarking)
%   - No experimental comparison of algorithms
%   - Theory-focused thesis (intentional choice per scope)

%─────────────────────────────────────────────────────────
% SECTION 6.3: OUTLOOK AND EXTENSIONS (1.2 pages)
%─────────────────────────────────────────────────────────
\section{Outlook}\label{sec:outlook}

\subsection{Extensions in Uncertainty Modelling}

% TODO: Natural next steps (0.5 pg)

% EXTENSION 1: Budgeted uncertainty
%   - Γ-constrained deviations: at most Γ edges deviate from nominal
%   - Bertsimas-Sim (2004) framework
%   - Some problems polynomial (selection), others NP-hard (shortest path)
%   - Goerigk Chapter 4 for spanning trees
%   - Discrete vs continuous variants

% EXTENSION 2: Polyhedral uncertainty
%   - General polytopes beyond intervals/boxes
%   - Inner vs outer description: complexity differences
%   - Goerigk Section 4.6
%   - LP duality techniques for inner description

% EXTENSION 3: Distributional robustness
%   - Ambiguity sets over probability distributions
%   - Moment-based, Wasserstein-distance-based
%   - Beyond scope of this thesis (stochastic optimisation)

\subsection{Extensions in Solution Concepts}

% TODO: Other robust paradigms (0.4 pg)

% EXTENSION 1: Two-stage optimisation
%   - First-stage decision + recourse after scenario revelation
%   - NP-hard even for K=2 (Goerigk Theorem 8.17)
%   - Applications: capacity planning, network design under uncertainty
%   - Goerigk Section 8.3

% EXTENSION 2: Recoverable robustness
%   - First-stage + limited recovery budget Δ
%   - Can modify at most Δ edges after scenario revealed
%   - NP-hard for spanning trees, approximation status mostly unknown
%   - Goerigk Section 8.4, Open Problem 13

% EXTENSION 3: Min-max-min problems
%   - Nested optimisation: min-max-min structure
%   - Γ-robustness generalisation
%   - Goerigk Section 10.6

\subsection{Open Problems}

% TODO: Specific research questions (0.3 pg)

% OPEN PROBLEM 1 (from this thesis):
%   Is 2-approximation for interval regret tight?
%   (Goerigk Open Problem 2)
%   - No better algorithm known for any combinatorial problem
%   - No inapproximability bound either
%   - Example showing gap would be valuable

% OPEN PROBLEM 2 (from this thesis):
%   Close gap for discrete Min-Max: O(K) approx vs O(log^(1-ε) n) lower bound
%   (Goerigk Open Problems 10, 12)
%   - Midpoint heuristic achieves O(K)
%   - Inapproximability shows Ω(log^(1-ε) n)
%   - Significant gap remains

% OPEN PROBLEM 3 (related):
%   Better algorithms for discrete Min-Max Regret?
%   Currently: same O(K) midpoint heuristic
%   Problem-specific algorithm unknown

%─────────────────────────────────────────────────────────
% SECTION 6.4: CLOSING REMARKS (0.2 pages — VERY SHORT)
%─────────────────────────────────────────────────────────

% TODO: Brief closing (0.2 pg — 1-2 paragraphs max)

% This thesis provided a self-contained introduction to robust spanning
% tree optimisation, balancing mathematical rigour with pedagogical clarity.
% The synthesis in Chapter 5 shows that complexity depends critically on
% uncertainty structure: intervals yield polynomial or 2-approximable problems,
% while discrete scenarios with unbounded K are strongly intractable. The
% midpoint heuristic emerges as a practical universal algorithm despite
% theoretical limits. Future work may tighten approximation bounds, extend
% to two-stage or recoverable settings, or explore budgeted uncertainty models.

% END OF CHAPTER 6```

