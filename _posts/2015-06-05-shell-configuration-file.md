---
title: Configuration files for shell scripts
layout: post
category: shell
tagline: "Read INI files in your shell scripts"
tags: [shell, programming]
---
Sometimes it is convenient to pass arguments to a program using
configuration files rather than command line arguments.  Using a
sourced file in shell scripts is a popular approach because it is very
easy to implement, however it has two flaws:

  1. It is not very easy to validate against malicious input, which
     might be a concern in some contexts.

  2. It is not very structured, for it does not offer any way to
     structure information.

In the last years[^1] a style of configuration files coming from the
Microsoft Windows world has gained popularity on Unix platforms.  This
style of file is known has **INI** files, and Wikipedia wants us to
believe that they look like this:

~~~ ini
# last modified 1 April 2001 by John Doe
[owner]
name=John Doe
organization=Acme Widgets Inc.

[database]
# use IP address in case network name resolution is not working
server=192.0.2.62
port=143
file=payroll.dat
~~~

Can we use such files to store configuration values for shell scripts,
without relying on any fancy dependency?  For sure, here is a **sed**
script which converts these files to a tabular format, like:

~~~
owner|name|John Doe
owner|organization|Acme Widgets Inc.
database|server|192.0.2.62
database|port|143
database|file|payroll.dat
~~~

The **sed** script follows:

~~~ sed
# Configuration bindings found outside any section are given to
# to the default section.
1 {
  x
  s/^/default/
  x
}

# Lines starting with a #-character are comments.
/^#/n

# Sections are unpacked and stored in the hold space.
/^\[/ {
  s/\[\(.*\)\]/\1/
  x
  b
}

# Bindings are unpacked and decorated with the section
# they belong to, before being printed.
/=/ {
  s/^[[:space:]]*//
  s/[[:space:]]*=[[:space:]]*/|/
  G
  s/\(.*\)\n\(.*\)/\2|\1/
  p
}
~~~

It is then easy to extract interestign values from the output of the
script with **awk** or **read**.

Of course, the script is not very robust and does not support fancy
features of other implementations, like arguments spreading on
multiple lines or enclosed between double quotes.  It nevertheless
mitigates risks bound to malicious input as it does not provide a way
to execute arbitrary code as the script and can be extended to support
more advanced use cases.


  [^1]: Maybe it is for a decade, actually.
