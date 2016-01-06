---
title: Testing complex shell programs without installing them
layout: post
category: shell
tags: [shell, programming, software-engineering]
---
A simple shell script fitting in one file can easily be tested from
the command line.  Complex scripts relying on several shell
subroutines libraries and other file assets are a bit more complicated
to test, because the file assets used by the script lie at different
locations on the file system when the script is developed and when the
script is installed.  Let us see together how we can use *parameter
expansion modifiers* to easily test our shell scripts without
installing them, and how this is actually used in a small project
[anvil][home-anvil].


## Test the scripts in a special crafted environment

We can environment variables to reconciliate the shell script's idea
of where to found its file assets when it is installed and when it is
tested as part of its development cycle.  More precisely, we can take
advantage of the *Assign Default Values* parameter expansion
modificator.  This modificator is described as follows in *sh(1)* on
**FreeBSD**:

> `${parameter:=word}`
> Assign Default Values.  If parameter is unset or null, the expan-
> sion of word is assigned to parameter.  In all cases, the final
> value of parameter is substituted.  Quoting inside word does not
> prevent field splitting or pathname expansion.  Only variables,
> not positional parameters or special parameters, can be assigned
> in this way.

Assume that our software package is called **anvil** and its source
repository contains a collection of function libraries found in the
folder `subr`, which are copied in `/usr/local/share/anvil/subr` when
the package is installed. If we write

~~~ shell
: ${subrdir:=/usr/local/share/anvil/subr}
~~~

near the top of our shell program, we can use `${subrdir}` to access
our subroutine libraries, for instance

~~~ shell
. "${subrdir}/common.sh"
~~~

In order to test our script as part of its developement cycle, we must
ensure that it reads the subroutines found in its source repository,
and not the libraries found in `/usr/local/share/anvil/subr` copied by
the installation of an older version of the software package.  For
this, it is enough to run the script in an environment where the
variable `subrdir` has been set to `./subr` or to the absolute path to
that directory.


## Configure installation paths

Instead of hard-wiring the installation in the sources, we may want to
use configuration parameters for this.  It is easy to use **autoconf**
for this, but in order to avoid running repeatedly the slow
`./configure` script it generates, we may prefer a solution based on
**autoconf** and Makefiles, so that the `./configure` script is run
once and for all and **make** is used to edit configuration parameters
in the scripts.  This is the approach used by [anvil][home-anvil],
which can be studied as an example of this technique.

  [home-anvil]:   https://github.com/michipili/anvil
