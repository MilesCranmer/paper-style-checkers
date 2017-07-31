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
:'<,'>s/^.*\\texttt{\(.\{-}\)}.*$/\1/g
```
- Cleans out non-tt text from the selection.

`:'<,'>!uniq`
- Shows only the unique terms.

Then, you should visually inspect these to see if there are any mixed-case terms, and fix those by hand.

Also, delete the terms which don't matter to you.

Then, run on the first line of tt terms:
```
:silent! :.,/\n\n/g/^/exe "norm! qbqmx0\"ay$IWord: \<esc>/\\n\\n\<enter>j/\\(texttt{\\)\\@!\<C-R>\"a\<enter>\"byy'x\"bP"
```
If a line that isn't blank is pasted above the word, then it was stated with some non-tt usage. Then you can go modify it.

You can also run this on terms defined with `\textsc{...}`, and so on.

## Most important papers covering same topics as paper paper are referenced

Paste bibcodes into: http://adsabs.harvard.edu/tools/citehelper, and implement them one-by-one if necessary.

### To find relevant bibcodes:

Copy the main tex file, and run:

```
:%v/cite/d_
```
To delete lines without citations

```
:%s/\\cite.\?{\(.\{-}\)}/\rcitation: \1\r/g | %v/citation: /d_ | %s/citation: //g 
```

Then

```
:%s/,/\r/g
```

To show one reference on each line.

Then, paste these at the top+1 of a (merged) copy of your bib file. Run:

```
:silent! :2,/\n\n/g/^/exe "norm! mx0*0Vf{%y'xP'xdd"
```

To move up only the relevant citations in between the reference phrases. Then, delete everything after the last one. Now, you will need to find the ADS urls by hand.

Run this to get the bibcodes out:
```
:%v/adsabs/d_ | %s/^.*edu\/abs\/\(.*\)},\s*$/\1/g
```
These can be pasted into the citation helper.

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


