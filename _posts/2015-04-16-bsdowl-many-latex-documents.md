---
title: Organise collections of LaTeX documents with BSD Owl Scripts
layout: post
category: make
tags: [bsdowl, texmf]
---
Let us discuss how to handle collections of LaTeX documents with the
build system [BSD Owl Scripts][bsdowl-home].
In our example we pretend
that we are preparing an electronic journal and want to distribute
each article of the journal as a separate electronic document.


## Organisation on the file-system

We use the following simple organisation at the file-system level:

1. We prepare a directory holding each issue of our journal, for
   instance `~/journal`.

2. Each issue of the journal is represented by a subdirectory.

3. Each article of the journal is represented by a subdirectory of the
   directory corresponding to the issue it belongs to.

Assume we already have several articles, as demonstrated by the
following command output:

~~~ console
% find ./journal -name '*.tex'
./journal/issue-2013-1/01-galdal/article.tex
./journal/issue-2013-1/02-arathlor/article.tex
./journal/issue-2013-2/01-mirmilothor/article.tex
./journal/issue-2013-2/02-eoron/article.tex
./journal/issue-2013-2/03-echalad/article.tex
~~~

Names like `galdal`, `arathlor` are the names of fictional authors of
articles of our journal. Each submission has a directory containing
the text `article.tex` of the article.


## Typeset each single article

We rely on **BSD Owl Scripts** to transform each article in a PDF
file.  We therefore add a Makefile in each directory corresponding to
an article.

~~~ console
% find ./journal -name 'Makefile'
./journal/issue-2013-1/01-galdal/Makefile
./journal/issue-2013-1/02-arathlor/Makefile
./journal/issue-2013-2/01-mirmilothor/Makefile
./journal/issue-2013-2/02-eoron/Makefile
./journal/issue-2013-2/03-echalad/Makefile
~~~

Each of these Makefiles can actually be as simple as

~~~ makefile
DOCUMENT=       article.tex
.include "latex.doc.mk"
~~~

These Makefiles can also define file-system locations where TeX will
lookup for common assets, define rules to automatically build some
tables or figures, or use any of the more advanced techniques
described [in the documentation][bsdowl-doc].  Since we want to keep
focus on the organisational features of **BSD Owl Scripts** we will
stick to that minimalistic Makefile.


## Bundle the articles together

To orchestrate the preparation of all our articles with
**BSD Owl Scripts** we just need to write additional Makefiles.

~~~
./journal/Makefile
./journal/issue-2013-1/Makefile
./journal/issue-2013-2/Makefile
./journal/issue-2013-3/Makefile
~~~

Each `Makefile` basically contains the list of subdirectories where
_make_ should descend to actually `build`, `install` or `clean`.
Readers fond of design patterns will recognise aggregates implementing
a delegate pattern.

The file `./journal/Makefile` should contain:

~~~ makefile
PACKAGE=        journal

SUBDIR=         issue-2013-1
SUBDIR+=        issue-2013-2
SUBDIR+=        issue-2013-3
.include "bps.subdir.mk"
~~~

The file `./journal/issue-2013-1/Makefile` should contain:

~~~ makefile
SUBDIR=         01-galdal
SUBDIR+=        02-arathlor
.include "bps.subdir.mk"
~~~

The remaining files `./journal/issue-2013-2/Makefile` and
`./journal/issue-2013-3/Makefile` can be similarly prepared. With
these settings, the targets `all`, `build`, `clean`, `distclean`,
`realclean` and `install` are delegated to `Makefile`s found in the
subdirectories listed by _SUBDIR_.

The variable `SUBDIR_PREFIX` can be used to define a customised
installation path for each article, so that the `Makefile` building a
document could be

~~~ makefile
DOCUMENT=       article.tex
DOCDIR=         ${HOME}/publish/journal${SUBDIR_PREFIX}
.include "latex.doc.mk"
~~~

With this setting, the document
`./journal/issue-2013-1/01-galdal/article.pdf` will be installed as
`${HOME}/publish/journal/issue-2013-1/01-galdal/article.pdf` and so
on.  It is possible to tweak this in all possible ways to use
arbitrary naming schemes for installed articles, like for instance
`${HOME}/publish/journal/issue-2013-1/01-galdal.pdf`
or whatever we fancy.


## Declare locations of file assets

We can elaborate on our basic setup to handle the case where our
documents share assets, for instance a logo for our journal or some
custom LaTeX packages.  In **BSD Owl Scripts** we can use the
*TEXINPUTS* variable to declare one or more such locations.
For instance the declaration

~~~ makefile
TEXINPUTS=      ${HOME}/share/texmf/tex/latex/journal
~~~

will arrange so that TeX finds all files in
`${HOME}/share/texmf/tex/latex/journal` when it needs them.  This
statement can be added to individual Makefiles responsible for the
preparation of an article, or it can be added to
`./journal/Makefile.inc`.  The latter file is read by **make** every
times it processes a Makefile based on **BSD Owl Scripts**.  Adding
that declaration to `./journal/Makefile.inc` is therfore similar to
adding it to each single Makefile in the project.


  [bsdowl-doc]:         https://github.com/michipili/bsdowl/blob/master/doc/LaTeXDocument.md
  [bsdowl-home]:        https://github.com/michipili/bsdowl
