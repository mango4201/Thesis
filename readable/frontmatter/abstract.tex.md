## frontmatter/abstract.tex

```tex
\selectlanguage{english}
\chapter*{Abstract}

The minimum spanning tree (MST) connects all network nodes at minimum cost, but edge costs are often uncertain due to market fluctuations, estimation errors, or operational changes. This thesis studies robust spanning trees under two uncertainty models (discrete scenarios, interval ranges) and two objectives (Min-Max, Min-Max Regret), classifying computational complexity and approximability across this design space.

We provide a self-contained treatment from first principles. After establishing MST foundations with five complete proofs (fundamental cycle and cut lemmas, three optimality criteria via exchange arguments), we systematically analyse four problem variants. For intervals, we prove extremal lemmas showing worst cases occur at boundaries: Min-Max becomes polynomial while Regret remains NP-hard with a 2-approximation. For discrete scenarios, we prove weak NP-hardness for $K=2$ via partition reduction and survey results for larger $K$ including pseudo-polynomial algorithms and approximation schemes.

The thesis delivers nine complete proofs in total: five MST foundations, two representative hardness proofs (partition reductions for Min-Max and Regret with $K=2$), and two approximation results (the 2-approximation algorithm with its supporting lemmas). A comprehensive classification table synthesises eight problem variants. A fixed four-vertex micro-graph illustrates concepts throughout. This work balances mathematical rigour with pedagogical clarity, serving as a focused entry point into robust combinatorial optimisation.

\addcontentsline{toc}{chapter}{Abstract}```

