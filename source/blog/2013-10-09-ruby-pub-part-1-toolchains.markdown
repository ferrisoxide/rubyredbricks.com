---
title: "Ruby Pub - Part 1: Toolchains"
created_at: 2013-10-09 21:49
description:
kind: article
comments: true
layout: post
---

![Ruby Pub](/images/posts/ruby-pub.png){: class="image left"}

Part 1 of a series investigating the development of a Ruby-based e-Publishing system.

Before progressing too far in building a toolchain for publishing e-books, I want to have a look at the current state of the art - just to see what's out there and check that I'm not re-inventing the wheel.

<!--READMORE-->

A surprising number of successful authors already use Ruby to drive their e-publishing toolchains. This post looks at a two of these systems: [Kitabu](https://github.com/fnando/kitabu){: target="_blank" } and [Bookshop](https://github.com/blueheadpublishing/bookshop){: target="_blank" }.


Kitabu
---

Jesse Storimer uses Kitabu in the production of his [series of books](http://www.jstorimer.com/pages/books){: target="_blank" } on working with low-level Unix sockets, TCP/IP and threads. He gives a more detailed explanation of how he uses Kitabu [on his blog](http://www.jstorimer.com/blogs/workingwithcode/7766061-my-ebook-publishing-process){: target="_blank" } than I will go into here.

### Installing Kitabu

Installing [Kitabu](https://github.com/fnando/kitabu){: target="_blank" } is slightly complicated, due to its dependency (via the eeepub gem) on RubyZip. RubyZip's interface changed in version 1.0, so to be able to install Kitabu we have to make use of an earlier version of the gem.

To install Kitabu:

{:.language-text}
    gem install rubyzip -v 0.9.9
    gem install kitabu

As a side note, this also affects the [Tachypomp](https://github.com/ferrisoxide/tachypomp){: target="_blank" } project.

Once installed Kitabu has some nice features. Executing `kitabu check` from the command line produces the following:

{:.language-text}
    Prince XML: Converts HTML files into PDF files.
    Not installed.

    KindleGen: Converts ePub e-books into .mobi files.
    Not installed.

    html2text: Converts HTML documents into plain text.
    Not installed.

    pygments.rb: A generic syntax highlight. If installed, replaces CodeRay.
    Not installed.

As the output suggests, Kitabu is capable of producing e-books in a variety of formats, including HTML, Kindle .mobi, PDF and text. PDF generation requires [Prince XML](http://www.princexml.com/){: target="_blank" }, a commercial tool for building high-quality documents. At $495 USD for a single user licence, Prince isn't an inexpensive solution, but it does produce exceptional PDF documents. If your goal is to produce print-ready PDFs then investing in Prince shouldn't be too onerous.

It would be nice if Kitabu could default to an open-source PDF generator (_e.g._ Prawn) in the absence of Prince, but pretty much all PDF generating gems have their own interfaces and it'd be a lot of work to maintain support for both.

### Working with Kitabu

Kitabu works with Markdown, Textile or HTML formatted files. All the contents are maintained in the `/text` folder, indexed by the convention of adding a sequence number at the start of each filename (_e.g_ `01_first_chapter.markdown`, `02_second_chapter.markdown`, ...). It's also possible to nest sections within chapters.

Running `kitabu new my_book_title` creates boilerplate layouts, templates, etc in the created `my_book_title` directory. There are various config files available to tweak, but that's beyond the scope of this post.

Kitabu's implementation of syntax highlighting is quite novel. Inline styling is supported by either CodeRay or Pygments, but Kitabu also allows you to declare references to code source files:

{:.language-text}
    @@@ ruby code/example-1.rb @@@

    In this example...

If you are working on a technical book this would probably prove handy, in that you can keep all your source code examples separate from the main text.

Another nice feature is `kitabu stats`. Running this command against the Tachypomp contents (ported to Kitabu) produced the following report:

{:.language-text}
    Chapters: 8
    Words: 34155
    Images: 0
    Links: 2
    Footnotes: 0
    Code blocks: 0

If you're wanting to keep tabs on your progress, I can see this being a handy feature.

Kitabu uses Sass under the hood to manage CSS. The ePub and HTML stylesheets are kept separate from each other in the `/templates` folder, allowing different styling for different representations of the content. This is a must, given ePub 2's limited support for CSS.

Kitabu has some built-in commands for generating content. For instance, the &lt;%= toc %&gt; will build a table of contents wherever declared. It may very well be possible to generate other common content using `erb`.

### Exporting Content

Running `kitabu export` will build HTML and ePub versions of your content by default, and PDF and .mobi versions if you have the right gems installed. The output is a little terse (`** e-book has been exported`), but the content is generated cleanly in your book project's `/output` folder.

I'm not sure if I did something wrong, but running Tachypomp through Kitabu generated an ePub document with a garbled front page. Looking through the unzipped ePub file doesn't reveal anything, so I suspect this is an issue with Calibre not rendering .png files properly.

The generated HTML ends up being in a single page. While useful for debugging, I wouldn't want people to have to download an entire novel in one hit. Not sure if there's any way to change Kitabu's behaviour, but it doesn't seem likely. There's also no built-in mechanism for deploy HTML to a server, though writing a simple `rake` task wouldn't be too difficult.

Though I didn't install Prince or .mobi support, the [sample documents](https://github.com/fnando/kitabu#samples){: target="_blank" } do show off its competent PDF generation capabilities.

### Summary

The framework isn't as extensible as either nanoc or Jekyll, but it's a purpose-built tool that does one thing well. Kitabu would be ideal as the main components of a publishing pipeline targeting off-line reading system like the Kindle or PDFs.

The inflexibility in managing HTML content, and the lack of nanoc-style filters to refine the generation of content, would drive me away from adopting Kitabu, but it's a nice and simple pipeline for producing e-books out of the box without having to write any custom code. Compared to `nanoc`, which requires a reasonable amount of configuration (e.g. the Rules file, custom commands) to get it to produce e-books, Kitabu assumes a sensible default model for e-book content.

In particular I like the `kitabu stats` command and would probably consider writing something similar as an extension to nanoc.


Bookshop
---

Pat Shaughnessy wrote his [Ruby Under the Microscope](http://patshaughnessy.net/ruby-under-a-microscope){: target="_blank" } using the [Bookshop](https://github.com/blueheadpublishing/bookshop){: target="_blank" } gem.

As for Jesse Storimer, Pat does a [much better job than I](http://patshaughnessy.net/2012/11/27/my-ebook-build-process-and-some-pdf-epub-and-mobi-tips){: target="_blank" } going through the ins and outs of using Bookshop. I will, however, endeavour to give it my best shot.

### Installing Bookshop

Keeping the gem environment clean, so I don't pollute the common setup:

{:.language-text}
    rvm use 1.9.3
    rvm gemset use bookshop --create
    gem install bookshop

I actually did something similar with Kitabu - I just neglected to mention it. Bookshop really seems to need [PrinceXML](http://www.princexml.com/){: target="_blank" } installed before being able to do anything reasonable. On the Mac this is fairly straight forward. After downloading the [PrinceXML package](http://www.princexml.com/download/){: target="_blank" } it's just a matter of unpacking the archive and running `/.install.sh`. Flawless. It doesn't matter if you install this before or after installing the Bookshop gem.

Bookshop required Java 1.5 or better installed, presumably to run [epubcheck](https://code.google.com/p/epubcheck/){: target="_blank" } - a tool for validating ePub files.

### Using Bookshop

Building a basic outline for an e-book is simple:

{:.language-text}
    bookshop new my_new_book

Bookshop is a much bigger beastie than Kitabu. The generated scaffolding is quite extensive, with a clean separation of content into `/frontmatter`, `/bodymatter` and `/backmatter` folders.

Bookshop uses HTML5 as the basis for generating content in different formats, not Markdownm and makes extensive use of `erb` to prepare the e-book text. It can also vary the generated content depending on the target format:

{:.language-ruby}
    <% if @output == (:pdf or :html) %>
      <%= import('frontmatter/cover.html.erb')%>
    <% end %>

While not as versatile as nanoc's representations, it certainly allows for variations in content between delivery formats. The `import` method is also interesting - you can organise your book in whatever files and folders suits you and pull the content in where appropriate. Because it's all `erb`-driven I imagine this would lend itself to reusing common content (_e.g._ copyright notices) or even a Rails-style templating system at a pinch. This is in contrast to the way I prefer to work, where a single source file is broken down using my [Breakdown gem](https://github.com/ferrisoxide/breakdown){: target="_blank" }. But I can see this working for what is probably a more typical writing workflow.

Defining the table of contents is a little irksome. You have to build `toc.ncx.erb` (for ePub / .mobi) and `toc.html.erb` (for HTML5 / PDF) separately by hand. It would be nice to have a single metadata file - say a `contents.yml` - that defines the table of contents in the one place, and generate the required artefacts automatically.


### Exporting Content

Running `bookshop build` gives a list of available formats and the location of build artefacts:

{:.language-text}
    $ bookshop build

    Usage: bookshop build [ARGS]

    The most common build commands are:
     pdf          Builds a new pdf  at /builds/pdf/book.pdf
     html         Builds a new html at /builds/html/book.html
     epub         Builds a new epub at /builds/epub/book.epub
     mobi         Builds a new mobi at /builds/mobi/book.mobi
     all          Builds all formats above

    All commands can be run with -h for more information.

As with Kitabu, the generated HTML ends up in a single file. Not particularly useful (at least to me), but the focus is very much on building content for offline delivery. Generating content in ePub format produces the following output:

{:.language-text}
    $ bookshop build epub

    Deleting any old builds
    rm -r
    cp -r book/epub/META-INF builds/epub/
    cp -r book/epub/mimetype builds/epub/
    Generating new html from erb
    Generating new cover.html from erb
    Generating new toc.html from erb
    Generating new content.opf from erb
    Generating new toc.ncx from erb
    cp -r book/assets builds/epub/OEBPS/assets/
    Zipping up into epub
      adding: mimetype (stored 0%)
      adding: META-INF/container.xml (deflated 32%)
      adding: OEBPS/assets/css/page-template.xpgt (deflated 78%)
      adding: OEBPS/assets/css/stylesheet.epub.css (deflated 65%)
      adding: OEBPS/images/canvas.jpg (deflated 0%)
      adding: OEBPS/images/cover.png (deflated 0%)
      adding: OEBPS/images/draft.png (deflated 6%)
      adding: OEBPS/images/html-18.png (deflated 2%)
      adding: OEBPS/book.html (deflated 59%)
      adding: OEBPS/content.opf (deflated 60%)
      adding: OEBPS/cover.html (deflated 34%)
      adding: OEBPS/toc.html (deflated 61%)
      adding: OEBPS/toc.ncx (deflated 70%)
    Validating with epubcheck
    Epubcheck Version 1.2

It interesting to note the `Validating with epubcheck` output. One nice feature of Bookshop is that it makes sure the generated content is valid ePub, something that Kitabu (and my own Tachypomp for that matter) trusts the eeepub gem to do for you. As it happens, I didn't suffer the same sort of weirdness generating ePub content that I did in Kitabu. Both systems created an image as part of the front page, with the Kitabu-generated front page seeming to scramble in Calibre. I didn't have any similar problems with Bookshop.

All the zipping is done with the Unix `zip` command. On Windows it's assumed that a `zip.exe` is available on the path. Bookshop doesn't use eeepub or any other gems for generating ePub 2 content. While there is more internal code to generate the ePub book, there's no reliance on third party gems like RubyZip.

Building a PDF file generates clean, print-ready documents - though it does require a commercial licence from Prince.

I didn't try creating a Kindle .mobi file, but as this just pipes the ePub contents through [Amazon's Kindlegem](http://www.amazon.com/gp/feature.html?ie=UTF8&docId=1000765211){: target="_blank" } executable I don't imagine there would be any real problems. I don't have a Kindle to test against, so I'm going to assume this just works.

### Summary

One major downside of Bookshop is that it is entirely driven by HTML5 - there doesn't seem to be any simple way to introduce Markdown or other alternative markup languages into the mix. There's also no mechanism to add additional formats (_e.g._ HPub). Having said that, the code is clean and easier to understand - it wouldn't take a massive effort to add either (hint: have a look at `/lib/bookshop/commands/build.rb`).

I really like the epubcheck integration. Knowing the generated content matches the ePub specification creates peace of mind. As a basic QA check I can't see any reason not to integrate this into a nanoc-based system.

Conclusion
---

While both Kitabu and Bookshop do an admirable job of producing e-books in a variety of formats, I can't say I'm compelled to walk away from nanoc. The limited options for generating HTML content are probably fine in general, but doesn't support the online / offline publishing model I'm pursuing.

There's nothing either can do that can't be replicated in nanoc and - while it may require additional coding - I'd prefer the flexibility of a general purpose tool. There are a lot of good ideas in both systems examined, and I undoubtedly look to stealing some of these for my own system.

In short, I expect the majority of authors looking to produce content for offline readers would do well to look at either Kitabu (if your preference is for writing in Markdown) or Bookshop (for HTML based content).

In follow up posts I'll be looking at commercial e-publishing systems, examining how Ruby can be used to build an open source e-publishing system that takes written content all the way through to delivery, promotion and selling. And naturally enough I intend getting round to actually coding my own platform, building on what I've seen in Kitabu and Bookshop - and lessons learned from the [Tachypomp](/blog/2013/10/02/ruby-and-hpub/) project.
