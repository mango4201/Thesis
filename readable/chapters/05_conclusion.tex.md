## chapters/05_conclusion.tex

```tex
%═══════════════════════════════════════════════════════════
% CHAPTER 5: CONCLUSION
% Status: SKELETON.
%═══════════════════════════════════════════════════════════
%
% ── BUDGET: A CONTENT COUNT, NOT A PAGE COUNT ─────────────
%
% See the header of 04_regret.tex for the measured cost model. The
% same discipline applies here, but Ch5 has NO proved results, so its
% cost is driven almost entirely by floats and by how many distinct
% topics the prose touches.
%
%     CHAPTER 5 CONTAINS ZERO PROVED RESULTS, TWO FLOATS, AND
%     THREE SECTIONS. Target 5 printed pages.
%
%   opening                                   0.25
%   S5.1  tab:complexity-landscape  ~300w  +  1.0  float
%   S5.1  pattern synthesis         ~450w     0.8
%   S5.2  gallery                   ~350w  +  1.5  float
%   S5.3  scope and outlook         ~550w     1.0
%   chapter boundary waste                    0.5
%                                          = ~5.0 pages
%
% PRIORITY, stated by the author: an extra page of EXAMPLE here is
% cheaper than an extra page of PROOF in Ch4. The table and the
% gallery are NOT to be shortened. If Ch5 runs to 5.5 pages because
% the gallery earns it, that is the right trade.
%
% ── TWO STRUCTURAL DECISIONS, WITH THEIR EVIDENCE ─────────
%
% (1) THE OLD S5.3 "LIMITATIONS" IS DELETED. Its content already
%     exists, written and live, in Ch1:
%       "This thesis does not cover polyhedral uncertainty sets,
%        ellipsoidal uncertainty, or distributional robustness ..."
%       "It also excludes two-stage and recoverable optimisation
%        models, where decisions can be partially adjusted ..."
%     The old plan then repeated that same list in S5.3 AND AGAIN in
%     S5.4, giving the same exclusion list three times in one thesis.
%     Ch1 keeps the job. The label sec:limitations is dropped; it is
%     referenced nowhere. The label sec:outlook is KEPT because Ch1
%     has a live \Cref to it ("briefly discussed in the outlook").
%
% (2) THE PLANNED FIGURE 5.2 IS CUT. It was to be a "geometric
%     interpretation of the extremal lemmas", which needs the words
%     vertex, extreme point, polytope or convex. Grep across Ch1-Ch4:
%     ZERO occurrences of any of them. The figure would have to build
%     that vocabulary from nothing, which makes it groundwork rather
%     than an example, and the standing rule is to skip a topic
%     entirely rather than introduce it and then try to shorten it.
%
% ── S5.1 SYNTHESIS OF RESULTS ─────────────────────────────
%
% tab:complexity-landscape is the thesis's result inventory and Ch1
% has a live \Cref to it. Six cells: two objectives x three models.
% Every cell cites by \Cref to a label in Ch3 or Ch4, or by \cite to
% the literature. Qualifiers that a table cell will silently drop and
% that MUST survive:
%     "weakly" NP-hard at K=2 vs "strongly" NP-hard once K is input
%     "not approximable within 2-eps UNLESS P = NP"
%     O(log K) is a MIN-MAX guarantee only; the regret side has K
%     the midpoint heuristic recurs in both objectives with DIFFERENT
%       guarantees: factor 2 for interval regret, factor K for discrete
%
% THE SIXTH CELL, regret x budgeted, is out of scope: Ch4 covers only
% discrete and interval. Keep the cell and carry it by citation plus a
% footnote, which is cleaner than a body section:
%     reformulation: Goerigk S4.5.3, Theorem 4.25 ([GH23])
%     hardness: interval regret is strongly NP-hard (Goerigk 8.16,
%     [AV04; AL04]) and the interval set is the budgeted set at
%     Gamma = |E|, so with Gamma part of the input the budgeted regret
%     problem inherits it.
% Ch1 poses the research question over all six combinations, so state
% once, plainly, that the sixth is carried by citation rather than
% developed. Do not let a reader discover it by counting cells.
%
% PATTERN SYNTHESIS, ~450 words. The five-pattern block was
% reallocated; only these belong here:
%   1. Extremal behaviour at the interval boundary: visible only with
%      both objectives side by side. Min-max has ONE worst-case vector
%      serving every tree; regret's worst-case vector depends on the
%      tree. Ch4's Summary supplies the mechanism, Ch5 records the
%      contrast. Do not re-derive.
%   3. The interval dichotomy: min-max easy, regret hard.
%   5. The midpoint heuristic recurring across both objectives.
%   2. K as the complexity parameter: ONE SENTENCE only. Ch3's Summary
%      already explains it for min-max.
%   4. Budgeted as interpolation: NOT HERE. Ch3 S3.4 already makes it
%      concretely and Ch3's Summary generalises it. A table cell is
%      the most Ch5 should spend.
%
% ── S5.2 EXAMPLE GALLERY ──────────────────────────────────
%
% fig:micro-graph-gallery. Ch1 promises it: "displays the running
% micro-graph under each robust objective". PROTECTED: do not shorten.
% Does not exist yet; the whole figure is to be built.
%
% LAYOUT, and this is the cookie: ARRANGE THE PANELS IN THE SAME 2x3
% SHAPE AS tab:complexity-landscape, objectives down, models across.
% The table and the figure then have identical geometry, and a reader
% can move between the inventory and the pictures without re-orienting.
% The empty sixth cell marks the out-of-scope case in exactly the place
% the table footnotes it, so the scope decision is visible rather than
% discovered by counting.
%
%                    discrete      interval      budgeted
%      min-max        (a) T2        (b) T2      (c) T2, Gamma=2
%      regret         (d) T1        (e) T1      (f) out of scope
%
% Plus one small reference panel, placed above or beside the grid:
%      (0) deterministic MST under the midpoints c^(3)  ->  T1, cost 11
%
% ALL VALUES VERIFIED BY EXHAUSTIVE ENUMERATION over the eight trees.
% Do not re-derive them from prose:
%   (0) deterministic, midpoints            T1   cost 11
%   (a) min-max, discrete c^(1),c^(2),c^(3) T2   worst case 15
%   (b) min-max, interval                   T2   worst case 15
%   (c) min-max, budgeted, Gamma = 2        T2   worst case 14
%   (d) min-max regret, discrete            T1   max regret 1
%   (e) min-max regret, interval            T1   max regret 3
%
% THE POINT OF THE PANEL SET, and why it earns a page and a half: the
% same five-edge graph yields DIFFERENT optimal trees depending only on
% which robustness concept is applied. Say it once, in the prose after
% the figure: across all six variants only two of the eight spanning
% trees are ever selected. Min-max chooses T2 under every uncertainty
% model; regret chooses T1 under both it is defined for; and the
% deterministic baseline agrees with regret, not with min-max. That
% last observation is the one a reader will remember.
%
% TikZ: reuse the styles of fig:micro-graph in Ch2, where RWTHOrange
% already marks tree edges. Keep that convention rather than inventing
% a second one.
% PRINT WARNING: RWTHBlue and RWTHRed have almost identical greyscale
% luminance (67.4 vs 68.5 out of 255). Never let colour alone carry a
% distinction; label every panel in text as well.
%
% ── S5.3 SCOPE AND OUTLOOK (label sec:outlook) ────────────
%
% ~550 words, two paragraphs. Ch1 already lists what is excluded, so
% this section must ADD something rather than repeat the list.
% The author's test for what belongs: topics that are integral or
% interesting for a full understanding AND at least vaguely
% approachable to this thesis's reader, without new machinery.
%
% KEEP, because each is one sentence and each has a spanning-tree
% specific result to point at:
%   - two-stage optimisation: decide now, adjust after the scenario is
%     revealed. Already known to be NP-hard for the spanning tree
%     problem even at K = 2 (Goerigk Thm 8.17, [GKZ20]).
%   - recoverable robustness: a limited modification budget after the
%     scenario is observed. NP-hard for constant K (Goerigk Thm 8.19,
%     [KKZ13]).
%   - polyhedral uncertainty: the natural next step, and approachable
%     precisely BECAUSE S3.4 already optimises over a polytope.
%   - the two open problems, which need no new vocabulary at all:
%     whether the factor 2 for interval regret is tight (Goerigk Open
%     Problem 2), and the gap between the O(log K) upper bound
%     (thm:mm-kunbdd-approx) and the inapproximability lower bound.
%
% DROP, because each needs machinery the thesis never builds:
%   ellipsoidal uncertainty, distributional robustness and ambiguity
%   sets (probability and convex geometry), min-max-min (niche).
%   Ch1 already names the first two as excluded; that is enough.
%
% ── HOUSE CONVENTIONS ─────────────────────────────────────
% As in Ch2, Ch3 and Ch4. Reference by label, never by number.
% British English, no em dashes, no -ize, no contractions, no
% hand-wave markers. Qualifiers survive compression.
%═══════════════════════════════════════════════════════════

\chapter{Conclusion}\label{ch:conclusion}

%─────────────────────────────────────────────────────────
%─────────────────────────────────────────────────────────
\section{Synthesis of Results}\label{sec:synthesis}
% tab:complexity-landscape (~300w + float) then pattern synthesis
% (~450w, patterns 1, 3, 5 and one sentence of 2). See header.

\paragraph{Complexity Landscape.}

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

%─────────────────────────────────────────────────────────
%─────────────────────────────────────────────────────────
\section{Example Gallery}\label{sec:gallery}
% fig:micro-graph-gallery (~350w + float). PROTECTED. Six panels in the
% SAME 2x3 shape as tab:complexity-landscape, plus one reference panel.
% Values verified by enumeration; see header. Close with the observation
% that only two of the eight trees are ever selected.

\paragraph{Micro-Graph Solutions Across Objectives.}

\paragraph{Extremal Behaviour Visualisation.}

%─────────────────────────────────────────────────────────
%─────────────────────────────────────────────────────────

\paragraph{Scope of Uncertainty Models.}

\paragraph{Scope of Solution Concepts.}

\paragraph{Proof Selection.}

%─────────────────────────────────────────────────────────
%─────────────────────────────────────────────────────────
\section{Scope and Outlook}\label{sec:outlook}
% ~550w, two paragraphs. Must ADD to Ch1's exclusion list, not repeat
% it. Keep two-stage, recoverable, polyhedral, two open problems.
% Drop ellipsoidal, distributional, min-max-min. See header.

\paragraph{Extensions in Uncertainty Modelling.}

\paragraph{Extensions in Solution Concepts.}

\paragraph{Open Problems.}```

