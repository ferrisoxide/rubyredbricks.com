---
title: "Ruby Pub - Part 2: Franklin"
date: 2015-10-13 00:18 UTC
tags: ruby, epub
---

Ruby ePub: Franklin
=========

![Franklin Logo](/images/posts/franklin.png){: class="image right"}

Continuing from an [earlier post](/blog/2013/10/09/ruby-pub-part-1-toolchains/){: target="_blank"} examining Ruby-based e-Publishing systems, this article looks at **Franklin** - the toolkit used to generate content for Bryan Braun's "Bitbooks" service.

The timing could perhaps have been better, with Bryan announcing that as of October 8th, 2015 the [Bitbooks site](https://web.archive.org/web/20150915034729/http://bitbooks.cc/){: target="_blank"} [archived link] will be closing down. I managed to talk to Bryan shortly before his service shutting off. Thankfully Bryan assures us the **Franklin** source code will remain [available on Github](https://github.com/bryanbraun/franklin){: target="_blank"} and he will continue offering support for **Franklin**.

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

Setting Up Franklin
-----

**Franklin** works as a template for the [Middleman](https://middlemanapp.com/){: target="_blank"} static site generation tool. The complete setup instructions are available on [Franklin's Github page](https://github.com/bryanbraun/franklin){: target="_blank"}, but a shortend version is as follows:

{:.language-bash}
    gem install middleman
    mkdir -p ~/.middleman  # ensure Middleman's template folder exists
    git clone git@github.com:bryanbraun/franklin.git ~/.middleman/franklin
    middleman init mysite --template=franklin
    cd mysite
    bundle install

These steps generate a basic **Franklin** project, including a default `index.md` page, several sample pages and a default 404 page.

The generated `config.rb` file contains fairly standard **Middleman** configuration data, as well as helpers specifically for **Franklin**. You probably won't need to modify this file at all, unless it's to add extra **Middleman** extensions.

The ebook-specific configuration files are located in the template-generated `/data` folder, with `/data/book.yml` containing metadata for your book. Again, details on how to configure these are available on the Github pages, but the purpose of each configuration item (_e.g._ `title`, `author`, `github_url`, _etc_) is straightforward and easy to understand.

The actual contents for your book are defined in `/data/tree.yml`. There is no convention for setting up the index for your book -- _i.e._ the name of each page doesn't imply an order -- but again the process for setting out your index is relatively straightfoward.

_Example tree.yml_
{:.language-yaml}
    ---
    index.md: "/index.md"
    section-1.md: "/section-1.md"
    section-2.md: "/section-2.md"

This could become laborious if you have a large number of pages to manage. Having a command line mechanism to insert new pages (or even create new pages) would be a nice thing to have, but any additions to the available **Middleman** command line options have to be done through custom extensions. Converting the **Franklin** template to a **Middleman** extension would be a major rewrite.

As such, the template approach works well. The trade off is doing some of the content management (creating new pages, looking after the index) by hand.

Some interesting 'gotchas'. The **Franklin**-generated `Gemfile` uses `'http://rubygems.org'` as the rubygems source, rather than the now default `https://..`. I assume this was to support Windows, or environments without OpenSSL installed.

**Franklin** also uses an older version of the **Middleman** gem (`~> 3.3.2`). It's probably a good idea to change the `Gemfile` to use `https` and use a more current version of Middleman (_e.g._ `~> 3.4.0`). Doing either works fine and won't affect Franklin's operation.

A note for OS X users: the default version of [Eventmachine](https://github.com/eventmachine/eventmachine/){: target="_blank"} used by **Middleman** (version 1.0.3) is incompatible with Yosemite and later. You may need to run `bundle update` after installing to bring **Eventmachine** up to version 1.0.8 or later.

Converting Existing eBooks
----

Earlier efforts at [producing an ebook using Ruby](/blog/2013/05/06/nanoc-novel/){: target="_blank"} relied on **nanoc** to do the heavy lifting. The resulting [Tachypomp project](https://github.com/ferrisoxide/tachypomp){: target="_blank"} could produce content in the [Baker Framework](http://bakerframework.com/){: target="_blank"}'s hPub format - at the cost of considerable customisation. While this will be missed, being able to quickly set up and build an HTML-base ebook using **Middleman** and **Franklin** more than makes up for it.

Converting any existing Jekyll or nanoc ebook should be relatively straightforward, providing the content is in a fairly standard Markdown format. This was certainly the case for **Tachypomp**, and only required copying the markdown files from `/content` to `/source`. **Jekyll** and **nanoc** share similar conventions for front-matter contents (the YAML header for each content source file). **Tachypomp** only uses `title` in the front-matter configuration, so no changes were necessary.

**Tachypomp** already had an index file, due to the `book.json` file required by the Baker Framework. Converting this to **Franklin's** `tree.yml` structure took no time, but if I didn't have this I could have piped `ls` to a text editor and modified the output accordingly. If a book already has a regular page naming convention (e.g. `page-1.md`, `page-2.md`, _etc_) a trick like this should suffice.

Probably the only thing I would change would be using a different Markdown processor, as [redcarpet](https://github.com/vmg/redcarpet){: target="_blank"} doesn't support all the features I'm after. Previously I used [kramdown](http://kramdown.gettalong.org/){: target="_blank"}, and swapping to this should be a simple matter of changing the `Gemfile` and rebundling.

Themes
---

**Franklin** comes with three built in themes: _Hamilton_ - a serif-font style with drop down table of content; _Epsilon_ - a more futuristic looking theme with a pop out sidebar navigation; and _Glide_ - a responsive theme using a clean sans font set.

_Example theme: Epsilon_

![Epsilon Theme](/images/posts/epsilon-theme.png){: width="60%"}

For **Tachypomp** I opted for the _Glide_ theme, but adding a new theme in the future shouldn't be too onerous. Modifying an existing theme or adding a new one involves creating a new **Middleman** `layout.erb` and supplying the required javascript and stylesheets.

Changing themes is trivial - just modify the `theme:` setting in `/data/book.yml` and run `middleman build`.

Hosting on Github
---

While not part of **Franklin** or **Middleman** specifically, ultimately the ebook needs to be hosted somewhere. Thanks to the [Middleman Deploy](https://github.com/middleman-contrib/middleman-deploy){: target="_blank"} extension, deploying to a remote site only takes a few minutes.

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

**Tachypomp** only contains text, so it's perhaps not the best test of **Franklin's** features. But given it took less that 15 minutes to go from my older **nanoc**-based book to a working, online version it's reasonable to assume that more complex texts would not require an order of magnitude more work.

As noted earlier, one "nice to have" would be a command line mechanism for managing content. A search function would also be useful. These features are possibly best served by other **Middleman** extensions.

All-in-all **Franklin** (plus Github Pages) is a simple and inexpensive way to develop and deliver your ebook. Being able to deploy to other formats (e.g PDF) and having a way to control access would be good if you are aiming to monetise your book, but if the goal is to "get it out there" **Franklin** seems a reasonable approach.
