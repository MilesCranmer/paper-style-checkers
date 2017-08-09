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

## All typewriter-text (or, `\textsc{}`, `\textit`, etc.) terms are used consistently:

```
:%g/\\texttt{/t?begin{document}?
```
- Copies all `tt` text to the beginning of the document

```
:'<,'>s/^.*\\texttt{\(.\{-}\)}.*$/\1/g
```
- With `tt` term text highlighted, this cleans out non-tt text from the selection.

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

## Most important papers covering same topics as paper are referenced

### (Using the abstract)

Paste the abstract text into vim, and put it all into one line (you might want to call `:set paste` before pasting).

`:s/[\.;:,()]//gc`
- Deletes all punctuation in the abstract

`:s/\s\+/%20/gc`
- This gets the abstract text ready to paste into a URL.

After this, copy-paste the following URL base onto the start of your vim string:

`http://adsabs.harvard.edu/cgi-bin/nph-abs_connect?return_req=no_params&arxiv_sel=ALL&db_key=PRE&text=`

Make sure there are no spaces after the `=`. Then, copy this entire thing into your browser URL bar, and hit enter. The abstracts
hit should look very similar to what you are working on.

### (Using bibcodes)

Paste bibcodes into: http://adsabs.harvard.edu/tools/citehelper, and implement them one-by-one if necessary.

#### To find relevant bibcodes:

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

## No grammar mistakes; well-written English

Both the tools [`detex`](https://www.ctan.org/tex-archive/support/detex?lang=en), which takes the TeX file and extracts the text, and a [pdf to text](http://pdftotext.com/) tool work to extract text from the latex. You should try them both and see what
works best for your particular file. If there is a lot of inline math, you may find that the pdf to text works better.
Else, `detex` generally produces cleaner results.

The majority of this text is usable, aside from some scattered numbers from figures. One thing that must be fixed is the newlines within the same paragraph:

```
:%g/.*\S.*\n.*\S.*\n/.,/\n\s*\n/j
```

This sews together all single newlines, but not double newlines. The result is paragraphs being in a single line of the document.

Using the text, I send it to [Grammarly](https://app.grammarly.com/), and correct the mistakes by hand. You can
also try [hemingway](http://www.hemingwayapp.com/), but this often seems too extreme
for academic writing (and I find it dissuades a healthy amount of sentence type variety).

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


