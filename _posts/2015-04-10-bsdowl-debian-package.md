---
title: Debian and Ubuntu packaging for BSD Owl Scripts users
layout: post
category: make
tagline: "The (n+1)-th guide to Debian packaging"
tags: [bsdowl, debian, maintainer]
---
I recently wrote Debian and Ubuntu packages for
[anvil][homepage-anvil], a small software package using
[BSD Owl Scripts][homepage-bsdowl] as build system.  I documented
my work in the form of a [short document][howto-doc] and of a
[series of commits][howto-branch] in a dedicated branch of
the **anvil** repository.

You can take advantage of this documentation if you want to write
Debian or Ubuntu packages for your **git**-hosted software built with
**BSD Owl Scripts**.  Take good note that this documentation is
focused on the technical preparation of a package.

If you consider to submit your software for inclusion in Debian
repositories, you should get in touch with a mentor which will help
you to implement all the best practices described in
[Debian New Maintainers' Guide][debian-maintainer].

In contrast, you can to setup a so-called
[private package archive][homepage-ppa] to let Ubuntu users easily
install your package within minutes. Nevertheless, Debian guidelines
and processes guarantee the consistency of this distribution, which
the publication in private package archives do not.


  [homepage-anvil]:     https://github.com/michipili/anvil
  [homepage-bsdowl]:    https://github.com/michipili/bsdowl
  [homepage-ppa]:       https://launchpad.net/~michipili
  [howto-doc]:          https://github.com/michipili/bsdowl/blob/master/doc/PackageDebian.md
  [howto-branch]:       https://github.com/michipili/anvil/compare/debian
  [debian-git]:         https://wiki.debian.org/PackagingWithGit#Using_the_upstream_repo
  [debian-maintainer]:  https://www.debian.org/doc/manuals/maint-guide/index.en.html
