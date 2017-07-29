# paper-style-checkers
Accumulation of style checklists for research papers, implemented programmatically in vim.

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
:'<,'>s/^.*\(\\texttt{.\{-}}\).*$/\1/g
```
- Cleans out non-tt text from the selection.

`:'<,'>!uniq`
- Shows only the unique terms.

Then, you should visually inspect these to see if there are any mixed-case terms, and fix those by hand.

Also, delete the terms which don't matter to you.

Then, run on the first term:
```
:exe "norm! mxf{lvt}y/[^{]\<C-R>\"\\>\<enter>yy'xP"
```
If a line is pasted above it, then it was stated with some non-tt usage. Then you can go modify it.

Else, continue to run these commands on each line with `@:`, or record this exe as part of a macro (with `q`).

## General grammar checks

Because of the use of macros or math, instead of using something like [`detex`](https://www.ctan.org/tex-archive/support/detex?lang=en), which takes the TeX file and extracts the text, I find using a [pdf to text](http://pdftotext.com/) tool on the compiled file is much better.

The majority of this text is usable, aside from some scattered numbers from figures. One thing that must be fixed is the newlines within the same paragraph:

```
:%g/.*\S.*\n.*\S.*\n/.,/\n\s*\n/j
```

This sews together all single newlines, but not double newlines. The result is paragraphs being in a single line of the document.

Using the text, I send it to [Grammarly](https://app.grammarly.com/), and correct the mistakes by hand.

It might also be wise sometimes to comment-out section headers and some figures ahead of time, using a command like:
```
:%g/\\section\|\\subsection\|\\subsubsection/norm I%52d0baa5945e6
```
Where the `52d0baa5945e6` is a hash key, so you remember which lines to uncomment, and `%` is the comment character.

Once you have recompiled, then just:
```
:%g/^%52d0baa5945e6/norm 0df6
```

To uncomment the lines.
