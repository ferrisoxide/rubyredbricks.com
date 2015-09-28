---
title: "Tachypomp: from ePub to HPub"
created_at: 2013-10-02 21:34
description: Creating an eBook using Ruby and HPub
kind: article
comments: true
---

The Tachypomp project is an attempt to build an ebook, using Ruby to convert a Markdown-based text into a variety of e-publishing formats. The [first cut](/blog/2013/05/06/nanoc-novel/) of the project served as a "proof of concept" for producing content in both ePub and HTML formats.

<!--READMORE-->

The next stage looks at producing an eBook within the [Baker Framework](http://bakerframework.com/), a e-publishing system for producing ebooks for the iPad. The Tachypomp project aims to produce content in HTML, ePub and the Baker Framework's [HPub](https://github.com/Simbul/baker/wiki/hpub-specification) formats, using the [nanoc](http://nanoc.ws/) gem to generate content in all formats from the same source text.

This project is still very much a work in progress. The long-term goal is to develop a general-purpose framework for
publishing content in a wide range of formats.

The Tachypomp and Other Stories
---

![Edward Page Mitchell](/images/posts/edward-page-mitchell.jpg){: class="image right"}

The source text for the project has an interesting background. [Edward Page Mitchell](http://en.wikipedia.org/wiki/Edward_Page_Mitchell) first published 'The Tachypomp' in 1874, while working as an editor for a New York City newspaper. Over the next decade and a half he produced several short stories that contain ideas and writing styles that were both novel and prescient - or at least interesting to look back at through the lens of time.

Often publishing anonymously, Edward Page Mitchell was only recognised as a leading early science-fiction author many years after his death in 1927. His work predates H.G. Wells own time-travel stories by several years.

Several articles have been written about Edward Page Mitchell, and are worthwhile reading by fans of speculative fiction:

* [Edward Page Mitchell (1852-1927), Science Fiction Pioneer](http://autodidactproject.org/bib/timemachine03.html)
* [The Man That Time Forgot](http://www.unwinnable.com/2010/09/27/the-man-that-time-forgot-%E2%80%93-edward-page-mitchell-and-the-burden-of-anonymity/)

Mitchell also has an entry in [The Encyclopedia of Science Fiction](http://www.sf-encyclopedia.com/entry/mitchell_edward_page).

The original text was sourced from the [Gutenberg Project](http://gutenberg.net.au/ebooks06/0602521.txt). The copyright has expired and these wonderful stories and now part of the public domain.

HPub - an Open Specification
---

![Baker Logo](/images/posts/baker-logo.png){: class="image left"}

The [Baker Framework](http://bakerframework.com/) is an open-source publishing system for producing content for the iPad and similar devices. It packages ebooks by wrapping a small executable around HTML content and allows the inclusion of Javascript and CSS to create compelling, interactive e-reading experiences.

A showcase of the kind of publications that can be generated using the framework is available on the [Backer Framework website](http://bakerframework.com/books).

The principles behind the [HPub specification](https://github.com/Simbul/baker/wiki/hpub-specification) that forms the core of the Baker Framework declares a digital publishing format that:

* Allows great designs
* Allows both pixel-perfect and flexible reflowable publications
* Is simple for authors
* Is simple for parsers

The heart of the HPub specification is JSON-based manifest file that declares the properties of a publication. A example (taken from the Tachypomp project):

{:.language-json}
    {
      "hpub": 1,
      "title": "The Tachypomp and Other Stories",
      "author": [
        "Edward Page Mitchells"
      ],
      "creator": [
        "Project Gutenberg"
      ],
      "date": "2006-07-01",
      "url": "book://tachypomp.rubyredbricks.com/ebook",
      "contents": [
        "index.html",
        "table-of-contents.html",
        "section-1.html",
        "section-2.html",
        "section-3.html",

        ...

Individual sections are created as HTML5 pages and compiled using an XCode project included in the Baker Framework into an executable application suitable for delivery via Apple's iTunes store.

For Android users, the compatible [Friar Framework](http://www.friarframework.com/) provides equivalent functionality, and can use the same JSON manifest and HTML5 pages as defined in the HPub Specification.

nanoc Representations
---

One of the key features of nanoc is the ability to have [different representations of the same source](http://nanoc.ws/docs/glossary/#glossary-representation). For instance, in the `Rules` file below the default representation uses the 'default' layout, whereas the epub and hpub representations use 'epub' and 'hpub' layouts respectively.


{:.language-ruby}
    compile '*' do
      if item.binary?
        # don’t filter binary items
      else
        filter :erb
        filter :kramdown
        layout 'default'
      end
    end

    compile '*', :rep => :epub do
      if item.binary?
        # don’t filter binary items
      else
        filter :erb
        filter :kramdown
        layout 'epub'
      end
    end

    compile '*', :rep => :hpub do
      if item.binary?
        # don’t filter binary items
      else
        filter :erb
        filter :kramdown
        layout 'hpub'
      end
    end

The `:rep => [:name]` parameter provides differing compilation rules for the same basic items. In Tachypomp's case, generating XHTML (for ePub) and HTML5 (for online and HPub) allows for different presentation models for the different output formats.

Similarly nanoc representations allow the generated content to be created in different target locations.

{:.language-ruby}
    route '*' do
      if item.binary?
        # Write item with identifier /foo/ to /foo.ext
        item.identifier.chop + '.' + item[:extension]
      else
        # Write item with identifier /foo/ to /foo/index.html
        item.identifier + 'index.html'
      end
    end

    route '*', :rep => :epub do
      if item.binary?
        # Don't build binary items
      else
        item_identifier = item.identifier.chop
        item_identifier = '/index' if item_identifier.empty?
        '/epub' + item_identifier + '.xhtml'
      end
    end

    route '*', :rep => :hpub do
      if item.binary?
        # Don't build binary items
      else
        item_identifier = item.identifier.chop
        item_identifier = '/index' if item_identifier.empty?
        '/hpub' + item_identifier + '.html'
      end
    end

In this example the default online representation is generated in nanoc's `/output` folder. ePub and HPub representions are generated in `/output/epub` and `/output/hpub` respectively.

This outlines how nanoc processes the items normally found in nanoc's `/content` folder. Importing the content and generating packages ready for delivery requires stepping through Tachypomp's installation process.

Installation
---

Clone the project into your local filesystem:

{:.language-text}
    git clone git@github.com:ferrisoxide/tachypomp.git
    cd tachypomp
    cp nanoc.yaml.example nanoc.yaml
    bundle install

The project assumes Ruby 1.9.3 is installed and will use RVM if available.

Importing Content
---

The sample text "The Tachypomp and Other Stories" is a collection of short stories. The source is maintained in a single text file to simplify editing. A `ebook.yaml` file is expected to be found in the project's root directory, defining the location of the source text and related meta data.

{:.language-yaml}
    source: the-tachypomp-and-other-stories.md

    meta:
      title: The Tachypomp and Other Stories
      creator: Edward Page Mitchells
      publisher: Project Gutenberg
      date: 2006-07-1

    epub:
      identifier:
          url: :scheme => 'URL'
          id: ??
      uid: ??

    hpub:
      version: 1
      url: book://tachypomp.rubyredbricks.com/ebook

To import the source text, run the following from within the project's root folder:

`nanoc import`

This will generate content files by breaking the larger file into several smaller ones, using the [Breakdown gem](https://github.com/ferrisoxide/breakdown). The Breakdown gem is also used to insert nanoc meta data into each processed page.

The import process will also create an `book.json` file in the `/content folder`. The `book.json` file is loosely based on the hPub ebook specification, and is used by other processes to build table of contents for alternative formats.

Compiling Content
---

The imported content will need to be compiled using nanoc:

`nanoc compile`

Once the content is compiled it can be packaged into different formats. Currently only ePub 2 and HPub formats are supported, but there's no reason why alternative formats (_e.g._ PDF) couldn't also be added. To package the ebook run the following:

`nanoc build`

This will build ePub and hPub compatible files in `/output/ebook` and `/output/hpub` respectively. The process also generates a zipped version of the HPub book with the `/output/ebook` folder.

Viewing the ePub Book
---

![Tachypomp view in Calibre](/images/posts/tachypomp-calibre.jpg){: class="image-left image-medium"}

Open the generated `/output/ebook/tachypomp.epub` in any ePub reader. I use [Calibre](http://calibre-ebook.com/) on the Mac, but any eBook reader capable of rendering ePub v 2 format books should work.

The output isn't particularly beautiful, but the ePub version is currently lacking any stylesheets. CSS support in ePub 2 is quite limited, so the options that will be available will be to just format the layout a little better.

There's no mechanism to add more interactive content via Javascript - though ePub 3 does have some support for more dynamic content. At present I can't find a decent ePub 3 reader for testing the content, but I can't imagine adding support will be much harder than developing the HPub version.

Building the HPub Book
---

![Tachypomp view in iPad simulator](/images/posts/tachypomp-ipad.jpg){: class="image-right image-medium"}

Download the [Backer Framework](http://bakerframework.com/). NB: you must have a recent version of XCode installed.

Copy the `/output/hpub/` folder from the Tachypomp project into the `/Baker Framework [version]/books/` folder, overwriting the existing `/book` folder.

Open the `/Baker Framework [version]/Baker.xcodeproj` file in XCode and build it. You should be able to run the book as an app within an iPad simulator.

As for the ePub version, there are no CSS styles applied. The Baker Framework does support CSS3 - adding a stylesheet simply wasn't a high priority for this proof-of-concept.

There are also some odd artefacts in the text where &ldquo; and &rdquo; aren't rendered properly. This may be related to the original source being in Latin-1 encoding. These could be cleaned up using a nanoc filter.


End Notes
---

While overall I'm happy with this as a proof-of-concept, there is a lot to do to take this prototype and work it into something useful. Not the least of which, I need to:

* Clean up the code (was a bit of a hack)
* Add support for PDF
* Stylesheets for ePub and hPub versions

I also intend moving much of the functionality into a Ruby gem to simplify the process of building new e-books.

Longer term the goal is to develop a complete publishing solution around nanoc.

You can view the online version at [tachypomp.rubyredbricks.com](http://tachypomp.rubyredbricks.com). The source code for this project can be found on [Github](https://github.com/ferrisoxide/tachypomp).


