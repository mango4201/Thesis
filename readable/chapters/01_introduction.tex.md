## chapters/01_introduction.tex

```tex
%═══════════════════════════════════════════════════════════
% CHAPTER 1: INTRODUCTION
% 
% Prerequisites: None (thesis opening)
% Provides:
%   - Concrete motivation (fiber network scenario)
%   - Research scope (2 objectives, 2 models)
%   - Contributions summary (5 key deliverables)
%   - Thesis roadmap (Ch2-6 overview)
% Page budget: 3.5 pages
% Status: PLACEHOLDER — READY FOR ATOMIC WRITING
%═══════════════════════════════════════════════════════════

\chapter{Introduction}\label{ch:intro}

%─────────────────────────────────────────────────────────
% SECTION 1.1: MOTIVATION (1.2 pages)
%─────────────────────────────────────────────────────────
\section{Motivation}\label{sec:intro-motivation}

% TODO §1.1.1: CONCRETE OPENING (1 paragraph, 0.3 pg)
% START with fiber-optic network example (concrete scenario):
%   - Building network connecting cities
%   - Cable costs depend on: terrain, regulations, material prices
%   - Cost fluctuations: 20-40% over planning horizon
%   - Risk: optimal design today → 50% more expensive with different costs
% 
% TRANSITION to general problem:
%   - Many infrastructure planning scenarios (not just telecom)
%   - Central question: how to design networks robust to cost uncertainty?

% TODO §1.1.2: CLASSICAL MST BASELINE (1 paragraph, 0.2 pg)
% When costs known precisely:
%   - Minimum spanning tree (MST) = classical solution
%   - Well-understood: Kruskal, Prim algorithms (polynomial time)
%   - Connects all nodes at minimum total cost
% 
% BUT in practice:
%   - Costs uncertain at design time
%   - MST optimal for one estimate ≠ robust to variations

% TODO §1.1.3: ROBUST OBJECTIVES (2 paragraphs, 0.4 pg)
% Two natural design goals under uncertainty:
% 
% PARAGRAPH 1: Min-Max
%   - Objective: minimize worst-case absolute cost
%   - Hedge against: expensive scenarios
%   - Mathematical: min_T max_{c∈U} c(T)
% 
% PARAGRAPH 2: Min-Max Regret
%   - Objective: minimize worst-case performance gap
%   - Hedge against: missing optimal solution
%   - Mathematical: min_T max_{c∈U} [c(T) - MST(c)]
%   - Difference: absolute cost vs relative performance

% TODO §1.1.4: UNCERTAINTY MODELS (1 paragraph, 0.3 pg)
% Two standard uncertainty representations:
%   (i)  Discrete scenarios: finite set of cost vectors
%   (ii) Interval uncertainty: per-edge bounds [ℓ_e, u_e]
% 
% Together: 2 objectives × 2 models = 4 problem variants
% Central question: How does complexity differ across this design space?


%─────────────────────────────────────────────────────────
% SECTION 1.2: RESEARCH QUESTIONS AND SCOPE (0.8 pages)
%─────────────────────────────────────────────────────────
\section{Research Questions and Scope}\label{sec:intro-scope}

% TODO §1.2.1: RESEARCH QUESTIONS (3 bullet points, 0.4 pg)
% This thesis addresses three core questions:
% 
% RQ1: What is the computational complexity of robust spanning trees
%      under discrete scenarios and interval uncertainty?
%      → Classification: polynomial, weak/strong NP-hard
% 
% RQ2: Where do worst-case costs occur for interval uncertainty?
%      → Extremal lemmas: boundaries vs interior points
% 
% RQ3: What approximation guarantees exist for hard cases?
%      → 2-approximation for interval regret, FPTAS for K=const

% TODO §1.2.2: SCOPE BOUNDARIES (1 paragraph, 0.4 pg)
% IN SCOPE:
%   - Problem: Spanning trees in undirected connected graphs
%   - Objectives: Min-Max and Min-Max Regret (static single-stage)
%   - Uncertainty: Discrete scenarios (K finite), intervals (per-edge bounds)
%   - Results: Complexity classification + approximation algorithms
% 
% OUT OF SCOPE (mentioned briefly in Ch6):
%   - Budgeted-Γ uncertainty (at most Γ edges deviate)
%   - Two-stage optimization (first-stage + recourse)
%   - Polyhedral uncertainty (general polytopes)
%   - Distributional robustness (probability distributions)


%─────────────────────────────────────────────────────────
% SECTION 1.3: CONTRIBUTIONS (0.8 pages)
%─────────────────────────────────────────────────────────
\section{Contributions}\label{sec:intro-contributions}

% TODO §1.3: DELIVERABLES LIST (5 items, 0.8 pg)
% This thesis delivers:
% 
% 1. SELF-CONTAINED MST FOUNDATIONS (Chapter 2)
%    - Five complete proofs from first principles
%    - Fundamental cycle and cut lemmas (exchange arguments)
%    - Three MST optimality criteria (cycle, cut, equivalence)
%    - Complexity primer (P, NP, approximation glossary)
% 
% 2. EXTREMAL CHARACTERIZATION FOR INTERVALS (Chapters 3-4)
%    - Lemma 3.1: Min-Max worst cases at upper bounds → polynomial
%    - Lemma 4.1: Regret worst cases at boundaries → enables 2-approximation
%    - Both lemmas proved in full
% 
% 3. REPRESENTATIVE HARDNESS PROOFS (Chapters 3-4)
%    - Theorem 3.2: Partition reduction for Min-Max K=2 (full proof)
%    - Theorem 4.2: Reuses construction for Regret K=2
%    - Total: 9 complete proofs across thesis
% 
% 4. APPROXIMATION ALGORITHM WITH FULL PROOF (Chapter 4)
%    - Theorem 4.5: 2-approximation for interval regret
%    - Includes supporting lemmas 4.6-4.7 (lower/upper bounds)
%    - Best known result for any robust combinatorial problem
% 
% 5. COMPREHENSIVE CLASSIFICATION (Chapter 5)
%    - Table 5.1: 8 problem variants (2 obj × 2 unc × 2 K regimes)
%    - Example gallery: micro-graph across all objectives
%    - Key patterns: extremal principles, K threshold, interval dichotomy


%─────────────────────────────────────────────────────────
% SECTION 1.4: THESIS STRUCTURE (0.7 pages)
%─────────────────────────────────────────────────────────
\section{Thesis Structure}\label{sec:intro-structure}

% TODO §1.4: ROADMAP (6 chapters, 1-2 sentences each, 0.7 pg)
% 
% CHAPTER 2: FOUNDATIONS
% Establishes MST theory with five complete proofs (fundamental cycle/cut,
% three optimality criteria). Defines uncertainty models (discrete, interval)
% and robust objectives (Min-Max, Regret). Includes complexity primer.
% 
% CHAPTER 3: MIN-MAX SPANNING TREE
% Analyses Min-Max objective. For intervals: proves extremal lemma yielding
% polynomial algorithm. For discrete: proves weak NP-hardness (K=2, partition
% reduction), surveys pseudo-polynomial algorithms (K=const) and strong
% NP-hardness with inapproximability (K unbounded).
% 
% CHAPTER 4: MIN-MAX REGRET SPANNING TREE
% Analyses Regret objective. For intervals: proves extremal lemma, establishes
% NP-hardness, delivers 2-approximation with complete proof. For discrete:
% mirrors Chapter 3 complexity (reuses partition construction for K=2).
% 
% CHAPTER 5: SYNTHESIS AND EXAMPLE GALLERY
% Consolidates results in classification table (8 problem variants). Presents
% micro-graph example gallery illustrating optimal solutions differ across
% objectives. Extracts key patterns: extremal principles, K threshold,
% interval dichotomy.
% 
% CHAPTER 6: CONCLUSION AND OUTLOOK
% Summarizes achievements (9 proofs, classification table, pedagogical
% micro-graph). Acknowledges scope limitations. Points to extensions:
% budgeted uncertainty, two-stage optimization, recoverable robustness.
% Lists three open problems from literature.
% 
% APPENDIX A: NOTATION TABLE
% Comprehensive symbol reference (40 entries) for quick lookup.

% END OF CHAPTER 1```

