---
kind: article
title: "Ruby as CMS"
created_at: 2012-08-27 18:01
comments: true
categories: ruby, content-management
layout: post
---

In a nutshell
-------------

Using Ruby as a CMS you can:

* build fast and secure websites
* maintain control of production
* manage your revisions with a sensible tool (Git, Subversion, _etc_)

<!--READMORE-->

Some history
------------

A few years back - in an earlier incarnation of this blog - I put forward the idea that Ruby on Rails could serve as a half-decent content management system. You can access the [original post](http://web.archive.org/web/20100726152648/http://rubyredbricks.com/2009/2/24/rails-as-cms) and a [wiki.rubyonrails.org discussion page](http://web.archive.org/web/20100611170511/http://rubyredbricks.com/2009/2/24/ruby-on-rails-based-cms) via the Wayback Machine.


Looking back I still stand by the general concept: if you have a technical design team that understands HTML, CSS and little bit of code, it's relatively easy to craft an organisation's online presence using layouts, templates and other tools that Rails provides. This approach cuts down the friction between software development and web design: developers act as toolmakers, crafting helper methods and supporting code. Designers inspire efficiency by consuming these tools and providing feedback. Website development becomes a space where devs and designers work together, sharing ideas and tools while building a common vocabulary.

I've seen this in action. [Carbon Planet's](http://www.carbonplanet.com) website was managed like this for some time.

The trick was having a smart designer who knew his stuff, and wasn't afraid to get his hands dirty with a little code. I'd like to think we gave our designer a better experience than he would have had munging up pages with PHP.

There are certain places where the "Rails as CMS" idea works as a clean, lightweight content management solution. It's great for developing and serving up static pages - and for mixing in dynamic content through normal Rails development practices.

Enter nanoc
-----------

After Carbon Planet I ran a part-time web hosting business built around Wordpress. The intent was to get away from having to code, provide customers with a simple interface for managing their own sites - and generally sit back and relax a little. The reality was that I ended up doing most of the content management anyway - and spent more time than I wanted configuring Wordpress or plugging up security holes. Worst of all, I was having to code in PHP!

When a rogue plugin swamped my sites with malware I decided to pack it in. Yes, I had backups - and I was able to remove the cruft from the system - but in the end it was less stress to lock down Wordpress as much as I could and help customers move to other providers as their contracts came to a close.

The handful of clients I decided to maintain still wanted a solution. Because their needs were simple - no more than basic brochure-ware - all I needed was a tool to build static web sites. [Jekyll](http://jekyllrb.com) and [nanoc](http://nanoc.stoneship.org) seemed to be the two standout toolkits in the Ruby space at the time and, wanting to just get something up and running quickly, out of sheer arbitrariness I picked *nanoc*.

The inexplicably all-lowercase *nanoc* was a dream. One quick gem install and I had all the tools for building a simple website. I could use Markdown or any number of template models for building content. I could use ERB-style layouts. I could build helpers to streamline common features. All configuration and content could be stored in Git. Here was the same Rails-as-CMS idea, without all the great but unnecessary features that Rails provides. And I was back using Ruby. Joy!

More recently I've wanted to get back into blogging, and needed more dynamic content (comments, contact forms, _etc_) than *nanoc* gives out of the box. There are plenty of good services (_e.g._ Disqus, Wufoo) that provide these sorts of features, but integrating these into *nanoc* meant more coding that I had time for. Having followed the [But She's A Girl](http://www.rousette.org.uk/blog/) blog, I was inspired by BSAG's [move away from *nanoc*](http://www.rousette.org.uk/blog/archives/octopress/) to give Octopress a go.

Blogging with Octopress
-----------------------

[Octopress](http://octopress.org) is a framework for building blogs, built on top of Jekyll. It's given me a chance to scratch that particular itch - that maybe I'd be missing something by going with *nanoc* over Jekyll - not it's not fair to compare it to *nanoc*. Octopress comes with an excellent swag of plugins, and configuring it to connect to Google Analytics, Twitter and other services you're likely to want to use is just a matter of filling in the blanks in its _config.yml file.

Adam Pohorecki does a better job of examining the pros and cons of Octopress in his blog post ["Octopress - the Good, the Bad and the Ugly"](http://adam.pohorecki.pl/blog/2012/01/17/octopress-the-good-the-bad-and-the-ugly/). I'd have to concur that if Octopress was installed as gem like *nanoc*, instead of forking the project from Github, it'd be a bit easier to maintain. But not by much, and in any case the developer provides a [guide to updating Octopress](http://octopress.org/docs/updating/). I can't say I've had to go through this process yet.

I couldn't tell you which I prefer out *nanoc* and Octopress. If there was an "Octopress for *nanoc*" I might give it a go, but I'm not aware of anything that comes with a such a grab-bag of goodies and is as easy to set up. The content models for Jekyll and *nanoc* are reasonably similar, even the metadata headers, so I don't imagine it would be too difficult to port from one to the other if a decent blogging option for *nanoc* becomes available. But right now, Octopress serves my needs.

Serving it statically
---------------------

One potential benefit of using static site builders is the performance boost you get in production, though conventional caching techniques probably make the overhead of hitting a database insignificant in most cases. It's undoubtedly a good idea to provide a cache for your static pages, but it will involve fewer moving parts (and less configuration) than for database-driven sites.

Another benefit is the creation of an "air gap" between your production and development systems. Traditional CMSes usually provide an internal draft/publish model for managing content, but the draft version of a statically generated site can live in an entirely separate environment. Publishing a static site is a very deliberate act, where you rsync or otherwise push the entire site up to production. If you're using a version control system like Git to manage your content you have more fine-grained control over the drafting process, including branching and potentially rolling back your site to a known version (if things go horribly wrong).

For me the most important benefit is security - especially after my Wordpress experiences. A static site is a secure as the operating system and web server hosting it. A code-heavy site is as exploitable as its weakest component.

Outsourcing dynamic elements
----------------------------

One downside of static sites is - naturally enough - the lack of dynamic content. Thankfully there are numerous services - both free and commercial - for providing these features within statically built sites. I've mentioned [Disqus](http://disqus.com) and [Wufoo](http://wufoo.com) for providing comments and basic forms respectively, but there are a wide range of other providers even in this space. As for other features, if you need to add mailing list subscription to your site, [MailChimp](http://mailchimp.com) offer ways to integrate with your static content.

The choice of which services to use will come down to your budget and what is going to fit with you site. But this "outsourcing" of the dynamic components of your site is potentially cheaper than developing your own solutions or hosting a complete CMS.

There's also the opportunity to build your own common services and integrate them across all your sites. I've been considering this for contact forms: developing a web application and serving up customised forms to each of my customers' static sites. There are efficiency gains in sharing services between sites instead of code. Rather than implementing a contact form and database backend in every site I put together, I can build one service and focus my energies on making that system as reliable, configurable and secure as possible.

Summing up
----------

So what is this Ruby as CMS idea good for? If you're a coder who knows a little design, or a designer who understand a bit of code, then it's probably a good fit. It's probably heavier on code side of things, but if you're in a team with both sets of skills you could make this work for you.

It's not going to be ideal if you have a lot of dynamic content, or if you need to provide differing levels of access to different parts of your site. But..

* if you need to serve up web sites securely
* if you want to keep things simple
* if you're happy to have your developers and designers working in the same space
* if you're already comfortable with having Git or other version control systems in your toolchain

.. then using Ruby as your CMS may be for you.

For me, I'll keep on using *nanoc* for building sites for customers and using Octopress for blogging. They are both great tools and I don't feel I'm losing anything by getting to know both. If you are having similar experiences, or disagree with me completely, please feel free you to share your thoughts.

If you're viewing this post on the home page, please visit the link to the page in "Recent Posts" if you'd like to leave comments.
