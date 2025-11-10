## tools/pack-frontmatter.tex

```tex
\input{tools/srcpack-template}

\renewcommand{\PackTitle}{Front matter, notation, bibliography}
\renewcommand{\PackFiles}{%
  \SrcFile{main.tex}{main.tex}
  \SrcFile{00_titlepage.tex}{00\_titlepage.tex}
  \SrcFile{notation.tex}{notation.tex}
  \SrcFile{references.bib}{references.bib}
  \SrcFile{frontmatter/abstract.tex}{frontmatter/abstract.tex}
  \SrcFile{frontmatter/affidavit.tex}{frontmatter/affidavit.tex}
  % \SrcFile{frontmatter/kurzfassung.tex}{frontmatter/kurzfassung.tex}
  % \SrcFile{frontmatter/acknowledgements.tex}{frontmatter/acknowledgements.tex}
}
```

