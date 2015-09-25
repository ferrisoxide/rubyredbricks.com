---
kind: article
title: "More ePub with nanoc"
created_at: 2012-09-19 07:09
comments: true
categories: epub, ruby
---

A little way back I posted a [lengthy article](/blog/2012/09/06/quick-and-dirty-epub-with-nanoc/) on
how to create a e-book using ePub, the static website compiler [nanoc](http://nanoc.stoneship.org) and some
quick and dirty code. In this much shorter post I'll aim to clean up the solution a little.

<!--READMORE-->

nanoc meta-data
---------------

One of the nice things about **nanoc** is the capacity to add arbitrary meta-data to each page. In the original version of the e-book I used a convention based on the URL and **nanoc** identifier to link from one page to the next: page '/001/' precedes page '/002/' and so forth. It was crufty and required more gsubbing than was sound.

A cleaner approach is to put the data directly inside the page, within **nanoc's** meta-data block:

{:.language-text}
    ---
    title: Epithelium
    chapter: 1
    page: 1
    ---
    ... page goes here ...


There are certain pages that I want to exclude from the ePub version (_e.g._ the website home page). Again, using meta-data the ePub compiler can directed to ignore specific content:

{:.language-text}
    ---
    title: Home
    exclude_from_epub: true
    ---




Using the preprocessor
---------------------

Another useful feature in **nanoc** is the **preprocess** directive you can add to the _/Rules_ file. Using this I can build an outline of the e-book from meta-data and hive it off to a _.yaml_ for other parts of the code to use later.

{:.language-ruby}
    preprocess do
      outline = {}

      @items.each do |item|
        unless item[:exclude_from_epub]
          chapter = item[:chapter]
          page = item[:page]
          title = item[:title]

          if chapter
            outline[chapter] = {} unless outline[chapter]
            outline[chapter][page] = {
              :label => item[:title],
              :file => item.identifier.gsub(/^\/|\/$/, '') + '.html' }
          end
        end
      end

      File.open("tmp/outline.yaml", "w") {|file| file.write outline.to_yaml }
    end


The full code for the e-book can be found on [Github](https://github.com/ferrisoxide/this-purple-world).

Five more pages have been added to both the web and ePub versions of the book and are available [online](http://thispurpleworld.com). I'll be releasing more on a regular basis, so if you're interested in following the book's progress feel free to follow me on Twitter or look for the hashtag _#thispurpleworld_.

<a data-show-count="false" class="twitter-follow-button" href="http://twitter.com/ferrisoxide">Follow @ferrisoxide</a>

