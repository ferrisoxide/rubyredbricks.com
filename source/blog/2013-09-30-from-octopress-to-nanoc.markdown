---
title: "From Octopress to nanoc"
created_at: 2013-09-30 21:33
description: This is the description
kind: article
comments: true
---

Regular visitors will have noticed recent changes to the Ruby Red Bricks blog. There are some
visual differences, but more significantly the blog has been ported completely from
[Octopress](http://octopress.org) to [nanoc](http://nanoc.ws/).

<!--READMORE-->

Why the Change?
---

The time I get to code is extremely limited - mainly on the bus to and fro work. Both Octopress and nanoc are excellent tools and - while I'd like to be able to keep my options option - I don't have the time to keep up to date with both static site compilers.

It also seems a little silly blogging about playing with nanoc using a Jekyll-based blogging tool. Running the whole show with nanoc gives me an opportunity to get half-decent with one tool (instead of being crap at two).

But Octopress is Awesome!
---

Yes, Octopress is a fantastic blogging tool and has so much going for it: the Octopress community is as vibrant as the nanoc community (judging by Google Groups posts); it is part of the larger [Jekyll](http://jekyllrb.com/) family; and Octopress comes with pretty much everything you need for blogging, right out the box.

Octopress also comes with a bunch of stuff I never had any use for. I could have trimmed it down a little, but some of the code seems a bit magical - and I never knew what I might break. Octopress sites also tend to look like other Octpress sites. This is not a slight against Octopress - more an issue with me being lazy / time-poor.

![Sad Octopress](/images/posts/sad-octopress.jpg){: class="image left"}

In many ways it's not fair to compare the two systems, as Octopress is a much more a complete blogging tool than nanoc. With nanoc I had to build many things up from scratch, using advice from [here](http://jakoblaegdsmand.com/blog/2013/01/easy-blogging-with-nanoc/) and [there](https://github.com/crazysim/nanoc-foundation-blog) in the community. Though there's still work to be done, the  first cut has a smaller and more focused codebase than the original blog.

I'm sorry if I've made you sad, little Octopress creature, but I wish you all the best. And I really enjoyed the time we spent together.

nanoc is Awesome Too!
---

Where Octopress is reasonably prescriptive in how to construct blogs - it's probably fair to call it an "opinionated" blogging tool - nanoc is pretty much open-ended. There are benefits in its openness - I was able to move to [Zurb's Foundation](http://foundation.zurb.com/) framework for the front-end - but the cost is having to rewrite much of the support code by hand.

nanoc does come with tools for maintaining blogs.  [`Nanoc::Helpers::Blogging`](http://nanoc.ws/docs/api/Nanoc/Helpers/Blogging.html) and related modules makes it relatively simple to port some of the parts of an Octopress blog. Rewriting the [Archives](/archives) section was straightforward, thanks to all the blog articles being automatically collected in reverse date order within nanoc's [`sorted_articles`](http://nanoc.ws/docs/api/Nanoc/Helpers/Blogging.html#sorted_articles-instance_method) array.

Adding an RSS link was a painless one-liner, using the [`atom_feed`](http://nanoc.ws/docs/api/Nanoc/Helpers/Blogging.html#atom_feed-instance_method) method. But that's about where the easy fun stopped and we moved into "why am I doing this again?" territory.

Porting
---

Moving from Octopress to nanoc took about four or five hours. There were a couple of near rage-quit moments, but mostly due to my stupidity. Overall the process went fairly smoothly, thanks to a great deal of similarity between the two systems.

### Meta-data

Octopress and nanoc share the same basic model for content. The meta-data header for pages is essentially the same and - apart from inserting a `kind: article` entry in each post header - there was nothing to change other than move all the pages and posts into nanoc's `/content` folder.

### Markup

Markup proved a little trickier. Both nanoc and Octpress make use of a Mardown-style markup. Octopress also uses [Liquid Markup](http://liquidmarkup.org/) for more programmatic bits, but Liquid support was removed from nanoc quite some time ago. nanoc has a range of filters available, but the most natural seemed to be a mixture of `kramdown` (for Markdown) and `erb` (for the programmatic parts). The porting process was mostly a matter of seeing what broke, and fixing with either layout changes or creating helper methods.

### Layouts and Style

Thankfully I hadn't used much of the plugins packaged with Octopress, otherwise I would have had more trouble replacing functionality. Rebuilding the layouts was more a matter of taking the example Foundation blog layout and reworking it to render the nanoc `item` objects.

One neat trick I lifted from [Dave Clarke's blog](http://clarkdave.net/2012/02/building-a-static-blog-with-nanoc/) is displaying an excerpt of recent blog posts on the front page. Previously Octopress would display the complete post for any recent articles, increasing the page load time. The front page is a lot shorter, with links to the full article. I'm hoping this will incline people to read and reply to comments, as the the Disqus widgets were not available on the index page. We'll have to see how that goes.

Foundation is a bit of a task master. There's no simple way to make use of the grid within Mardown posts, unless you're willing to sprinkle your otherwise clean text with a bunch of `divs`. I had to cheat a bit, floating images to the left or right. It's not perfect, and I'm pretty sure it will choke the responsive layout. But it's a start.

One word about using the Foundation gem: pulling in the assets via Compass ([source](https://github.com/ferrisoxide/rubyredbricks.com/blob/master/compass/config.rb)) sprinkles the root of the project with `robots.txt` and other related files. I don't know how to prevent this, but it's just a matter of cleaning up after the event.


### Helpers & Commands

As noted before, the standard `Nanoc::Helpers::Blogging` module simplifies some of the core blogging functionality. It's missing the Twitter, Google Plus One, Disqus, Pinboard, Delicious and Google Analytics plugins provided by Octopress by default - but of these I was only really using Twitter, Disqus and Google Analytics.

The Twitter plugin stopped working when Twitter changed the terms of their API, so I've dropped that until I can think of a way to integrate it again (maybe as a third-party service). Google Analytics and Disqus integration is straightforward - it's just as simple as dropping the widget code into the layouts.

A modification of the `nanoc create_post` command pilfered from [Jakob Lægdsmand's blog]( http://jakoblaegdsmand.com/blog/2013/01/easy-blogging-with-nanoc/) to build the scaffold for each new post and I had an almost complete blogging system again.

### Images

Octopress uses a helper provided via Liquid to create image tags. While I like the model, it seems unnecessary when ordinary Markdown offers a similar feature. Adding CSS - to float left and right as mentioned earlier - doesn't seem to be supported by the standard Markdown syntax, but thankfully the Kramdown filter adds support without the resulting code looking too weird.

So the original Octopress image tag:

~~~~~~~~
   \{\% img left /images/posts/adelaide-metro-old-style-ticket-machine.jpg 250 250 Adelaide Metro Ticket Machine in Action \%\}
~~~~~~~~

becomes something very similar (but more Markdowny).

~~~~~~~~
    ![Adelaide Metro Ticket Machine in Action](/images/posts/adelaide-metro-old-style-ticket-machine.jpg){: class="image left"}
~~~~~~~~

### Codeblocks

Codeblocks was where things really came unstuck. There are a few options in nanoc-land, but most seemed a bit clunky - at least compared to Octopress's really nice [Solarised](http://ethanschoonover.com/solarized) solution.

I opted for simple, using Kramdown's built in support for Coderay. It doesn't look anywhere near as nice as the Octopress solution, but it will do in the short-term.

You can see various samples of Coderay in action in the refurbished [More ePub with nanoc](/blog/2012/09/19/more-epub-with-nanoc/) post from last year.


The Future
---

So where to from here? The blog is back up and running but will need a bit of tweaking - mostly removing inline CSS and other crufty artefacts from the port. I need to clean up the repo as well, as there are a few odd files left over from Octopress.

There's a hint of irony in what I've ended up with. Having wanting to move away from a blog that looks like other Octopress blogs, I've ended up with something that looks like every other Foundation based site. But the structure is in place now - there's something I feel I can work on.

The move has given me a fresh mindset. I like the simplicity of the new look and will strive to keep it like that. The main job ahead is to keep the content interesting and useful, so I've come up with a few basic rules I think will help:

### 1. Keep It Focused

This is a technical blog, and it should stay that way.

Politics, religion and pop music should stay where belong - on Facebook. If I'm inclined to post personal rants, there are other places on the internet for that.

### 2. Keep It Real

Ruby and Lego&trade; - and other things I dig? So where's the Lego?

I kinda got distracted from the main game early on. This blog was meant to be about the fun to be had playing with Ruby and Lego, with the odd bit of interesting tech thrown in. I want to get back to that and, wherever possible, bring the two worlds of Lego&trade; and Ruby together.

And the things I dig? Cue [joke for the kids](http://www.youtube.com/watch?v=k2rDbRUDkds).

### 3. Maintain the Joy

This is my happy place. This is where I come to experiment with and think about code and stuff. In a world where I'm banned from bringing Nerf guns to work (well.. I did shoot one of the other senior managers in a surprise dive from behind the printer), this is my little space to remember the good times. Back when my business card read "Code Poet" and the Internet felt like a magic sandbox.

### 4. Blog Early, Blog Often

As [Jon Moses](https://twitter.com/technicalmoses) commented on an [old post](/blog/2013/05/06/nanoc-novel/), "If you're not embarrassed (in some way) by code you release, you waited too long to release it." Ditto for blogging. Any ideas I have can live or die under the unyielding scrutiny of the internet, or be shaped into something new by smarter minds than my own.

I've yet to find my voice as a blogger - or even as a writer in general. The path is through practise, practise, practise. There's no short cuts - and there's no shortage of things I want to write about.

Final Notes
---

Finally, a thank you. Firstly to you, for reading yet another lengthy blog post. Secondly, to the nanoc crowd, whose numerous posts helped get me back on track. But lastly to Octopress - thanks for a great tool that kept the blog running the last couple of years.

### The Code

You can grab the code for this blog from [Github](https://github.com/ferrisoxide/rubyredbricks.com). If you want to see the nitty-gritty of the changes feel free to trawl through the commit log. You probably won't find anything revolutionary, and I expect anyone doing the same sort of thing would do it a completely different way. But the meandering path is there for all to see.

### Credits

I lifted most of the code from other people, so feel free to nick stuff from me. If I have pilfered code without appropriate attribution, please take me up on it. And if there's anyone I've forgotten to mention, let me know. But here's a list of nanoc people I feel I owe a debt to:

* [Jakob Lægdsmand]( http://jakoblaegdsmand.com/blog/2013/01/easy-blogging-with-nanoc/)
* [Dave Clarke](http://clarkdave.net/2012/02/building-a-static-blog-with-nanoc/)
* [Daniel Hoey](http://www.danhoey.com/blog/2011_09_23_building_a_blog_with_nanoc/)
* [Nelson Chen](http://www.mindflakes.com/)
* [Jim Mendenhall](http://www.starryhope.com/going-static-with-nanoc/)

and of course:

* [Denis Defreyne](http://stoneship.org/)
