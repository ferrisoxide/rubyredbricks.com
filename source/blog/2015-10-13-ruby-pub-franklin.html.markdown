---
title: "Ruby Pub - Part 2: Franklin"
date: 2015-10-13 00:18 UTC
tags: ruby, epub
---

Ruby ePub: Franklin
=========

![Franklin Logo](/images/posts/franklin.png){: class="image right"}

For the aspiring Rubyist-cum-writer there are a number of options when it comes to self-publishing online -- ranging from Ruby-based publishing platforms like the Michael Hartl-led [Softcover](https://github.com/softcover/softcover){: target="_blank"} project, down to hand-crafted solutions (e.g. my own [earlier experiments](/blog/2013/05/06/nanoc-novel/){: target="_blank"}). And if you don't need to own the whole pipeline, [Gitbook](https://www.gitbook.com/){: target="_blank"} and [Leanpub](https://leanpub.com/){: target="_blank"} offer comprehensive toolchains to help get your book out and into the hands of the reading public.

In fact, there's probably not been a better time to be writing a self-published book than right now. But with all the competition between publishing platforms it's natural that some solutions don't get the attention they deserve and fall by the way side.

Sadly, this was the case with Bryan Braun's [Bitbooks service](https://web.archive.org/web/20150915034729/http://bitbooks.cc/){: target="_blank"} [archived link]. Earlier this year Bryan announced that, as of October 2015, his site would be shutting down. Thankfully [Franklin](https://github.com/bryanbraun/franklin){: target="_blank"}, the open-source mechanism for generating content through **Bitbooks**, remains available and presents a simple and elegant solution for self-publishing authors wanting to get their work online.

We'll take a quick look at the **Markdown**-friendly **Franklin** tool in a moment, but before we do let's hear from Bryan himself - from an interview conducted shortly before his site closed down.

<hr/>

Interview with Bryan Braun
-------------------

***You chose Middleman as the engine for Franklin. What made you pick this static site generator tool over similar tools like [Jekyll](http://jekyllrb.com/){: target="_blank"} and [nanoc](http://nanoc.ws/){: target="_blank"}?***

I looked into Jekyll when I was getting started but at the time it relied on front-matter and filename conventions for defining the urls and templates being used. That was a non-starter for me. My goal was that people could just write markdown and the tool would take care of the rest. I didn't want them to have to worry about using a special "Franklin" format, or front-matter convention.

Eventually I discovered [Middleman](https://middlemanapp.com/){: target="_blank"}, which was a great fit. It was flexible enough for me to configure all the default behaviors in a config.rb file. Also, it provided interfaces for extensions and project templates, which allowed for a clean way to add customizations and release parts (like Franklin) as open source.

***Are there any publishing success stories from Bitbooks you'd like to share?***

I enjoyed seeing the new books people were writing with Bitbooks (like [Hands on Visual Regression Testing](http://www.kevinlamping.com/hands-on-visual-regression-testing/){: target="_blank"}, for example). However, my favorite experience was seeing content once locked in PDFs being liberated as HTML, where it could be referenced, linked to, and made available to search engines and screen readers. John Wiegley's ["Git From the Bottom Up"](https://jwiegley.github.io/git-from-the-bottom-up/){: target="_blank"}, was a good success story in that regard.

***What's your motivation for mothballing Bitbooks? And what impact will this have on your existing users?***

I saw other organizations beginning to deliver on my vision for Bitbooks, and once I saw that I wasn't going to be able to compete with them, my efforts subsided significantly. Bitbooks was designed to be low maintenance, but I knew that focusing on other things while leaving Bitbooks in limbo wasn't fair to users.

Also, by splitting my attention, I was doing several things poorly, which is against my principles. Anything worth doing, is worth doing to the best of your ability, and for me that means committing 100%. I couldn't do that for Bitbooks anymore.

Fortunately, this choice had minimal impact on Bitbooks users. Everybody's books were hosted on Github pages, meaning that nobody lost their content, and all books remained live at their original URLs.

***What do you hope for the future of Franklin?***

I hope Franklin continues to be the best solution for building ebooks with Middleman. It has several strengths, including a separation of book metadata from content, support for pure markdown documents, and a theme structure that provides full stylistic control. I intend to continue supporting it, and I expect people to find it valuable for years to come.

<hr/>

Franklin - A Quick Peek
-----

First off, what I really like about Franklin is it's **Middleman** base. **Franklin** works as a **Middleman** template, and takes almost no time to set up. The complete setup instructions are available on [Franklin's Github page](https://github.com/bryanbraun/franklin){: target="_blank"}, but a short version is as follows:

{:.language-bash}
    gem install middleman
    mkdir -p ~/.middleman  # ensure Middleman's template folder exists
    git clone git@github.com:bryanbraun/franklin.git ~/.middleman/franklin
    middleman init mysite --template=franklin
    cd mysite
    bundle install

These steps generate a basic **Franklin** project, including a default `index.md` page, several sample pages and a default 404 page. A fairly standard **Middleman** `config.rb` and the **Franklin**-specific `data/book.yml` and `data/tree.yml` files will also be created.

Nothing in the **Franklin** configuration files will confuse you. Each item in the `book.yml` file (_e.g._ `title`, `author`, `github_url`, _etc_) is straightforward and easy to understand. Similarly, `tree.yml` is a simple list of all the sections of your ebook.

_Example tree.yml_
{:.language-yaml}
    ---
    index.md: "/index.md"
    section-1.md: "/section-1.md"
    section-2.md: "/section-2.md"

As a side note, you don't actually have to build the `tree.yml` file yourself. As long as you have an `index.md` file for the front page of your book -- and you order your pages alphabetically (`01-page.md`, `02-page.md`, _etc_) -- the sequencing of pages happens automatically.

All the content is managed in the [Markdown](http://daringfireball.net/projects/markdown/){: target="_blank"} format, so if you're coming from a **Jekyll** or **nanoc** background you'll find this comfortable. **Franklin** uses [Redcarpet](https://github.com/vmg/redcarpet){: target="_blank"} out of the box, but swapping to [Kramdown](http://kramdown.gettalong.org/){: target="_blank"} or a similar formatting mechanism should be a simple matter of changing the Gemfile and rebundling.

Converting Existing eBooks
----

The [Tachypomp project](https://github.com/ferrisoxide/tachypomp){: target="_blank"} is a good test for converting an existing ebook project to **Franklin**. Based on **nanoc**, the original **Tachypomp Project** could produce content in the [Baker Framework](http://bakerframework.com/){: target="_blank"}'s hPub format - at the cost of considerable customisation. While this will be missed, being able to quickly set up and build an HTML-base ebook using **Middleman** and **Franklin** more than makes up for it.

Converting any existing Jekyll or nanoc ebook should be relatively straightforward, providing the content is in a fairly standard Markdown format. This was certainly the case for **Tachypomp**, and only required copying the markdown files from `/content` to `/source`. **Jekyll** and **nanoc** share similar conventions for front-matter contents (the YAML header for each content source file). **Tachypomp** only uses `title` in the front-matter configuration, so no changes were necessary.

**Tachypomp** already had an index file, due to the `book.json` file required by the Baker Framework -- readily converted to  the **Franklin's** `tree.yml` structure.

Themes
---

**Franklin** comes with three built in themes: _Hamilton_ - a serif-font style with drop down table of contents; _Epsilon_ - a more futuristic looking theme with a pop out sidebar navigation; and _Glide_ - a responsive theme using a clean sans font set.

_Example theme: Epsilon_

![Epsilon Theme](/images/posts/epsilon-theme.png){: width="60%"}

For **Tachypomp** I opted for the _Glide_ theme for that nice responsive styling. Changing themes is trivial - just modify the `theme:` setting in `/data/book.yml` and run `middleman build`.

Hosting on Github
---

This is where using **Middleman** as the basis for **Franklin** shines. Thanks to the [Middleman Deploy](https://github.com/middleman-contrib/middleman-deploy){: target="_blank"} extension, deploying to a remote site only takes a few minutes.

I'm using [Github Pages](https://pages.github.com/){: target="_blank"} to host **Tachypomp**. Apart from adding `gem 'middleman-deploy', '~> 1.0'` to the `Gemfile` the main configuration changes required are settings in the **Middleman** `config.rb`.

_Example config.rb settings_
{:.language-ruby}
    activate :deploy do |deploy|
      deploy.method = :git
      # remote is optional (default is "origin")
      # run `git remote -v` to see a list of possible remotes
      deploy.remote = "origin"

      # branch is optional (default is "gh-pages")
      # run `git branch -a` to see a list of possible branches
      deploy.branch = "gh-pages"

      # strategy is optional (default is :force_push)
      deploy.strategy = :force_push
      deploy.build_before = true
    end

You don't need to worry about creating a `gh-pages` branch as **Middleman Deploy** takes care of this automatically. The only other steps required are to create an CNAME record with your domain provider and point it to your account on Github. For my project the CNAME record for `tachypomp.rubyredbricks.com` points to `ferrisoxide.github.io`.

This assumes you are using a subdomain for your book. You can use apex domains, but this will require creating two `A` records for your book's site and pointing them to `192.30.252.153` and `192.30.252.154`. If in doubt, refer to the Github Pages documentation for [setting up custom domains](https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages/){: target="_blank"}.

One note not related to **Franklin** but has caught me up before. You will need to add your `CNAME` file to the `source` folder and commit it. The CNAME file contains a single line - the URI to your book's site (_e.g._ `tachypomp.rubyredbricks.com`). You need to do this even if you are using A records, and the file still needs to be called `CNAME`.

This is more an issue with deploying **Middleman** projects to **Github Pages**. I have forgotten to do this a couple of times and been puzzled over where my site disappeared to.

**Middleman Deploy** provides mechanisms for other deployment models (rsync, FTP, _etc_), but given the simplicity of **Github Pages** (and the cost - free) it seems the best option for managing your ebook's site.

If you are interested, the **Franklin**-generated ebook is available at [http://tachypomp.rubyredbricks.com/](http://tachypomp.rubyredbricks.com/){: target="_blank"}. It needs some cleaning up, but it's not bad for 10-15 minutues of work.

Final Thoughts
---

**Tachypomp** only contains text, so it's not a complete test of **Franklin's** features. But given it took less that 15 minutes to go from my older **nanoc**-based book to a working, online version it's reasonable to assume that more complex texts would not require an order of magnitude more work.

The ability to leverage existing **Middleman** extensions is a huge plus.

All-in-all **Franklin** (plus Github Pages) is a simple and inexpensive way to develop and deliver your ebook. Being able to deploy to other formats (e.g PDF) and having a way to control access would be good if you are aiming to monetise your book, but if the goal is to "get it out there" **Franklin** seems a reasonable approach.
