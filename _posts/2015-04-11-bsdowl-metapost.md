---
title: Drawing METAPOST pictures with BSD Owl Scripts
layout: post
category: make
tags: [bsdowl, metapost, graphics]
---
[METAPOST][metapost-home] is powerful language for creating technical
drawings and a program my John Hobby, it is found in most if not all
**TeX** distributions.  While most **LaTeX** compilation assistants do not pay
much attention to **METAPOST**, it is very well integrated in
[BSD Owl Scripts][bsdowl-home] so that preparing a **LaTeX** document
containing beautiful **METAPOST** pictures is achieved by a Makefile as simple
as

~~~ makefile
DOCUMENT=		galley.tex
SRCS+=			figures.mp
.include "latex.doc.mk"
~~~

It is also possible to produce pictures for themselves, using a
Makefile similar to

~~~ makefile
DOCUMENT=		figures.mp
MPDEVICE=		eps pdf png svg
.include "mpost.doc.mk"
~~~

It will produce EPS, PDF, PNG and SVG versions of the figures.


If you do not know **METAPOST** here are few figures drawn with it:

![A performance comparison chart][example-chart]
![A timeline][example-timeline]
![A UML diagram][example-uml]

These pictures are [examples][blueprint-example] found in my
[Blueprint][blueprint-home] project, a library of **METAPOST**
definitions. This project also illustrates the use of **BSD Owl Scripts**
to produce METAPOST pictures.

**See also:**
[Producing LaTeX documents][bsdowl-doc] (BSD Owl Scripts documentation),
[TeX Users Group page dedicated to METAPOST][metapost-tug],
[André Heck's METAPOST tutorial][metapost-heck].

  [blueprint-home]:     https://github.com/michipili/blueprint
  [blueprint-example]:  https://github.com/michipili/blueprint/tree/master/example
  [bsdowl-doc]:         https://github.com/michipili/bsdowl/blob/master/doc/PackageDebian.md
  [bsdowl-home]:        https://github.com/michipili/bsdowl
  [example-chart]:      https://raw.githubusercontent.com/michipili/blueprint/master/Library/Assets/example_legend-0.png
  [example-timeline]:   https://raw.githubusercontent.com/michipili/blueprint/master/Library/Assets/example_timeline-0.png
  [example-uml]:        https://raw.githubusercontent.com/michipili/blueprint/master/Library/Assets/example_umlcomponent-0.png
  [metapost-heck]:      http://staff.science.uva.nl/~heck/Courses/mptut.pdf
  [metapost-home]:      http://cm.bell-labs.com/who/hobby/MetaPost.html
  [metapost-tug]:       http://www.tug.org/metapost.html
