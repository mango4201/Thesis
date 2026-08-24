## chapters/04_regret.tex

```tex
%═══════════════════════════════════════════════════════════
% CHAPTER 4: MIN-MAX REGRET SPANNING TREE
%
% Prerequisites: Ch2 (MST, regret definition), Ch3 (min-max, for contrast)
% Status: SKELETON. Theorem shells carry titles and labels; bodies empty.
%═══════════════════════════════════════════════════════════
%
% ── WHY THIS PLAN LOOKS DIFFERENT FROM THE OLD ONE ────────
%
% The old plan said "11.3 pages" and allocated 1.8/2.2/2.5/3.3 to the
% four sections. That method has now failed three times:
%
%     Ch2   planned <=10  ->  actual 14   (1.4x)
%     Ch3   planned  12   ->  actual 18   (1.5x)
%     S3.4  planned 4.25  ->  actual  8   (1.9x)
%
% The overrun GROWS as the estimate gets finer, which is the tell: the
% thing that swells is not inside any single unit. Decomposing Ch3's
% 18 printed pages by word count shows where the pages actually went:
%
%     6 proof environments   2119 words   3.8 pages
%    10 result statements     532 words   0.9 pages
%     4 floats + captions     829 words   ~2  pages
%     RUNNING PROSE          5613 words  10.0 pages   <-- 62 percent
%
% Proofs are NOT what made Ch3 long. The connective prose is: the
% motivation before each result, the interpretation after it, the
% reconciliation with sealed text, and the discussion around each
% example. Section 3.4 is the clean case. The plan sized Lemma 3.2 at
% "a four-line proof"; the delivered proof is 450 words, because the
% house rules forbid "the greedy assignment is optimal" as a sentence.
% Lemma 3.3 was not in the plan at all: it appeared because
% instantiating a cited theorem as a black box was unsatisfying. Then
% the integer-vs-real Gamma remark, the n-1 vs |E| distinction and
% three hedges each cost sentences that no plan could have foreseen,
% because they only surfaced on contact with the sealed text.
%
% CONCLUSION: the cost driver is the NUMBER OF DISTINCT RESULTS the
% chapter touches, not their difficulty. Every result, however cheap
% its proof, drags a motivating paragraph, an interpreting paragraph
% and a consistency obligation behind it. Introducing a topic is
% expensive in a way that shortening it afterwards cannot undo.
%
% ── THE BUDGET IS A RESULT COUNT, NOT A PAGE COUNT ────────
%
% Realised per-item costs, calibrated on Ch3 (546 words/printed page):
%     a PROVED result, all-in          ~1.6 pages
%     a STATED result, grouped         ~0.4 pages
%     a float with its discussion      ~0.9 pages
%     chapter boundary waste           ~0.5 pages
% Check: S3.4 = 3 proved + 2 floats + summary + opening
%            = 4.8 + 1.8 + 1.4 = 8.0.  Actual: 8.  Model holds.
%
% [Writer A, 2026-08-21] THE 546 IS NOT IN CONFLICT WITH THE Ch3
% HANDOFF'S 412. They are the same measurement under two accountings.
% Measured from text-block coordinates on a clean build of Ch3:
%     all-in rate, floats folded into the pages       418 w/pg
%     non-float prose only, floats charged at 0.9 pg  579 w/pg
% The 546 above is the SECOND accounting, and the model only works if
% applied that way: words / 546, PLUS 0.9 per float, PLUS 0.7 boundary
% waste. Applied to R1..R5 this projects 8.6 to 9.0 printed pages, so
% the word budgets below stand and need no cutting. Do not relitigate.
% ONE EXCEPTION, R5: Ch3's Summary ran at 236 w/pg, not 546, because
% short paragraphs cost more than words. R5 at 280 words is therefore
% about 1.19 pages, not the 0.75 the cap assumes. Open, deferred.
%
%
% Ch4 as originally scaffolded was 5 proved + 3 stated + 2 floats,
% which projects to 13.7 pages. THE BINDING CONSTRAINT IS THEREFORE:
%
%     CHAPTER 4 CONTAINS EXACTLY TWO PROVED RESULTS.
%     Everything else is stated, cited, and grouped.
%     Two worked examples. One Summary. Target 8 pages, 9 acceptable.
%
% This is checkable BEFORE a word is written. A page number can only
% be checked afterwards, when the cost of cutting is highest.
%
% ── WHICH TWO PROOFS, AND WHY THOSE ───────────────────────
%
% Not a preference. Both are demanded by live \Cref from sealed text:
%
%  1. lem:interval-extremal-regret. Ch1: "For the regret objective,
%     \Cref{lem:interval-extremal-regret} PROVES that worst-case
%     regret occurs at boundary vertices of the interval box."
%     The abstract also lists "the interval extremal characterisations"
%     (plural, so both Lemma 3.1 and this one) among results proved in
%     full. Everything in S4.4 refers back to it.
%
%  2. thm:regret-k2-hard. The abstract lists "two representative
%     hardness reductions (min-max and min-max regret at K = 2)" among
%     results proved in full. Ch1: "extends this to min-max regret by
%     REUSING THE CONSTRUCTION with adjusted analysis" -- the sealed
%     text itself sanctions reuse rather than restatement, and so does
%     the source (Goerigk Thm 8.7 proves it with the single sentence
%     "We use the same construction as in the proof of Theorem 8.4").
%
% Everything else is stated with a citation. This is consistent with
% the thesis proof stance ("prove every lemma/theorem actually used in
% later derivations"): none of the remaining results feeds a later
% derivation; they feed tab:complexity-landscape, an inventory.
%
% ── WHAT WAS CUT, AND THE COUPLED EDITS IT FORCED ─────────
%
% thm:regret-2approx is now STATED AND CITED, not proved. Its two
% supporting lemmas are deleted. This alone saves ~4 pages, and it is
% the single decision that makes 8 pages reachable.
% It is kept as a statement rather than dropped entirely because Ch4
% would otherwise contain no positive algorithmic result at all while
% Ch3 has two, and because Ch5's midpoint pattern would dangle.
%
% Three coupled edits were required and HAVE BEEN APPLIED (rule C5,
% propagate fully or not at all). Do not revert one without the others:
%   - Ch1: "we prove a 2-approximation" -> "we present the ...".
%   - Ch1: the sentence "The proof is developed rigorously through two
%     supporting lemmas (\Cref{lem:regret-lower-bound,...})" deleted;
%     it was a LIVE \Cref and would have become an undefined reference.
%   - abstract: "and the 2-approximation with its two supporting
%     lemmas" removed from the proved-in-full list.
% Build after all three: 0 errors, 0 undefined, 0 multiply defined.
%
% ── EVERY PROMISE Ch4 MUST KEEP (live \Cref, checked) ─────
%
%  Ch1  lem:interval-extremal-regret  proved; worst case at the box boundary
%  Ch1  thm:regret-k2-hard            proved, by reusing the Ch3 construction
%  Ch1  thm:regret-2approx            exists and states the factor 2
%  Ch1  "Interval regret is harder than interval min-max despite
%        sharing extremal properties, while discrete regret mirrors
%        the complexity hierarchy of discrete min-max."
%  Ch2  "(Worked examples for interval ... require the extremal
%        analysis developed in \Cref{ch:minmax,ch:regret})"  -> S4.2
%        OWES the interval regret worked example.
%  Ch2  eq:regret-def and eq:minmax-regret-objective ALREADY define
%        regret and the objective. S4.1 MUST NOT redefine them.
%        This was a real trap: the old plan budgeted 0.6 pg to
%        "Definition: Regret(T,c) = c(T) - MST(c)", which exists.
%
% ── SOURCES, ALL OPENED AND READ VERBATIM ─────────────────
%
%   Goerigk Def 4.11 + Thm 4.12 ([ABV09])  the regret worst-case
%                                   scenario c^x (upper on x, lower
%                                   off x) and the proof that it
%                                   attains the maximum regret.
%                                   <-- THE ANCHOR FOR R2's PROOF.
%                                   Was missing from this list, which
%                                   is why R2(a) originally specified
%                                   a reconstructed proof. [ABV09] =
%                                   Aissi/Bazgan/Vanderpooten 2009,
%                                   ALREADY in references.bib as
%                                   AissiBazganVanderpooten2009Survey.
%                                   No bibliography gap for R1 or R2.
%   Goerigk Thm 8.6  ([ABV05])      pseudo-poly AND FPTAS, regret,
%                                   discrete, constant K
%   Goerigk Thm 8.7  (Goerigk's own) weakly NP-hard at K=2; proof is
%                                   "the same construction as Thm 8.4"
%   Goerigk Thm 8.8  ([KY97])       strongly NP-hard, K in the input
%   Goerigk Thm 8.9  ([KZ09])       regret not approximable within 2-eps
%   Goerigk Thm 8.16 ([AV04; AL04]) interval regret STRONGLY NP-hard
%   Goerigk Thm 5.26 ([KZ06])       midpoint is a 2-approximation for
%                                   min-max regret under intervals
%                                   <-- the anchor for thm:regret-2approx
%   Goerigk Thm 5.4  ([ABV09])      midpoint is a K-approximation for
%                                   BOTH objectives, discrete; tight
%
%   BIBLIOGRAPHY GAP: [AV04] = Ionut D. Aron and Pascal Van Hentenryck,
%   "On the complexity of the robust spanning tree problem with
%   interval data", Operations Research Letters 32(1), 2004, 36-40.
%   NOT yet in references.bib, and it is the more on-point title than
%   AverbakhLebedev2004. Goerigk attributes Thm 8.16 to BOTH. Add it.
%   Also: Goerigk says STRONGLY NP-hard. Do not drop the adverb.
%   Verify the primary details before use; do NOT take author names or
%   years from Goerigk's bracket keys. AverbakhLebedev2004 and
%   KasperskiZielinski2006 are already present. Ch5 needs three more:
%   [GH23], [GKZ20], [KKZ13]. Same rule applies to each.
%
% ── DRAFTING UNITS (one Flow Card each, in this order) ────
%
%  Ch4 MIRRORS Ch3 SECTION FOR SECTION. Ch3 treats the models in the
%  order interval, discrete, budgeted, and inside each model section
%  it uses one fixed rhythm:
%
%      extremal characterisation -> algorithmic consequence
%          -> worked example -> mechanism note
%
%  Ch4 treats the same models in the same order, minus budgeted, with
%  the same rhythm. The absence of a third model section is what
%  visibly marks the out-of-scope cell. The section tree is therefore:
%
%      S4.1 Regret Formulation            <-> S3.1 Problem Formulation
%      S4.2 Interval Regret               <-> S3.2 Interval Worst-Case
%        S4.2.1 Extremal Characterisation
%        S4.2.2 Hardness and Approximation
%      S4.3 Discrete Scenarios            <-> S3.3 Complexity and Approx.
%        S4.3.1 Constant K                <-> S3.3.1 (same subsection split)
%        S4.3.2 Unbounded K               <-> S3.3.2
%      Summary                            <-> Ch3 Summary
%
%  PROOF BUDGET, restated precisely so it cannot drift:
%      TWO SUBSTANTIAL PROOFS (R2 lemma, R3 theorem-by-reuse)
%      plus ONE THREE-LINE COROLLARY (R2), the analogue of Cor 3.1
%      whose own proof is 81 words. NO FOURTH PROOF.
%
%  R1  S4.1 Formulation                       ~650 words
%      Counterpart of S3.1, which is 608 words over 2 printed pages.
%      Only 67 of those words (11 percent) restate Ch2's definition;
%      the rest is new. S4.1 mirrors the NEW part, paragraph for
%      paragraph:
%      (a) One short paragraph restating eq:minmax-regret-objective in
%          the chapter's terms, opening the way S3.1 does ("the
%          min-max objective introduced in \Cref{sec:uncertainty}").
%          DO NOT redefine regret: Ch2 eq:regret-def already gives
%          Regret(T,c) = c(T) - MST(c), names it, and calls it
%          opportunity cost. This is the one genuine saving against
%          the old plan, worth about 0.4 pages.
%      (b) \paragraph{Decision Version and Complexity Classification.}
%          ONE OR TWO SENTENCES ONLY. S3.1 spends 152 words setting
%          the pattern up; Ch4 refers to it and says the regret
%          version is defined analogously.
%      (c) \paragraph{Maximum-Regret Evaluator.}  THIS OBJECT DOES NOT
%          EXIST YET. notation.tex has \Regret{T}{c} for the regret in
%          a single scenario and NOTHING for the maximum over the
%          uncertainty set. Introduce it with its own equation label,
%          exactly as eq:mm-evaluator introduces \wc{T}. Propose the
%          macro in the Flow Card, never a silent local alias, and
%          add the Appendix A row in the SAME delivery (rule C4).
%      (d) The organising question, the counterpart of S3.1's
%          linearity framing: what form does the maximum regret take
%          as a function of T? S4.1 asks it, S4.2.1 answers it. Ch2
%          already supplies the one-sentence reason it is harder than
%          for cost ("the adversary's choice of c affects both c(T)
%          and MST(c) simultaneously"); build on that sentence.
%      Point at tab:micro-graph-costs for the divergence of the two
%      objectives; DO NOT recompute it. No worked example here.
%      (e) THE OUT-OF-SCOPE SENTENCE BELONGS TO THE CHAPTER OPENING,
%          not to S4.1. The roadmap is where a reader counts two model
%          sections against Ch3's three, so the absence is noticed
%          there. One sentence, no explanation attempted:
%            "Budgeted uncertainty is treated in this thesis only for
%             the min-max objective; \Cref{sec:synthesis} records by
%             citation what is known for its regret counterpart."
%          Ch3's roadmap is the model for tone and length.
%
%  R2  S4.2.1 Extremal Characterisation   ~1100 words + 2 floats
%      Mirrors S3.2 beat for beat.
%      (a) lem:interval-extremal-regret + FULL PROOF. Statement: the
%          maximum regret of a fixed T is attained at the scenario
%          c^T with c_e = u_e for e in E(T) and c_e = l_e otherwise.
%          [Writer A, 2026-08-21] PROOF ROUTE CHANGED. The old route
%          (two monotonicity moves) is CORRECT but is a reconstructed
%          proof, and the proof policy does not admit one unless the
%          author has worked it on paper. The source exists and was
%          simply absent from this header's source list: Goerigk
%          Def 4.11 + Thm 4.12 are this lemma for a general 0/1
%          problem, with a four-line proof.
%          ROUTE NOW USED: fix c in U, let S be an MST under c. Then
%          c(T) - c(S) telescopes over E(T)\E(S) and E(S)\E(T); bound
%          the first sum above by u and the second below by l; what
%          results is exactly c^T(T) - c^T(S), which is at most
%          c^T(T) - MST(c^T). No compactness argument is needed, since
%          c^T lies in U and dominates every c, so the supremum is
%          attained. Verified line by line against the source and
%          numerically on all eight trees of the micro-graph.
%          The old route's intuition is NOT lost: it becomes the
%          motivating paragraph BEFORE the statement, which the house
%          rhythm requires anyway. Goerigk gives it in words: "if
%          x_i = 1, make the item as expensive as possible; if
%          x_i = 0, make the item as cheap as possible."
%          ATTRIBUTION mirrors S3.2, which cites Goerigk Thm 4.8 for
%          the min-max analogue; S4.2.1 cites Thm 4.12 for the regret
%          analogue. Same book, same chapter. Use \cite[Theorem~4.12].
%          DO NOT CLAIM THE MAXIMISER IS UNIQUE. c^T attains the
%          maximum but is not the only scenario that does. Goerigk
%          states this after Thm 4.12, and enumeration confirms it:
%          for T1 the value 3 is attained both at (4,5,7,4,5) and at
%          (2,3,7,4,5). This is a SECOND strictness trap, distinct
%          from the MST-witness trap recorded in (c).
%      (b) cor:regret-interval-evaluation + THREE-LINE PROOF. The
%          analogue of cor:mm-interval-polynomial, and the slot Ch3
%          puts an algorithmic consequence in. Evaluating the maximum
%          regret of a FIXED tree is one MST computation, hence
%          polynomial. Worth stating because it sets up the sharpest
%          contrast in the chapter: evaluation is easy while
%          optimisation is strongly NP-hard (S4.2.2).
%      (c) \paragraph{Worked Example.}  tab:regret-micrograph, columns
%          exactly analogous to tab:budgeted-micrograph: tree, c^T,
%          c^T(T), MST(c^T), regret. Discharges the Ch2 promise that
%          the interval worked example waits for this chapter.
%          VERIFIED BY ENUMERATION, do not re-derive from prose:
%            T1  c^T=(4,5,7,4,5)  16  13  regret 3
%            T2  c^T=(4,5,1,6,5)  15  10  regret 5
%            T3  c^T=(2,5,7,4,7)  19  11  regret 8
%          Full ranking over all eight trees: 3,4,5,7,8,9,9,10.
%          Optimum 3, uniquely at T1.
%          STRICTNESS TRAP: the witnessing MST is NOT unique. At c^T1
%          both T2 and {e1,e4,e5} attain 13; at c^T2 both T1 and
%          {e1,e3,e5} attain 10. Claim no uniqueness there.
%      (d) fig:regret-extremal-scenarios. TWO small panels of the
%          micro-graph side by side, the worst-case scenario for T1
%          and for T2, tree edges drawn at upper bounds and the rest
%          at lower bounds. This is the chapter's mechanism made
%          visual in one picture: different tree, different scenario.
%          Reuse the styles of fig:micro-graph in Ch2. Cheap, and it
%          answers the standing request for figures where genuinely
%          new topics appear.
%      (e) \paragraph{The Role of the Lower Bounds.}  DELIBERATE ECHO
%          of the paragraph of the same name closing S3.2, which
%          reaches the OPPOSITE conclusion and explicitly promises
%          this one: "the insensitivity to the lower bounds is
%          special to the min-max objective: the regret objective of
%          \Cref{ch:regret} measures each tree against the
%          scenario-optimal tree, a comparison that does depend on
%          the lower bounds". Land it concretely: under the all-upper
%          vector u the regrets of T1 and T2 read 1 and 0, against
%          true maxima of 3 and 5. The lower bounds are exactly the
%          off-tree entries of c^T.
%          THE MECHANISM SENTENCE IS OWNED HERE, in full, because this
%          is the only place with the numbers to land it: min-max has
%          one worst-case vector serving every tree
%          (lem:interval-extremal-cost); regret's worst-case vector
%          depends on the tree. Same boundary, different structure.
%          ALLOCATION, so the fact is not stated three times:
%            S4.2.1(e)  full statement, with the numbers   <-- here
%            Ch4 Summary  compressed to a clause
%            Ch5 pattern 1  consequence only, then \Cref back
%          The old plan had all three stating it in full.
%
%  R3  S4.2.2 Hardness and Approximation      ~500 words, NO PROOFS
%      thm:regret-interval-hard STATED (Goerigk 8.16, [AV04; AL04]),
%      STRONGLY NP-hard, adverb included.
%      thm:regret-2approx STATED (Goerigk 5.26, [KZ06]).
%      One paragraph on the contrast the thesis turns on: interval
%      min-max is polynomial (cor:mm-interval-polynomial), interval
%      regret is strongly NP-hard, and yet regret admits a factor 2
%      here where discrete regret admits only K.
%      Tightness of the factor 2 belongs to Ch5, not here.
%
%  R4  S4.3 Discrete Scenarios                ~1000 words
%      Same subsection split as S3.3, same titles pattern.
%      S4.3.1 Constant K:
%        thm:regret-k2-hard + PROOF BY REUSE. Do not restate the
%        construction of thm:mm-k2-hard; refer to it and give only
%        the adjusted analysis (in every scenario a spanning tree of
%        cost zero exists, so regret coincides with cost). REFER to
%        fig:partition-grid rather than redrawing it.
%        \paragraph{A Small Instance.} Echo of the paragraph of the
%        same name in S3.3.1: reuse the instance already worked there
%        and show regret and cost coincide on it. Cheap, and it makes
%        the word "reuse" concrete instead of asserted.
%        thm:regret-kconst-pseudo STATED (Goerigk 8.6, which covers
%        the pseudo-polynomial algorithm AND the FPTAS together).
%      S4.3.2 Unbounded K:
%        thm:regret-kunbdd-hard STATED (Goerigk 8.8) and the 2-eps
%        inapproximability STATED (Goerigk 8.9). No 3-partition
%        sketch. Note the symmetry with Ch3: both objectives carry a
%        2-eps bound.
%
%  R5  Summary                                ~280 words
%      Unnumbered, \addcontentsline, matching Ch2 and Ch3. THREE OR
%      FOUR PARAGRAPHS, not one block: Ch3's Summary had to be broken
%      up because 286 words in one paragraph was the second-longest
%      paragraph in the thesis against a median of 56.
%      Mechanism for regret across the two models, ONE contrast with
%      min-max, and a provenance sentence in the shape Ch3 uses,
%      naming what is proved here and what is quoted. The RWTH
%      guidelines grade this explicitly. No result list;
%      tab:complexity-landscape owns the inventory. Cap 0.75 pages.
%
%  Projected: R1 650 + R2 1100 + R3 500 + R4 1000 + R5 280 = 3530
%  words = 6.5 pages, + 2 floats (1.8) + opening and boundary waste
%  (0.7) = 8.5 to 9.0 printed pages. At the top of the band, so if
%  something must give it is R3's prose, never a float.
%
% ── HOUSE CONVENTIONS INHERITED FROM LOCKED Ch2 AND Ch3 ───
%   - Numbering is per chapter, one counter per environment. ALWAYS
%     reference by label, never by number.
%   - \textcite for narrative attribution, \cite for parenthetical;
%     \Cref for named objects, \eqref for equations. No bare \ref.
%   - Costs are non-negative in all three models.
%   - "partition problem" lower case; "weakly"/"strongly" NP-hard
%     always with the adverb; "cost list" for the K-vector;
%     never "nominal MST", write "MST under <cost vector>".
%   - British English, no em dashes, no -ize spellings, no
%     contractions, no hand-wave markers.
%   - Verify a constructed subgraph is a spanning tree through the
%     two-of-three characterisation in sec:graph-notation.
%   - Universally quantified claims about hardness or non-linearity
%     need "in general". This failure mode fired four times in Ch3.
%
% ── MICRO-GRAPH FACTS (verified by full enumeration) ──────
%   discrete scenarios c^(1) lower, c^(2) upper, c^(3) midpoints:
%     min-max regret optimum is T1 = {e1,e2,e3} with max regret 1
%     (T2 has 3, T3 has 4); tab:micro-graph-costs already carries the
%     full cost/regret table.
%   interval uncertainty, worst case for a fixed T at c_e = u_e on T
%   and c_e = l_e off T:
%     max regret T1 = 3, T2 = 5, T3 = 8; optimum is T1 at 3.
%     (Full ranking over the eight trees: 3, 4, 5, 7, 8, 9, 9, 10.)
%   CONTRAST TO Ch3: the min-max optimum is T2 under both discrete and
%   interval uncertainty, while the regret optimum is T1 under both.
%   The two objectives therefore genuinely disagree on this instance,
%   which is the point tab:micro-graph-costs already makes in Ch2.
%═══════════════════════════════════════════════════════════

%─────────────────────────────────────────────────────────
% CHAPTER OPENING + SECTION 4.1: REGRET FORMULATION
%
% Provenance: Unit R1 v2 (final), Writer A, 2026-08-21. Built and
%   measured on a clean tree: 0 errors, 0 undefined, 0 multiply-defined,
%   overfull unchanged at the Ch2 baseline of 3. Opening 201 raw words /
%   0.42 pg; S4.1 534 raw words / 1.19 pg; R1 total 1.61 printed pages.
%   S4.1 is -18 percent against the planned 650, outside tolerance, and
%   the delta is explained: v1 (-14 percent, in tolerance) was revamped
%   on the author's direction to cut backward scaffolding. Four S3.1
%   references inside S4.1 became one; the decision paragraph sits at
%   the two sentences the plan itself prescribes. Content inventory
%   (a)-(e) is complete; nothing was cut; the saved pages fall to R2.
%
% Goal: state the min-max regret spanning tree problem at the edge level
%   and isolate the evaluator the chapter minimises, WITHOUT redefining
%   regret (Ch2 owns eq:regret-def).
% Prereqs, all confirmed present: eq:regret-def, eq:minmax-regret-objective,
%   eq:mm-objective, eq:mm-evaluator, sec:uncertainty, sec:complexity,
%   sec:mm-formulation, tab:micro-graph-costs, lem:interval-extremal-cost,
%   sec:synthesis. Macros \Regret, \MSTcost, \cT, \Scenarios, \cs, \wc.
% Results proved: none. Results stated: none. No theorem environment
%   appears in S4.1, which is what holds the chapter's proof budget at two.
% Sources: none. S3.1, its counterpart, also cites nothing.
%
% NEW LABELS  eq:regret-objective (4.1), eq:regret-evaluator (4.2).
% NEW MACRO   \wcr{T} -> wcr(T), added to notation.tex AND to Appendix A
%   in this same delivery (rule C4). \wr was NOT available: it is LaTeX's
%   wreath product. \wcr, \maxreg, \mreg, \wreg were all free; \wcr was
%   chosen for the exact parallel with \wc.
%
% ORGANISING DEVICES OF THIS UNIT, so later units stay consistent:
%  - THE FLOOR. MST(c) is introduced as "a floor set by the scenario
%    alone, beneath which no spanning tree can be driven", and the image
%    is reused exactly twice more ("the floor also shifts every tree
%    equally", "the floor closes that route"). If R2-R5 want the image,
%    keep the word "floor"; do not introduce a synonym.
%  - THE TWO QUESTIONS. S4.1 ends by splitting the chapter's work into
%    (1) what form does wcr(T) take, (2) can that form be minimised over
%    all trees at once, and states that under discrete uncertainty (1)
%    is immediate while under intervals (1) already requires proof.
%    S4.2.1 answers (1) for intervals; S4.2.2 and S4.3 answer (2).
%    The Summary can close over this pair.
%
% DECISIONS TAKEN HERE, so a later thread does not undo them:
%  - NO FORWARD REFERENCE to cor:regret-interval-evaluation, which does
%    not exist until R2. The decision-version paragraph points at
%    \Cref{sec:regret-extremal} instead, exactly as S3.1 points at
%    \Cref{sec:mm-extremal} rather than at cor:mm-interval-polynomial.
%    R1 therefore compiles clean when delivered on its own.
%  - THE MECHANISM SENTENCE IS NOT STATED HERE. S4.1 poses the questions
%    and stops; S4.2.1(e) owns the answer in full. The roadmap gives the
%    RESULT for S4.2 (characterisation exists, strongly NP-hard
%    nonetheless, factor 2 survives), not the mechanism.
%  - The directional argument (raise on T, lower off T) is R2's
%    motivating paragraph and is deliberately not spent here. S4.1 says
%    only WHY separation fails: the floor couples all m coordinates, and
%    per Ch2 the all-upper-bounds vector is in general not the regret
%    worst case. "In general" is load-bearing there; on some instances
%    u IS the worst case.
%  - O(Km log n) versus S3.1's O(Kn) is correct and deliberate: regret
%    evaluation needs one MST per scenario, min-max evaluation needs none.
%─────────────────────────────────────────────────────────

\chapter{Min-Max Regret Spanning Tree}\label{ch:regret}

\Cref{ch:minmax} judged a spanning tree by an absolute standard, the cost it incurs in the worst case, with no other tree entering the verdict.
This chapter keeps the setting: a tree is still committed before the costs are revealed, and an adversary still selects from a prescribed uncertainty set.
What changes is the standard.
The min-max regret objective~\eqref{eq:minmax-regret-objective} charges the tree only the gap between its cost and the best cost available in the same scenario, so the adversary now works to widen a gap rather than to inflate a total.

\Cref{sec:regret-definition} writes this objective out at the edge level and isolates the evaluator the chapter minimises.
\Cref{sec:regret-interval} treats interval uncertainty: an extremal characterisation as explicit as the min-max one exists, the problem is strongly $\mathsf{NP}$-hard nonetheless, and a $2$-approximation is available.
\Cref{sec:regret-complexity-discrete} treats discrete uncertainty, where the hierarchy of \Cref{sec:mm-complexity} returns: weakly $\mathsf{NP}$-hard already at $K = 2$, pseudo-polynomial and approximable for any constant $K$, and strongly $\mathsf{NP}$-hard once $K$ is part of the input.
Budgeted uncertainty is treated in this thesis only for the min-max objective; \Cref{sec:synthesis} records by citation what is known for its regret counterpart.
A closing summary draws the two models together.

\section{Regret Formulation}\label{sec:regret-definition}

The regret~\eqref{eq:regret-def} introduced in \Cref{sec:uncertainty} measures a spanning tree $T \in \cT$ under a realised cost vector $c$ by the difference $\Regret{T}{c} = c(T) - \MSTcost{c}$.
Expanding the tree cost edge by edge turns the min-max regret objective~\eqref{eq:minmax-regret-objective} into the \emph{\textcolor{RWTHBlue}{min-max regret spanning tree problem}}
\begin{equation}\label{eq:regret-objective}
\min_{T \in \cT}\; \max_{c \in \Scenarios}\; \biggl[\, \sum_{e \in E(T)} c_e \;-\; \MSTcost{c} \biggr].
\end{equation}
The bracket subtracts an optimal value from a tree cost.
Only its first term depends on the tree; the second, $\MSTcost{c}$, is a floor set by the scenario alone, and no spanning tree costs less.
A scenario that is expensive for $T$ therefore gains the adversary nothing if it raises the floor by just as much.

The floor also shifts every tree equally.
Under a single fixed scenario, regret and cost differ by the constant $\MSTcost{c}$ and therefore rank the spanning trees identically, so the two objectives can differ only through the maximisation.
On the micro-graph they do differ: across the three scenarios recorded in \Cref{tab:micro-graph-costs}, the min-max tree is $T_2$ and the min-max regret tree is $T_1$.

\paragraph{Decision Version and Complexity Classification.}

The decision version asks, for a bound $B \in \R$, whether some spanning tree has maximum regret at most $B$; the tree itself is the certificate, as in \Cref{sec:mm-formulation}, so membership in $\mathsf{NP}$ turns on evaluating the inner maximum in polynomial time.
That evaluation is costlier than its min-max counterpart, because every scenario is now costed against its own optimum.
Under discrete uncertainty the maximum regret of $T$ is the largest of the $K$ differences $\cs{k}(T) - \MSTcost{\cs{k}}$, which takes $O(Km \log n)$ time against $O(Kn)$ for the worst-case cost.
The $K$ minimum spanning trees do not depend on $T$, so computing them once serves every tree.
Under interval uncertainty the evaluation rests on the extremal characterisation of \Cref{sec:regret-extremal}.

\paragraph{Maximum-Regret Evaluator.}

The quantity the chapter minimises is the \emph{\textcolor{RWTHBlue}{maximum regret}} of a fixed spanning tree $T \in \cT$,
\begin{equation}\label{eq:regret-evaluator}
\wcr{T} \;:=\; \max_{c \in \Scenarios}\; \Regret{T}{c} \;=\; \max_{c \in \Scenarios}\; \biggl[\, \sum_{e \in E(T)} c_e \;-\; \MSTcost{c} \biggr],
\end{equation}
the counterpart of the worst-case cost~\eqref{eq:mm-evaluator}.
The problem in~\eqref{eq:regret-objective} collapses to $\min_{T \in \cT} \wcr{T}$, and the chapter turns on two questions about $\wcr{T}$: what form does it take as a function of the tree, and can that form be minimised over all spanning trees at once?

For the worst-case cost, one answer settles both questions.
Over the interval set, \Cref{lem:interval-extremal-cost} lets every cost climb to its upper bound independently of the rest, so the evaluator becomes a sum of fixed per-edge weights, and minimising such a sum is a single deterministic minimum spanning tree computation.
No such reduction is available for the maximum regret.
The floor is the reason: $\MSTcost{c}$ depends on all $m$ coordinates of $c$ at once, so the bracket in~\eqref{eq:regret-evaluator} is not a sum of independent per-edge terms, and its maximum cannot be found by maximising one coordinate at a time.
\Cref{sec:uncertainty} already records the consequence: the all-upper-bounds vector, which is the worst case under the min-max objective, is in general not the worst case for regret.
Locating the maximum requires an argument that treats both terms of the bracket together.

These two questions organise the two sections that follow.
Under discrete uncertainty the first is immediate, the maximum in~\eqref{eq:regret-evaluator} running over the $K$ scenarios of a finite uncertainty set, and \Cref{sec:regret-complexity-discrete} is occupied entirely by the second.
Under interval uncertainty even the first requires proof, and \Cref{sec:regret-extremal} begins there.

\section{Interval Regret}\label{sec:regret-interval}
% Counterpart of S3.2. Same rhythm: extremal characterisation, algorithmic
% consequence, worked example, mechanism note.

\subsection{Extremal Characterisation}\label{sec:regret-extremal}
% UNIT R2  ~1100 words + tab:regret-micrograph + fig:regret-extremal-scenarios.
% PROVED RESULT 1 of 2, plus the three-line corollary.

\begin{lemma}[Interval Extremal Regret]\label{lem:interval-extremal-regret}
\end{lemma}

% cor:regret-interval-evaluation, analogue of cor:mm-interval-polynomial:
% evaluating the maximum regret of a FIXED tree is one MST computation.
% Three-line proof. Sets up the contrast with S4.2.2: evaluation easy,
% optimisation strongly NP-hard.

% \paragraph{Worked Example.}  tab:regret-micrograph, columns as in
% tab:budgeted-micrograph. Verified: T1 -> 3, T2 -> 5, T3 -> 8; full ranking
% 3,4,5,7,8,9,9,10; optimum 3 uniquely at T1. The witnessing MST is NOT
% unique (ties at c^T1 and c^T2); claim no uniqueness there.
% fig:regret-extremal-scenarios: two panels, worst-case scenario for T1 and
% for T2 side by side. Reuse fig:micro-graph styles.

% \paragraph{The Role of the Lower Bounds.}  Deliberate echo of the paragraph
% of the same name closing S3.2, which promises this one and reaches the
% opposite conclusion. Land it concretely: under u alone the regrets read
% 1 and 0 against true maxima 3 and 5.

\subsection{Hardness and Approximation}\label{sec:regret-approx-interval}
% UNIT R3  ~500 words.  NO PROOFS.

\begin{theorem}[Interval NP-Hardness]\label{thm:regret-interval-hard}
\end{theorem}

\begin{theorem}[2-Approximation via Midpoint]\label{thm:regret-2approx}
\end{theorem}

\section{Discrete Scenarios}\label{sec:regret-complexity-discrete}
% UNIT R4  ~1000 words.  Same subsection split as S3.3.

\subsection{Constant \texorpdfstring{$K$}{K}}\label{sec:regret-const-k}
% PROVED RESULT 2 of 2, by REUSE of thm:mm-k2-hard. Refer to
% fig:partition-grid, do not redraw it.

\begin{theorem}[K=2 Weak NP-Hardness]\label{thm:regret-k2-hard}
\end{theorem}

% \paragraph{A Small Instance.} Echo of the paragraph of the same name in
% S3.3.1: reuse the instance worked there, show regret and cost coincide.

\begin{theorem}[Pseudo-Polynomial for Constant K]\label{thm:regret-kconst-pseudo}
\end{theorem}

\subsection{Unbounded \texorpdfstring{$K$}{K}}\label{sec:regret-unbdd-k}
% Stated and cited only. Note the 2-eps symmetry with Ch3.

\begin{theorem}[Strong NP-Hardness]\label{thm:regret-kunbdd-hard}
\end{theorem}

% UNIT R5  ~280 words, several paragraphs, cap 0.75 pages.
% Mechanism + ONE contrast with min-max + a provenance sentence naming
% what is proved here and what is quoted. No result list.
\section*{Summary}
\addcontentsline{toc}{section}{Summary}```

