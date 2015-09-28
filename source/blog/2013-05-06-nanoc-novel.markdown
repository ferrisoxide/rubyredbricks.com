---
kind: article
title: "nanoc-novel"
created_at: 2013-05-06 15:01
comments: true
categories:
---

![nanoc and ePub logos](/images/posts/nanoc-epub-logos.png){: class="image right"}

Much to my wife's chagrin, I've been writing an online novel. My good lady doesn't mind me writing, it's the constant fiddling with the technology that drives her crazy. And to be honest, tweaking layouts, trying out new frameworks, _etc_ is the e-version of sorting out your pens.

<!--READMORE-->

I finally settled on [nanoc](http://nanoc.ws/) a while ago, building a very rough draft using nanoc and [Maruku](https://github.com/bhollis/maruku) to convert numerous 'pages' of Markdown content into a website. You're welcome to [read it](http://thispurpleworld.com), though bear in mind it is still a draft, still very rough - and certainly not to everyone tastes.

The chief problem with writing like this is managing content split up over many files. If I want to insert a page, or move pages around, there's a fair bit of manual renumbering. It's also hard to edit, as I can't just read through the text as it flows. It's much easier to work with a single text file - and somehow break it up into individual pages for the final online version.

To this end I built a very simple gem called [breakdown](https://github.com/ferrisoxide/breakdown), designed to split a large Markdown file into many smaller ones - based on directives within the file.

Given my own novel is a work in progress I wanted to try this approach on a more substantial piece of text. Trawling through the [Gutenberg Project](http://gutenberg.net.au), I came across a wonderful set of short stories by [Edward Page Mitchell](http://en.wikipedia.org/wiki/Edward_Page_Mitchell). Mitchell wrote most of his stories in the 1870s and 1880s, making him an early contributor to American science fiction literature.

The Gutenberg Project version of Mitchell's work is collected as a set of short stories, entitled [The Tachypomp and Other Stories](http://gutenberg.net.au/ebooks06/0602521.txt). As such it made a good candidate for breaking into smaller pieces using the *breakdown* gem. Because the gem splits on normal Markdown horizontal rules, marking up the original text - and adding small amounts of formatting - took only a few minutes. The single-file version of the text looks something like this:


{:.language-text}
    *** index

    | Title  | The Tachypomp and Other Stories |
    | Author | Edward Page Mitchell            |
    ...
    ...

    *** table-of-contents

    THE TACHYPOMP
    THE SOUL SPECTROSCOPE
    THE MAN WITHOUT A BODY
    ...
    ...

    *** section-1

    THE TACHYPOMP

    A Mathematical Demonstration

    There was nothing mysterious about Professor Surd's dislike for me. I
    was the only poor mathematician in an exceptionally mathematical
    class. The old gentleman sought the lecture-room every morning with
    ...
    ...


Once converted into several files, nanoc does the heavy lifting of converting the files into HTML. In the example above, the text is split into `index.md`, `table-of-contents.md` and `section-1.md` and placed in nanoc's content folder. From there nanoc processes the files, adds styles and other artefacts as determined in its Rules file, and builds the website.

The process is still reasonably manual at present, requiring an import step and then build. By extending nanoc's command mechanism it should be relatively easy to wrap all the calls together compile the entire site from a single source file in one step.

The resulting online book can be found here: [http://tachypomp.rubyredbricks.com/](http://tachypomp.rubyredbricks.com/).

The source code for the books can be found here: [https://github.com/ferrisoxide/tachypomp](https://github.com/ferrisoxide/tachypomp).

None of this is particularly clever or (ahem) novel. The *breakdown* gem is fairly naive - it's the first gem I've ever written and I'm already slightly embarrassed by it. But as someone who no longer codes professionally, it was a bit of fun getting back into hacking.

There are a number of ways to improve this approach, but I'm already finding it paying dividends. Apart from the small amount of coding that went into the gem, I'm less inclined to fiddle with technology and more motivated to actually finish [This Purple World](http://thispurpleworld.com). And who knows, I might even make my wife happy in the process - though that's not a sure thing. For the record, she hates the novel.
