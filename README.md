# Documentation README

## Introduction

This document contains information on how the Akachain documentation is
built and published as well as a few conventions one should be aware of
before making changes to the doc

The crux of the documentation is written in
[reStructuredText](http://docutils.sourceforge.net/rst.html) which is
converted to HTML using [Sphinx](http://www.sphinx-doc.org/en/stable/).
The HTML is then published on http://akachain.readthedocs.io
which has a hook so that any new content that goes into `docs/source`
on the main repository will trigger a new build and publication of the
doc.

## Conventions

* Source files are in RST format and found in the `docs/source` directory.
* The main entry point is index.rst, so to add something into the Table
  of Contents you would simply modify that file in the same manner as
  all of the other topics. It's very self-explanatory once you look at
  it.
* Relative links should be used whenever possible. The preferred
  syntax for this is: :doc:\`anchor text &lt;relativepath&gt;\`
  <br/>Do not put the .rst suffix at the end of the filepath.

Notes: The above means we have a dependency on the github mirror
repository. Relative links are unfortunately not working on github
when browsing through a RST file.

## Setup
