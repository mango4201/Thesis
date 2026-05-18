## frontmatter/abstract.tex

```tex
\selectlanguage{english}
\chapter*{Abstract}

The minimum spanning tree (MST) connects all network nodes at minimum cost, but edge costs are often uncertain due to market fluctuations, estimation errors, or operational changes. This thesis studies robust spanning trees primarily under two uncertainty models (discrete scenarios and interval ranges) and two objectives (min-max, min-max regret), classifying computational complexity and approximability across this design space; a third model, budgeted uncertainty, receives a focused complementary treatment.

We provide a self-contained treatment from first principles. After establishing MST foundations with five complete proofs (fundamental cycle and cut lemmas, three optimality criteria via exchange arguments), we systematically analyse the resulting problem variants. For intervals, we prove extremal lemmas showing worst cases occur at boundaries: min-max becomes polynomial while regret remains $\mathsf{NP}$-hard with a 2-approximation. For discrete scenarios, we prove weak $\mathsf{NP}$-hardness for $K=2$ via partition reduction and survey results for larger $K$ including pseudo-polynomial algorithms and approximation schemes. The budgeted case is shown to inherit polynomial-time solvability for min-max through a citation-led reduction to nominal MST instances.

The thesis delivers nine complete proofs in total: five MST foundations, two representative hardness proofs (partition reductions for min-max and regret with $K=2$), and two approximation results (the 2-approximation algorithm with its supporting lemmas). A comprehensive classification table synthesises the complexity landscape. A fixed four-vertex micro-graph illustrates concepts throughout. This work balances mathematical rigour with pedagogical clarity, serving as a focused entry point into robust combinatorial optimisation.

\addcontentsline{toc}{chapter}{Abstract}```

