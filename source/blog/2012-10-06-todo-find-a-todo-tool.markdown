---
kind: article
title: "TODO: Find a todo tool"
created_at: 2012-10-06 09:05
comments: true
categories: project-argh, ruby
---

This is a follow-on from an [earlier post](/blog/2012/10/04/ordering-my-brain-with-ruby/) about getting
organised using Ruby scripts. The first step is to get some kind of system in place to managing
the things I need to remember - a Todo list tool.

<!--READMORE-->

Sourcing a Todo Tool
====================

First off, I'm not going to build if something already exists. I think that it's great that we as a
community - and I'm thinking of the larger programming community - try to solve the same problems in
different ways. It may look inefficient from an external point of view, but it's really actually the
most optimal way of solving problems - follow as many paths as possible to find the best reusable one.

But that's the topic for another post.

A quick google reveals numerous Todo list applications written in Ruby. The topic seems a perennial
favourite in Ruby coding competitions. Chances are looking good I'll find a suitable starting point for
_Argh!_

But before I start looking, some constraints. What do I want from a tool?

Requirements
============

* MUST be written in Ruby
* MUST be able to capture new todo items via the a unix shell
* MUST be able to categorise items (priority, project, focus, groupings, _etc_)
* MUST be able to manage items (list, delete, change category - meta tasks)
* MUST be simple to use

* SHOULD work across different computers

* MAY be accessible from a mobile web interface.

The last two come from the folks at [Reddit](http://reddit.com). Makes sense.

As for time contraints, I can't spend more than about 30 mins having a look - it's the weekend and there's stuff to do with the family. Here's the top hits:

Candidate Tools
===============

Public Gist from Matt Sears
---------------------------

[https://gist.github.com/1259080](https://gist.github.com/1259080)

A small, simple Todo manager. Exists as a Gist, but with a couple of forks that might be worth following. Has a basic tagging system using '@' identifiers.

Can create, delete, list _etc_ as expected. Can change priority using 'bump' command. Defers filtering on '@' identifiers to the system's grep.

**Pros**:

  * Deadly simple; Small code footprint.

**Cons**:

  * Outsourcing filtering out to grep is a great idea, but a reminder in the help wouldn't hurt.
  * Only one identifier per item (as far as I can tell)

**Examples:**

{:.language-text}
    # Add todo items
    $ todo add Check out rubyrags.com
    Add: (1) Check out rubyrags.com

    # Create a todo with context
    $ todo add Buy Duck Typing shirt from rubyrags.com @work
    Add: (2) Buy Duck Typing shirt from rubyrags.com @work

    $ todo add Buy Ruby Nerd shirt from rubyrags.com
    Add: (3) Buy Ruby Nerd shirt from rubyrags.com

    # List todo items
    $ todo list
    1. Check out rubyrags.com
    2: Buy Ruby Nerd shirt from rubyrags.com       @work
    3: Buy Duck Typing shirt from rubyrags.com     @work

    $ todo list @work
    1: Buy Ruby Nerd shirt from rubyrags.com       @work
    2: Buy Duck Typing shirt from rubyrags.com     @work

Dooby
------

[https://github.com/rafmagana/dooby]([https://github.com/rafmagana/dooby)

Interesting approach - essentially one project per directory. Has a Twitter-like tagging model; people (_@joan_, _@peter_), contexts (_#important_, _#home_) and item sets (_%fix_computer_, _%do_many_small_tasks_).

I tried installing Dooby under Ruby 1.9.3 and had a few issues. Not sure what's going on but didn't
have the time to get bogged down in debugging. The exercise is to see what's on offer, kick the tyres later.

**Pros**:

  * Quite feature-rich
  * Comes as a gem

**Cons**:

  * Not convinced by the one-project-per-directory
  * Larger code-base

**Examples**:

{:.language-text}
    # Add todo items, using @people, #context and %item_sets tags
    $ dooby add "#fix the email error in %website, check   this out with @peter #today"
    $ dooby a "learn to use the #aliases of the #commands  "
    $ dooby a "#pair with @jim on the %tickets module"

    # List todo items
    $ dooby list @peter
    $ dooby l today
    $ dooby l "#today"
    $ dooby l %website


Others
------

I wanted to review at least three, but the more I looked the more sketchy the Todo apps looked.
I also chewed up my allotted time too quickly. Still easily distracted.

I did look at the [_todo_ gem](http://rubygems.org/gems/todo), but it doesn't look like it's
been touched in a while.

There was also another command line app [Tudu](http://cli-apps.org/content/show.php/TuDu?content=129325) that
sprung up in searches. But once I started leaning out of Ruby-space it really was time to stop.

As noted before, Todo apps appear in Ruby comps fairly regularly. I might need to look further into the
results of the [CodeBrawl](http://codebrawl.com/contests/command-line-todo-lists) comp - _NB_ Matt Sears' entry came in second in that comp.

If anyone knows any code I should have a good look at let me know!

Way Forward
===========

Right now, I want to start with something simple. Matt Sears' gist looks pretty decent for a one pager (not including the tests). Dooby looks like fun but, as commented on by Bill Dueber in the previous post, the whole point of this exercise is to get things together and happening. I've love to have Dooby's multiple tags model in Matt's code though.

Neither of the two solutions I've looked at can work across computers, nor have a mobile interface. The winner of the CodeBrawl comp seems to use Github Gists as the repo model. Interesting, but not sure I'd want all my data public. Anyway, the next step is to actually get something installed, and we can book-strap from there. Onwards, forwards.


**Future post: Argh! Coding Begins**
