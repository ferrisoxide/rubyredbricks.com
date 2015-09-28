---
kind: article
title: "Getting started with taco, more links"
created_at: 2012-10-07 11:54
comments: true
categories: project-argh, ruby
---

I've been in touch with Matt Sears, of the [Matt Sears Todo.rb Gist](https://gist.github.com/1259080) fame, he tells me that he's been working on [taco](https://github.com/mattsears/taco) - a gem-ified version of todo.rb with tweaks and extra features.

<!--READMORE-->

Taco looks pretty neat. From the code, it looks like Matt's is moving towards alternative repos for taco's list - which will help with the cross-computer, mobile access requirements [listed earlier](blog/2012/10/06/todo-find-a-todo-tool/). But the basics are all there.

Installing taco
===============

The taco site recommends installing as a gem:

{:.language-text}
    gem install taco


I couldn't get this to work, as it seemed to pull in an older version. I've reported it back to Matt, though it might be something stupid in my RVM setup. I don't feel in total control of RVM yet, but I'm learning (I am but a humble manager).

Installing from repo worked:

{:.language-text}
    git clone git://github.com/mattsears/taco.git
    cd taco
    bundle install
    rake build
    rake test
    rake install


As did adding it to a _Gemfile_:

{:.language-ruby}
    source "http://rubygems.org"
    gem 'taco', :git => 'git://github.com/mattsears/taco.git'




Using taco
==========

Once installed, you can query taco directly, without any parameters:

{:.language-text}
    $ taco

    TACOS:
    --------------------------
    [1] Add an item!     empty
    [2] Add an item!     empty

Help is in the logical place:

{:.language-text}
    $ taco help

    Usage: taco [options] [command]
    Taco holds stuff in a shell

    Commands:
      add       Adds a new item
      del       Removes an item
      list      Prints all items
      init      Bootstraps tacos in this directory
      bump      Moves an item to the top of list
      settings  Moves an item to the top of list

    Options:
        -h, --help                       Display this screen
        -v, --version                    Display the current version

Dupe in the 'settings' description there, but these are minor. Taco has what I expect from an app: sensible defaults and some form of navigation/support. No mention of the '@' tagging though, _e.g._

{:.language-text}
    taco add tidy desk @work

All the commands operate as you'd expect, though there are some quirks.

Taco automatically generates two empty items within your list:

{:.language-text}
    $ taco

    TACOS:
    --------------------------
    [1] Add an item!     empty
    [2] Add an item!     empty

Add a new items wipes out one of the placeholder items:

{:.language-text}
    $ taco add have breakfast
    Added: have breakfast: @

    TACOS:
    ----------------------------
    [1] Add an item!       empty
    [2] have breakfast

But doing it a second time doesn't:

{:.language-text}
    $ taco add organise brain
    Added: organise brain: @

    TACOS:
    ----------------------------
    [1] Add an item!       empty
    [2] have breakfast
    [3] organise brain


Minor. And easy to snip out:

{:.language-text}
    $ taco delete 1
    Deleted: Add an item!: @empty

    TACOS:
    -----------------------
    [1] have breakfast
    [2] organise brain

The _bump_ and _list_ commands are fairly self evident. There is a neat _done_ command to mark
tasks as complete, though it didn't work exactly as expected:

{:.language-text}
    $taco done have breakfast
    Done: organise brain: @done

    TACOS:
    ---------------------------
    [1] have breakfast
    [2] organise brain     done

OK, my bad - you have to use indexes throughout. But it's not responding to garbage input. Minor, minor. The correct way to mark an item as done - or delete/bump it - is to use the item's index:

{:.language-text}
    $taco done 1
    Done: have breakfast: @done

    TACOS:
    ---------------------------
    [1] have breakfast     done
    [2] organise brain


Taco's repo is in _$HOME/.tacos.yml_, so you can access your lists in any dir.

There's also a _$HOME/.tacorc_ file for storing configuration data:

{:.language-yaml}
    storage: 'yaml'
    foo: 'bar'
    1: '1'

I think I understand what the *storage:* property is for, but the rest look like some kind of index. I'm sure will be revealed as I get into the code.

Other links
===========

Some useful [Reddit comments](http://www.reddit.com/r/ruby/comments/111bzs/todo_1_find_a_todo_tool_bootstrapping_lifehacking/):

[metamorfos](http://www.reddit.com/user/metamorfos) points us to us his [rubdo project on Github](http://github.com/metamorfos/rubdo). Use of Dropbox a great idea.

[seyday](http://www.reddit.com/user/seydar) points us to [ditz on Rubyforge](http://ditz.rubyforge.org/). Heavier tool than what I need right now, but one to come back to.

That's it. Spent enough time on this over the weekend and now it's time for the family.

Cheers folks
-Tom
