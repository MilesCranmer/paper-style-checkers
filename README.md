# paper-style-checkers
Accumulation of style checklists for research papers, implemented programmatically

## All figures have a reference in the text:
```
:%g/\\label{.*fig.*}/t?begin{document}?
```
- Copies all figure labels to below \begin{document}
```
:'<,'>g/.*/exe "norm! mxf{lvt}y/ref{\<C-R>\"\<enter>yy'xP"
```
- With the labels highlighted, this searches for reference to that text, and pastes it above.
- Will fail if nothing is found.

## All typewriter-text terms are used consistently:

```
:%g/\\texttt{/t?begin{document}?
```
- Copies all `tt` text to the beginning of the document

```
:1/begin{document}/+1,1/begin{document}//texttt.*\n\(\(.*texttt.*\)\@!.\)*\n/s/^.*\(\\texttt{.\{-}}\).*$/\1/g
```
- Cleans out non-tt text (most of this is just a range instead of highlighting).

With the range: `:1/begin{document}/+1,1/begin{document}//texttt.*\n\(\(.*texttt.*\)\@!.\)*\n/`, append:
- `sort`, to sort the terms
- `uniq`, to show only the unique ones.
