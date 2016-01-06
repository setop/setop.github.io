---
title: Of course grepping logs is terrible!
layout: post
category: essay
tagline: "Generalists versus specialists"
tags: [bsdowl, texmf]
---
There has been a heated discussion on [LinuxFR](https://linuxfr.org)[^1]
about a blog article
“[Grepping logs is terrible](https://asylum.madhouse-project.org/blog/2015/05/05/grepping-logs-is-terrible/on)”
whose author strongly advocates in favor of *binary log storage*
solutions, which perform consistently better than *text log storage*
solutions in any regards.  The author, whose name remains undisclosed[^2],
presents himself as an experienced system administrator and gives a
fair account of the arguments usually presented by tenants of the
*text log storage* faction.

This mysterious author is definitely right in asserting that *binary
log storage* solution perform better than *text log storage*
solutions.  This is in some sense, totally obvious, and discussing
this alternative from a performance perspective misses an important
point, that I would like to discuss here.

Our author is a *professional* system administrator and he, of course,
has to work with astronomical quantities of data and to make as much
sense out of this data as possible.  The numbers he can infer from
log-analysis give the pulse of the system. How fast he can get to
relevant information has a direct incidence on the time he needs to
accomplish basic tasks in his work. How easy it is to retrieve log
records satisfying complex criterions determines how fast he can
diagnose and repair system malfunctions.  As a professional, he has to
pick and use tools matching his needs.

As the title of his article suggests it “grepping logs is terrible”
but it necessarily is, for **grep** is a generic tool which can be
used in many other contexts than log exploration!  Any specialized
research tool *has* to be better than **grep**, otherwise what would
be the point of using it?  Most Unix users are not system
administrators or do not have requirements similar to our author, and
they *occasionally* need to interact with the logs.  For these users,
being able to interact with the logs using *generic tools they
possibly already know* is more convenient than having to use a random
dedicated command. And even if by any wonder they had hundreds of
gigabytes to analyze one time, it would be acceptable to do this in
half an hour with **grep** rather than in a few minutes minutes with a
specialized tool because this is a one-time operation.

Our author is therefore *obviously right* in his statement, as he
merely observes that specialized tools perform better than generic
tools.

The promise made by Unix systems to represent as many data as possible
as *text files* implies that the user who learns the generalist tools
to analyze and transform text[^3] will be able to use them on any kind of
data, because after all, these are text. This is also the promise that
time spent to learn these tools is time well spent, because they can
be used in a variety of contexts, not only the context which triggered
the need to learn them.  These tools are generic and are incredibly
useful at prototyping systems, which can then be implemented in a more
robust or efficient manner.  The scenario where the system
administrator observes that his system has reached a scale where
**grep** does not perform well enough to let him do his work
efficiently is just an occurrence of a prototype needing a perennial
implementation.  And *this* is the Unix way, which has nothing to do
with a silly war between fanatical *text* and *binary* factions, which
unfortunately [caught our author under its fire](http://asylum.madhouse-project.org/blog/2015/05/07/grepping-logs-is-still-terrible/).


  [^1]:
    A site similar to [Slashdot](https://slashdot.org) for French speakers.

  [^2]:
    I did not spend much time tying to remove this lay of indeterminacy!

  [^3]:
    Let us mention **grep**, **sed**, **awk**, **sort**, **join**
    and **paste** as some of the most important.
