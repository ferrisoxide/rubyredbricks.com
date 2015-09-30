---
title: BASIC Games in Ruby - Animals
date: 2015-09-26 14:00 UTC
tags:
published: true
---

![BASIC Computer Games Ruby Edition](/images/posts/basic-computer-games-ruby-edition.jpg){: class="image left"}

_NB This post is aimed at entry-level coders. It will have limited value or interest to experienced Rubyists._

Introducing "Animal"
-------------------

An [earlier post](/blog/2013/10/07/basic-games-in-ruby/) on converting the BASIC game Acey Ducey to Ruby garnered some great feedback, including [some corrections](https://github.com/chris-hanson/basic-computer-games-ruby-edition/blob/master/acey-ducey.rb){: target="_blank"} as well as inspiring a [port to Haskell](https://github.com/gscalzo/HaskellTheHardWay/tree/master/AceyDoucey){: target="_blank"}.

Inspired to continue working through David H. Ahl's [BASIC Computer Games](http://www.atariarchives.org/basicgames/index.php){: target="_blank"} book from the 70s, I'm turning my attention to "Animal" -- a 20-questions style game where the computer tries to guess the name of an animal you are thinking of:

{:.language-text}
    Animal
    Adapted from a BASIC game from Creative Computing - Morristown, New Jersey.

    Play 'Guess the Animal'
    Think of an animal and the computer will try and guess it.
    Are you thinking of an animal?
    N
    Are you thinking of an animal?
    Y
    Does it swim?
    Y
    Is it a fish
    N
    The animal you were thinking of was a?
    dolphin
    Please type a question that would distinguish a fish from a dolphin
    Is it a mammal?
    For a dolphin the answer would be?
    Y
    Are you thinking of an animal?
    LIST
    Animals I know
    dolphin
    fish
    bird
    Are you thinking of an animal?
    Y
    Does it swim?
    Y
    Is it a mammal?
    Y
    Is it a dolphin
    Y
    Why not try another animal?
    Are you thinking of an animal?

Animal was quite a popular BASIC game in its day. Along with [ELIZA](https://en.wikipedia.org/wiki/ELIZA) it's an example of how simple programs can _simulate_ human-like behaviour - even on the limited computing platforms we had in the 70s and early 80s. It's also not hard to see it as the predecessor for modern web-based games like [Akinator](http://en.akinator.com/). Indeed, the accompanying text in Ahl's book suggests that the program could be reworked to cover different knowledge domains - geography, cell structures... even characters from television and movies. Akinator is a bit more sophisticated than "Animal", but the basic idea is the same.

"Animal" [(source)](http://www.atariarchives.org/basicgames/showpage.php?page=4){: target="_blank"} is actually the third program in the David Ahl's "Basic Computer Games". I had intended on progressing through the book in sequence, but I'm skipping "Amazing" [(source)](http://www.atariarchives.org/basicgames/showpage.php?page=3){: target="_blank"} as the code seems more convoluted than the mazes it produces. We may come back to that program in due course.

Apart from the obvious nostalgia value, the main reason for reviewing these old BASIC games is to be able to talk to my kids over the essentials of coding. After working in management for a decade, and having precious little time to sit down and code with them, I've long been concerned they'd pick up bad habits like I did as a young coder. Indeed, when I was interviewed for my first gig post-management I gave "making sure my kids don't end up as PHP hacks" as the chief reason for returning to coding.

My fears were somewhat realised when it became clear that my kids don't have a strong understanding of object-oriented programming. As such, this post is aimed at an introductory level and covers some of the basics of Ruby programming. It won't have huge value to a seasoned Rubyist, but at a personal level I hope it will remind me of some of the reasons why I love Ruby so much - and why I believe Ruby is an ideal first programming language.

Data Model
----------

The original Animal game stores knowledge of animals in a simple array:

{:.language-basic}
    70 DIM A$(200)
    80 FOR I=0 TO 3
    90 READ A$(I)
    100 NEXT I

It took me a while to remember what this meant, but in BASIC the `READ` command is used to load into memory data that is stored elsewhere in the program - often declared at the end of the rest of the code. In Animal's case, the data is embedded in the second half of the code.

{:.language-basic}
    530 DATA "4", "\QDOES IT SWIM\Y2\N3", "\AFISH", "\ABIRD"

The upshot of this is we end up with an in-memory data model that looks something like this:

{:.language-text}
    A$(0) "4"
    A$(1) "\QDOES IT SWIM\Y2\N3"
    A$(2) "\AFISH"
    A$(3) "\ABIRD"

Already we are seeing some of the limitations of the program. The BASIC version of Animal can only store 199 records - with the first element in the array being used to maintain a count of the questions and animals. This was fine in the days of VIC-20s and the like, but we're not so constrained with today's computing platforms.

The data model is also rather dense, with the questions and paths to other questions (the `\Y2\N3` part of each question) packed into strings that have to be reinterpretted by the program itself.

Happily we can unpack the BASIC data model into Ruby objects with a clearer and cleaner intent, breaking it into the blocks of code - _i.e._ classes - that make sense.


{:.language-ruby}
    class Animal
      attr_reader :name

      def initialize(args)
        @name = args[:name]
      end
    end

    class Question
      attr_reader :text
      attr_accessor :yes_path, :no_path

      def initialize(args)
        @text     = args[:text]
        @yes_path = args[:yes_path]
        @no_path  = args[:no_path]
      end
    end

Our `Animal` class represents an animal: a bird, frog, baboon, _etc_. It is our _definition_, as far as the game is concerned, of what an animal should look like. Similarly our `Question` class captures the elements of a question: its text and where to take the user next based on a yes/no answer.

Now we have our new data model we can build the initial state of the program as Ruby objects:

{:.language-ruby}
    def starting_position
      @starting_position ||= Question.new(
        text: "Does it swim?",
        yes_path: Animal.new(name: 'fish'),
        no_path:  Animal.new(name: 'bird')
      )
    end

The notion of a 'position' is used throughout the Ruby version, with the variables `current_position` and `previous_position` used internally to keep a track of where the user is in the tree as they progress through the tree.

Calling `starting_position` generates a tree-like structure representing all the knowledge program holds about animals.

{:.language-text}

                Question: Does it swim?
                           /        \
                         /            \
                   [yes path]       [no path]
                      /                   \
                    /                       \
                Animal: Fish             Animal: Bird

As the user adds more knowledge the tree gradually expands:

                Question: Does it swim?
                           /        \
                         /            \
                   [yes path]       [no path]
                      /                   \
                    /                       \
            Question: Is it a mammal?    Animal: Bird
                   /        \
                 /            \
           [yes path]       [no path]
              /                   \
            /                       \
        Animal: Dolphin         Animal: Fish

Side note: I considered drawing this tree as a graphic, but somehow using old-style ASCII just appeals. That old nostalgic feeling again.

Remininscing on how my friends and I learnt programming as kids, we spent a lot of time _reading_ code - and our spry little minds could tease out the data structures and flows despite the `GOTO` spaghetti. Ahl's book packs in almost 100 example programs and so has little space for explanation of the internals of each. This, coupled with the mechanical activity of typing in each program (there were no downloads back then), meant that we had to get a good idea of the purpose of each line of code and how it related to the rest of the program.

The original draft of this post went into great detail about classes and objects, how to pass parameters and so forth. Given the value in reading code - it still takes up a large chunk of what I do as a day-to-day coder - I'd encourge my sons and other interested parties to read the code.

The full source of the Ruby version of Animal is available on [Github](https://github.com/ferrisoxide/basic-computer-games-ruby-edition/blob/master/animal.rb){: target="_blank"}


Recursing the Tree
------------------

At the start of each run through the game, the user can type the 'LIST' command to retrieve all the animals the program currently knows about.

When I talked over this part of the program with my eldest, we came up with a variety of ways to list the animals. Initially we considered having a separate array of `Animal` objects, but this would have required more code to maintain the list - both in the initial setup and as the game progresses.

Given that the data we need is already in the tree, we settled on simply 'walking' through it and finding all the animals.

{:.language-ruby}
    def list_animals
      puts 'Animals I know'
      animals = collect_animals(starting_position, [])
      animals.each do |animal|
        puts animal.name
      end
    end

    def collect_animals(position, animals)
      if position.is_a?(Animal)
        animals << position
      else
        animals = collect_animals(position.yes_path, animals)
        animals = collect_animals(position.no_path, animals)
      end
      animals
    end

The `collect_animals` method is a recursive algorithm. In my experience a small number of otherwise good professional coders don't understand recursion very well, and will happily brute force their way through a data structure. Recursion is an important concept in coding and to this end it's probably worth deconstructing how `collect_animals` works.

The method takes a `position` - a node in our tree - and a list of animals as an array. The `list_animals` method kicks this off by calling `collect_animals(starting_position, [])`, passing in the start of our tree and an empty array. This empty array will act as an 'accumulator', filling up with `Animal` objects as we find them.

The first thing we check for in `collect_animals` is our end point - finding an Animal object at the end of a branch. It adds the animal to the `animals` array and then returns the accumulating array.

If we don't find an `Animal` (_i.e._ the `position` is a `Question`) then we follow each yes or no path, continually calling `collect_animals` until it finds terminating `Animal` objects.

All in all, recursing through the tree like this is a more satisfactory solution than maintaining a separate list of `Animal` objects. Obviously with a very large tree this could take some time, but it's not an issue at the moment. And in that we have another important lesson for young coders: don't complicate your code solving problems you don't currently have (otherwise known as the [YAGNI - You Aint Gonna Need It - principle](https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it){: target="_blank"}).


Protecting Input Using Ruby Blocks
-----------

The original Animal BASIC code does little to check input and it's very easy to scramble the program by entering in bad data - or even just hitting return. My son and I decided to try and prevent bad data entry as much as possible, trapping empty responses or input that didn't make sense.

Doing this we found ourselves writing the same sort of code over and over, and I took it as a good moment to talk to him about reusing code via Ruby's blocks.

Our basic `get_input` method takes a `prompt` argument as text to present to the user.

{:.language-ruby}
    def get_input(prompt)
      is_valid = false

      while !is_valid
        puts prompt
        input = gets.chomp
        is_valid = !input.empty?
        is_valid = is_valid && yield(input) if block_given?
      end

      input
    end

The method loops around while `is_valid` remains false. A blank response from a user (_e.g._ hitting the carriage return) will always mark the input as invalid. If a block is passed to the method (tested with `if block_given?`) the block will be called to test if the input data is valid (returning a `true` or `false`).

In the `get_yn_answer` method, calling the `get_input` will loop until the user enters a 'Y' or 'N' (or 'y' / 'n') response to the prompt.

{:.language-ruby}
    def get_yn_answer(prompt)
      get_input(prompt) do |input|
        input.upcase == 'Y' || input.upcase == 'N'
      end.upcase
    end

In `prompt_to_start` the user can't continue through the program until a 'Y' (or 'y') response has been entered.

{:.language-ruby}
    def prompt_to_start
      get_input "Are you thinking of an animal?" do |input|
        case input.upcase
        when 'Y' then true
        when 'LIST' then
          list_animals
          false
        else
          false
        end
      end
    end

The `prompt_to_start` also allows the user to request a 'LIST' of existing animals. Arguably calling the `list_animals` method potentially creates a side-effect and normally I'd discourage kids from this style of coding. But as the purpose is to manage the flow it's a reasonable compromise here.

This is quite a limited use of Ruby's block idiom, but the idea was to give my kids a taste of how sections of code can be reused.

Going Forward
-------------

The main game code is still quite procedural - it _feels_ like a BASIC program that's been converted to Ruby, albeit with some minor additions. There are probably things that can be done to make the code more Ruby-like.

Working on Animal together, my son and I managed to cover blocks, classes, tree structures and recursion. There were probably more informal lessons along the way - in just being able to sit down and talk code with my kids - but that's a reasonable take-away.

The question now is whether my kids take any of this on and do more coding of their own. They've both expressed a desire to get into software development as a career, but it doesn't feel like they have the same opportunities to learn their craft -- to do the 10,000 hours that I did as a kid -- with the constant distractions of the internet and games.

If I had my time again I'd find a way to record more of the conversations we had while coding.

I plan on taking more time to code with my kids, to talk through design and the sorts of compromises you always have to address even when building small applications.

As long as they end up doing what they want, what they enjoy... hell, I'd even be happy if they end up coding in PHP.

Feel free to look over the code for [BASIC Computer Games (Ruby Edition)](https://github.com/ferrisoxide/basic-computer-games-ruby-edition){: target="_blank"}, make comments or criticism.

Any feedback is appreciated. Thanks for reading.

