---
title: Delegating complex treatments to filters in shell programs
layout: post
category: shell
tags: [shell, programming, software-engineering]
---
Novice shell programmers tend to reproduce procedural structures they
learnt from classical procedural languages like Pascal or C[^1].  While
it produces results, this approach is catastrophic and complex
treatments should be delegated to filters.  I will first convince you
that _catastrophic_ is not as much of an hyperbole as it may seem and
discuss a simple concrete example from a code review I recently made.


## Example presentation

I reviewed a submission for **opam**, the package manager recently
adopted by the OCaml community, and spent quite a time to comment on a
code snippet which confronts the list of compilers supported by the
system with a list of compiler versions that are to be kept, the
purpose of the script being to remove the remaining ones.

Each supported compiler is represented on the filesystem by a
directory, whose path relative to *${OPAMROOT}* — the path to data
owned by **opam** has — the following structure:

~~~
compilers/${SERIES}/${VERSION}
~~~

So for instance, the directory `compilers/4.02.1/4.02.1+PIC`
corresponds to the compiler `4.02.1+PIC` in the `4.02.1` series.

The problem solved by the snippet I reviewed performs a rather
straightforward treatment: given a list of compiler versions held by
the variable *COMPILER_VERSIONS* it removes from the file-system the
compilers whose version is not listed in *COMPILER_VERSIONS*.


## Mimicking the classic procedural approach

The classic procedural approach to solving this problem can be worded
“consider each compiler, if it is not in my little list, then delete
it.”  This can be implemented like this in the shell:

~~~ shell
is_in_compiler_versions()
{
  local version
  for version in ${COMPILER_VERSIONS}; do
      if [ ${version} = $1 ]; then return 0; fi
  done
  return 1
}

for compiler in compilers/*/*; do
    if !is_in_compiler_versions "${compiler##compilers/*/}"; then
        rm -r -f "${compiler}"
    fi
done
~~~

There is nothing terribly surprising here and it definitely works, so
why should we consider this approach *catastrophic*?  Here are a few
reasons:

1. The code is hard to read, there is no function name advertising the
   purpose of the main treatment and this purpose is well hidden in
   a conditional in the body of a for-loop.

2. The code is hard to debug, since of the three important data sets
   involved in this treatment, only the list *COMPILER_VERSIONS* can
   be easily examined by the maintainance programmer.  The list of
   supported compilers and the list of compilers to remove form the
   file-system exist only in an evanescent manner in this code and
   cannot be easily examined.

3. The code is hard to reuse, because the enumeration of the compilers
   to remove and the actual removing are tightly bound together.

To put this in a few words, mimicking the classical procedural approach
led to a code which is hard to read, hard to debug and hard to reuse.
Maybe labeling this *catastrophic* was not an exaggeration, after all.
And we did not even consider execution speed, the shell being rather
slow, that kind of code performs poorly when it has to handle a lot of
data.

The example itself is of course really innocent but things go worse
when we consider more complicated treatments, and larger programs.
Now, what can we do about this?  We can opt for


## Delegating complex treatments to filters

Understanding that complex treatments should not be performed by the
shell itself but delegated to filters is probably the most important
perspective shift required to program the shell properly.  Here is how
we can rewrite the previous snippet using filter.

~~~ shell
find_compilers()
{
  find "compilers" -type d -depth 2
}

select_not_in()
{
    awk -F '/' -v filter_out_list="$1" '
BEGIN {
  split(filter_out_list, s, " ")
  for(i in s){
    filter_out[s[i]]
  }
}
!($3 in filter_out) {print}
'
}

find_compilers\
  | select_not_in "${COMPILER_VERSIONS}"\
  | xargs rm -r -f
~~~

This solves all the problems found before.  The code is *easy to
read* because the function names make their purpose obvious.  We do
not need to understand **awk** to guess what the filter
`select_not_in` does, since it is pretty clear from its name.  Using
**awk** here is essentially irrelevant, any language can be used to
perform this selection step.  It is very easy to scan the code down to
the end of the pipeline to see that the purpose of the pipeline is to
remove some files.  The code is also *easy to debug* because the
maintainance programmer can break the pipe sequence anywhere to
examine the output of the program at that point, insert tees, insert a
filter to pause between each line or mock the input of the filter.
This code is *easy to reuse* because each of the three steps
are independent.  Last, it is way faster than the previous program and
starts much less processes.


## Consequences for code organisation

Once we have understood the benefits of delegating complex treatments
to filters, we can draw a few consequences for the organisation of our
programs and how we should shape our competences.

1. As a rule of thumb, shell variables should not contain any complex
   data[^2] and should contain only variables from the Unix world, that
   is, paths in the filesystem and PIDs.  Everything else is stored in
   files or flaws from one process to the other through a pipe.

2. It is crucial to know well a tool which we can use to quickly write
   these filters.  I am very pleased with **sed** and **awk** as the
   versions in BSD systems are quite lightweight in comparison to some
   others, but there is a lot of reasonable choices here.



  [^1]:
    Yes I wrote _Pascal or C._  This is a bit old-school, I know.

  [^2]:
    Are `base64`-encoded files complex data?
