<!--
    This Source Code Form is subject to the terms of the Mozilla Public
    License, v. 2.0. If a copy of the MPL was not distributed with this
    file, You can obtain one at http://mozilla.org/MPL/2.0/.
-->

<!--
    Copyright 2017 Joyent
-->

# Requests for Enhancement

[Requests for Discussion](https://github.com/joyent/rfd) have allowed for
technical thinking to be formally written down over a wide range of subjects.
Their broad subject matter is not accidental: RFDs are intentionally informal
in their subject and structure, with the belief that it is better to write
things down than to refuse them in the name of a foolish consistency.  That
said, a subcategory has emerged that merits a distinction: RFDs that don't
really discuss *how something is designed* but rather *what problem should be
solved*.  Rather than describing the concrete plans on how something will be
implemented, these are often subjective, representing (to a degree) the
priorities of the author(s):  the problem is important enough that it merits
being written down -- but not (yet) at the point where the architecture has
been seriously considered.  As such, these documents are not requests for
discussion on detailed design but rather *requests for enhancement* (RFEs).  

RFEs are described in more detail in
[RFD 102](https://github.com/joyent/rfd/tree/master/rfd/0102/README.md);
this repository contains their formalized embodiments.  They are in the
spirit of an RFD (and mechanically similar, though without any discussion of
design or implementation) but thought of, conceived of, and catalogued
separately.  

## RFEs

| state    | RFE |
| -------- | ------------------------------------------------------------- |
| predraft | [RFE 1 The Router Network Object for Triton](./rfe/0001/README.md) |
| predraft | [RFE 2 The Remote Network Object](./rfe/0002/README.md) |

## Contents of an RFE

An RFE is the request for additional functionality to the system, with the
emphasis on requirements rather than design -- it is the "what" than the
"how".  RFEs should almost always result in the addition of user- or
operator-visible abstraction, with the exception being an existing abstraction
that is to be enhanced in some quantifiable way.  A good RFE needs to answer
two questions:  what problem needs to be solved (and why?) and what defines
success?

### "What needs to be solved?"

There is an art to describing the problem that needs to be solved:  too
vague, and one runs the risk of putative solutions that don't actually
solve the problem; too specific and the problem description drifts into
a solution description.  Ideally, the problem description should be 
abstract and yet still sufficiently concrete to constrain (but not overly
constrain!) solutions.

### "Why?"

Engineers are problem solvers -- and understanding _why_ a problem needs to be
solved very much helps to understand the implications in terms of what needs
to be solved.  Sometimes the "why" of a problem only helps to motivate its
solution (or the engineer providing it!), but in some cases the "why" can shape
the solution significantly -- or imply other problems that need to be solved.

### "What defines success?"

It's important to know the least that can be done to be considered
successful -- not because engineers are underachievers, of course, but
rather to allow for an iterative course to be plotted.  This is an exercise
in defining the [minimum viable
product](https://en.wikipedia.org/wiki/Minimum_viable_product):  at what
point is the problem considered to be sufficiently solved to at least allow
further feedback?  Alternatively, it may be that emphasis is not on
"minimum" but "viable":  many pieces of software infrastructure require
significant work to render something that is at all usable, and the emphasis
may be on the high level of functionality required to meet even the
lowest of expectations.

## Mechanics of an RFE

To create a new RFE, you should do the following steps.

### Allocate a new RFE number

Like RFDs, RFEs are numbered starting at 1, and then increase from there.
When you start, you should allocate the next currently unused number. Note
that if someone puts back to the repository before you, then you should just
increase your number to the next available one. So, if the next RFE would be
number 42, then you should make the directory 0042 and place it in the file
0042.md. Note, that while we use four digits in the directories and
numbering, when referring to an RFE, you do not need to use the leading
zeros.

```
$ mkdir -p rfd/0042
$ cp prototypes/prototype.md rfd/0042/README.md
$
```

### Write the RFE

At this point, you should write up the RFE. Any files that end in `*.md`
will automatically be rendered into HTML and any other assets in that
directory will automatically be copied into the output directory.

To make them readable in a normal text editor, RFEs should have a default
text width of 80 characters. Any other materials related to that RFE should
be in the same directory.

#### RFE Metadata and State

The start of every RFE document needs a small amount of metadata based on the
[python-markdown2](https://github.com/trentm/python-markdown2/wiki/metadata)
metadata format, e.g.:


```
---
authors: Jared Dunn <jared@pp.com>, Nelson Bighetti <bighead@cs.stanford.edu>
state: draft
---
```

RFEs should have two pieces of metadata.  The first is the `authors`, 
who should be listed with their name and e-mail address.

The second is the `state`, which should be one of the following:

1. predraft
1. draft
1. publish
1. abandoned

While an RFE is in the `predraft` state, it indicates that the work is not
yet ready for serious contemplation, and the RFE is effectively a
placeholder.

RFEs being actively written should be in the `draft` state.  RFEs that
are in the `draft` state should have an issue open to capture any
questions. This issue should have the synopsis that indicates the RFE
number and that its purpose is to discuss it (e.g., "RFE 42: Discussion").
When this is done, it is helpful to add metadata to the RFE that contains
a link to a search for the issue:

```
discussion: https://github.com/joyent/rfd/issues?q=%22RFE+42%22
```

Once (or if) the RFE has converged, it should be updated to the `publish`
state and this issue should be closed.  (Note that just because something
is in the `publish` state does not mean that it cannot be updated and
corrected.)

If an RFE becomes no longer desired or is otherwise obviated or meaningless,
it should be moved into the `abandoned` state.

## Contributing

Contributions are welcome, you do not have to be a Joyent employee to
submit an RFE or to comment on one.  The discussions for RFEs can occur
anywhere, but generally happen on the issues open for RFEs not yet in the
`publish` state.

