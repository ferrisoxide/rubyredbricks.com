---
kind: article
title: "Quick and dirty ePub with nanoc"
created_at: 2012-09-06 18:01
comments: true
categories: epub, ruby
---

Bit of background
-----------------

First off, this is a bit of a hack. At best it's a jump headfirst into publishing an ePub document using **nanoc**, while knowing very little of the former and only a fraction more of the latter.

We're aiming for a quick way of producing ePub books, using **nanoc** to do the bulk of the
heavy lifting while other gems and custom code fill in the rest.

<!--READMORE-->

A sample project demonstrating this approach can be found at [thispupleworld.com](http://thispurpleworld.com), with the source code available on [Github](https://github.com/ferrisoxide/this-purple-world).

An ePub Primer
--------------

ePub is a set of standards produced by the International Digital Publishing Forum. There are two main versions currently in use: [ePub 2.0.1](http://idpf.org/epub/201) and [ePub 3.0](http://idpf.org/epub/30). ePub 3.0 introduces some interesting features, including interactivity via Javascript and support for SVG and MathML, but the tools and content we'll be looking at here use version 2.0.1

ePub is essentially a packaging model for electronic books. Content (pages, chapters, _etc_) is represented as XHTML documents, styled with CSS and bundled together in a zip file. Two special xml-based files, *toc.ncx* and *content.opf*, contain the table of contents and overall descriptive data for your book.

It is possible to bundle fonts, images and other elements into your ePub package. For now we're just going to worry about text content. It's also possible to create nested documents in ePub, but in our first cut we'll be assuming a flat structure.

*Step 1:* make it work. *Step 2:* make it pretty.

Setup nanoc
-----------

Let's set up an empty **nanoc** site to start filling with the content.

{:.language-text}
    gem install nanoc
    nanoc create_site my-epub-project
    cd my-epub-project


Create a */Gemfile* in the same directory.

{:.language-ruby}
    source "http://rubygems.org"

    gem 'nanoc'         # here for convenience
    gem 'adsf'          # simple web server for previewing content
    gem 'kramdown'      # used to generate Markdown content
    gem 'systemu'       # used when deploying via rsync
    gem 'eeepub'        # ePub generator

The [eeepub](https://github.com/jugyo/eeepub) gem provides a simple DSL for packaging XHTML documents. It also provides lower level tools for manipulating *toc.ncx* and *content.opf* files, but we won't be using these.

Install the gems using bundler:

{:.language-text}
    bundle install

Setup eeepub
------------

**nanoc** allows for multiple layouts. Create an alternative layout for generating the ePub XHTML documents in the project's */layouts* directory. NB: `<meta>` tags need to be terminated with a closing `/>` in XHTML.

{:.language-text}
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">
    <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
      <head>
        <title>
          <meta name="generator" content="*nanoc* <%= Nanoc::VERSION %>" />
        </title>
      </head>
      <body>
        <div id="main">
          <%= yield %>
        </div>
      </body>
    </html>




**nanoc** also allows for multiple representations, meaning the same content item can be rendered in different formats. We'll make use of this to render the website in both HTML (for viewing online) and XHMTL (for ePub). In the project's */Rules* file add the following compile declaration to the rules **nanoc** created by default:

**/Rules**

{:.language-ruby}
    compile '*', :rep => :epub do
      if item.binary?
        # don’t filter binary items
      else
        filter :erb
        filter :kramdown
        layout 'epub'
      end
    end


Note we're using both **:erb** and **:kramdown** filter types. We'll come back the reason for this later. The compile declaration includes a **:rep => :epub** option and changes the layout to use the *epub.html* template created before.

In the same */Rules* file add the following routing rule:

**/Rules**

{:.language-ruby}
    route '*', :rep => :epub do
      if item.identifier == '/'
        # Exclude the root index.html from the epub version
      else
        # Write item with identifier /foo/ to /epub/foo.html
       '/epub' + item.identifier.chop + '.html'
      end
    end

Again, the route includes the **:rep => :epub** option. The route rule explicitly removes the default */index.html* created by **nanoc** when initially generating the site, as this will only be used in the web version. We may change this later, but I wanted to illustrate that you can vary the content between representations using route and compile declarations.

We'll store the configuration data **eeepub** and our custom code will need in */epub.yaml*. Create this file
in the project's root directory.

**/epub.yaml**

{:.language-yaml}
    meta:
      title: My ePub Project
      creator: Me!
      publisher: Me!
      created_at: 2012-09-07
      identifier:
        url: http://my-epub-project.com/book
        id:: BookId
      uid: BookId

    filename: my-epub-project.epub
    output_dir: output/book

I'll have to admit, I don't really know what the **identifier** and **uid** attributes are for - other than they end up included in the generated ePub meta data. I plan on looking into the ePub meta data further.

The **filename** and **output_dir** attributes will be used to generate the actual *.epub* file.

Creating Pages
---------------

While ePub allows for hierarchical ordering of content, we're going to use a flat structure for now. In
your project root, execute the following on the command line:

{:.language-text}
    nanoc create_item "001"
    nanoc create_item "002"

**nanoc** will have created two files in the */content* folder. Naming each page '001', '002' and so on
is just a convention we'll make use of custom code later on. We could have just as easily use 'Page 1', 'Page 2', _etc_ for our convention.

Edit both with some random text for now, but I'll ask you to do something a bit odd with them.
Somewhere in text add an **erb** style tag like below. Do the same for the text you add to the second file.

**content/001.html**

{:.language-text}
    ---
    title: 001
    ---
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla
    imperdiet est sit amet quam placerat ac mollis est viverra. Nunc
    adipiscing purus sit amet lorem imperdiet vitae aliquet sem tincidunt.
    Proin pretium ultrices nulla, at tincidunt lacus tempus in.
    Etiam sollicitudin odio &lt;%= link_to_next_page 'in dui elementum vulputate' %&gt;


The **link_to_next_page** method is not built into **nanoc** - it's a style of linking between pages
I've used in [this purple world](http://thispurpleworld.com). It may not be useful to you, but I want to show
how we can control how content is generated in different representations.

This also illustrates how we can mix and match template types in the same file. Here we are using both **erb** and **markdown** content together - made possible by the multiple **filter** properties set in the */Rules* configuration file.

Representation-Aware Content
----------------------------

One way we can extend **nanoc** is by adding helpers to the */lib* folders. These helpers are similar to
helpers in Rails - methods to generate smaller pieces of content. Here's the **link_to_next_page**
method used above:

**/lib/link_to_page.rb**

{:.language-text}
    include Nanoc3::Helpers::LinkTo

    module LinkToPage

      def link_to_next_page(text)
        if @item_rep.name == :epub
          text
        else
          next_page_num = @item.identifier.gsub(/\//, '').to_i + 1
          next_page_ident = next_page_num.to_s.rjust(3, '0')
          # check if last page
          next_page = @items.find { |i| i.identifier == "/#{next_page_ident}/" }
          next_page ? link_to(text, "/#{next_page_ident}") : text
        end
      end
    end

    include LinkToPage

The code is a little crufty, but what it's basically doing is creating an href link between pages - but
only in the default representation. With ePub we can assume that readers will navigate from page to page
using the controls built into their ePub readers, so we don't want the **link_to_next_page** to do anything
much at all, apart from returning the text passed to it.

Add the *link_to_page.rb* code to */lib* and run the command line run the following:

{:.language-text}
    nanoc compile

Have a look at the HTML and XHTML **nanoc** generates in the */output* and */output/epub* folders. Note how the same source files have produced two quite different sets of documents.

Converting output to epub
-------------------------

We need a mechanism to convert the generated XHTML into the final ePub format. Another way of extending
**nanoc** is by adding to the set of command line actions. Create a */commands* folder in the project
root and add something like this:

**/commands/convert_to_epub.rb**

{:.language-ruby}
    require 'eeepub'
    require 'yaml'

    usage       'convert_to_epub'
    summary     'build epub file from output/epub'
    description 'This is a rough and ready post-compile tool to build epub files from content.'

    run do |opts, args, cmd|
      root_dir = File.join(File.dirname(__FILE__), '..')
      config = YAML.load(File.read(File.join(root_dir, 'epub.yaml')))

      nanoc_output_dir = File.join(root_dir, 'output') # TODO get this from *nanoc* config
      file_list = Dir.glob("#{nanoc_output_dir}/epub/*.{html}")

      epub = EeePub.make do
        title       config['meta']['title']
        creator     config['meta']['creator']
        publisher   config['meta']['publisher']
        date        config['meta']['date']
        identifier  config['meta']['identifier']['url'],
                      :scheme => 'URL',
                      :id => config['meta']['identifier']['id']
        uid         config['meta']['uid']

        files file_list
        nav file_list.collect {|f| {:label => "Page #{File::basename(f, '.html')}", :content => File::basename(f) }}
      end

      FileUtils.mkdir_p(File.join(root_dir, config['output_dir']))
      epub_filename = File.join(root_dir, config['output_dir'], config['filename'])
      epub.save(epub_filename)
      FileUtils.rm_rf("#{nanoc_output_dir}/epub/") # remove epub XHTML files created by nanoc

      puts "epub saved to #{epub_filename}"
    end


Again, the code is pretty ordinary - but it servers to demonstrate how we can extend **nanoc** to our own ends.

Run the following from the command line:

{:.language-text}
    nanoc convert_to_epub

The XHTML files in */output/epub/* will have been removed and the packaged version of your .epub file will now be available in */output/book/*. Open the file in [Calibre](http://calibre-ebook.com), or one of the eBook readers available.

You can also look at the content online. From the project root execute the following:

{:.language-text}
    nanoc view

Open a web browser and navigate to *http://localhost:3000*.
Same content, two different representations.

Example Project - This Purple World
-----------------------------------

The sample project used to test this technique is something I started years ago, when I was more vigorously engaged with writing. When I read the first few pages out at a writers' group a little old lady said, "It made me feel sick".

The writing may not be your cup of tea, but the exercise was to see how quickly we could get something built - in the small amount of time I have for coding (moved into management, trying to keep it real).

For the record, at the coffee break the little old lady told me, "I think you were trying to make me sick, so I guess it's effective writing".

You can check out a draft version at [thispupleworld.com](http://thispurpleworld.com). The source code is available on [Github](https://github.com/ferrisoxide/this-purple-world). Bear in mind, this is still very much a work in progress. I've put 5 (out of around 100) pages up. It really needs a decent edit, so I'll put up more as I go through it - hopefully cleaning up the **nanoc** / **eeepub** project in the process as I get to understand both toolkits more throughly.

Next Steps
----------

This really is a quick and dirty project. There are number of ways to improve the product, and I'll look at this in a later (and hopefully shorter) post.

Some things that come to mind:

* Making use of ePub's hierarchical model for publications, separating the content into chapters and pages.
* Replace the "001", "002" page numbering convention with use of **nanoc** meta data.
* Styling ePub documents using CSS
* That neglected *Step 2*: make it pretty


