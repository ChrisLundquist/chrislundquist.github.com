---
layout: post
title: "Small steps"
category: Development
tags: [Coding]
---
{% include JB/setup %}

*Premature optimization and extension can cause many more problems than gains.*

  It is often the case where we try to extend or improve things before that are
complete. This usually leads to the manifestation of many bugs. The root cause
of this is *taking too many or too large of steps at a time*.

One way to tell when this is happening, is when you start writing many different
functions at a time, but aren't able to finish any of them. My strategy here is
to find the separation of concerns and establish the minimum amount of work I
need to do to accomplish this step. The three C's of computer science can
be an excellent guide.

* Correctness
- Having a ugly function that works correctly, is better than a shorter one
that has bugs.

* Completeness
- If you don't account for half of your cases, then it is an obvious bug.

* Compactness
- It is a fairly common saying, something isn't done when there is nothing left
to add. It is done when there is nothing left to take away.

The order of these guidelines is very important. You first need to do something
correctly before it is time to extend it all the way. Likewise, an empty function
is very compact, but not complete.

