---
title: "BASIC Games in Ruby"
created_at: 2013-10-07 17:34
description: This is the description
kind: article
comments: true
layout: post
---

![BASIC Computer Games Ruby Edition](/images/posts/basic-computer-games-ruby-edition.jpg){: class="image right"}

**NOSTALGIA WARNING!!**

Recently I rediscovered my copy of [BASIC Computer Games - Microcomputer Edition](http://www.atariarchives.org/basicgames/index.php), sitting on my oldest son's bookshelf.

<!--READMORE-->

Finding this battered old tome evoked many happy memories; typing reams of ALL-CAPS code into the brand-spanking new [Micromation](http://en.wikipedia.org/wiki/Micromation), an [MP/M](http://en.wikipedia.org/wiki/MP/M) machine my school purchased through a long-running fundraising campaign. This was before I got my hands on a VIC-20, a machine that booted directly into a [Commodore BASIC](http://en.wikipedia.org/wiki/Commodore_BASIC) shell, and lost whole Summer holidays to simple, joyful coding.


BASIC was a big part of growing up with computers for my crowd. Looking back now, it was a terrible first language to learn, and I wonder how much further I would be now if I'd had something like Ruby available to me when I was a kid. With a hint of nostalgia, I decided to sit down with my son and see what a Ruby version of a BASIC Computer Game might look like - if we ported one of these old games to a new language.

Always Something There to Remind Me
---

[David Ahl's](http://en.wikipedia.org/wiki/David_H._Ahl) collection of BASIC Computer Games has a bit of history. Originally intended as an educational tool, it came illustrated with [George Becker's](http://www.bekerbots.com/) wonderful ["Beker Bots"](http://blastprocessing.blogspot.com.au/2009/04/robots-of-george-beker.html), suggestive of a magical, robotised world beyond the code laid bare in Ahl's book.

My own copy has travelled with me, from job to job, from city to city. It was borrowed from Greg S. - a childhood friend with more access to ready-funds than I did, who kept me supplied with books and copies of [Your Computer (Australia)](http://en.wikipedia.org/wiki/Your_Computer_(Australian_magazine)) and inadvertently financed my early eduction in computing. Sadly, I lost contact with Greg over the years and never got to return his book. So it has stayed with me, a pointer to an old place in memory.

Terrible, Terrible Code
---

One thing that stood out re-reading old BASIC code is just how absolutely horrible the language was. [BASIC](http://en.wikipedia.org/wiki/BASIC) - or Beginner's All-purpose Symbolic Instruction Language for the kids who have never seen it without the 'Visual' prefix - was probably the first programming language for old coders like myself who grew up in the 80s.

BASIC taught me the (ahem) basics of variable assignment, looping and conditional statements. It also taught me how to write sloppy, unstructured code.

Unfortunately many of the games collected in Ahl's book reflect some of these bad programming practices, reinforcing [Edsger Dijkstra's](http://en.wikipedia.org/wiki/Edsger_Dijkstra) famous claim that the [Go To Statement (should be) Considered Harmful](http://www.u.arizona.edu/~rubinson/copyright_violations/Go_To_Considered_Harmful.html).


But What If My First Language Was Ruby?
---

I'm slightly envious of my kids growing up with a plethora of really decent computer languages available to them. My oldest knows a little Ruby and my youngest is a keen [Scratch](http://scratch.mit.edu/) programmer. With that in mind I had some trepidation in exposing them to the world of BASIC. Much like showing someone how to rub two sticks together so they can appreciate matches, I sat down with my 13-year old to take apart an old BASIC game - and put it back together in Ruby.

I was surprised how much I remembered BASIC - and how easy it was to explain things to my son ("`print` is just like `puts`"). BASIC's accessibility is really central to its charm. We started with the first game in the book: [Acey Ducey](http://www.atariarchives.org/basicgames/showpage.php?page=2), a simulation of a simple card game where two cards are drawn and the player bets on whether or not the next card will fall between the two.

You can draw you own conclusion on the [original source code](http://www.atariarchives.org/basicgames/showpage.php?page=2). Delving into the mechanism, we came across a curious algorithm:

{:.language-basic}
    260 PRINT"HERE ARE YOUR NEXT TWO CARDS"
    270 A=INT(14*RND(1))+2
    280 IF A<2 THEN 270
    290 IF A>14 THEN 270
    300 B=INT(14*RND(1))+2
    310 IF B<2 THEN 300
    320 IF B>14 THEN 300
    330 IF A>=B THEN 270
    350 IF A<11 THEN 400
    360 IF A=11 THEN 420
    370 IF A=12 THEN 440
    380 IF A=13 THEN 460
    390 IF A=14 THEN 480
    400 PRINT A
    410 GOTO 500
    420 PRINT "JACK"
    430 GOTO 500
    440 PRINT "QUEEN"
    450 GOTO 500
    460 PRINT "KING"
    470 GOTO 500
    480 PRINT "ACE"
    500 IF B<11 THEN 550
    ...
    ...

So...lines 270 to 300 basically loop until the condition of "two cards, with the first being smaller than the first" occurs. "That's just silly," declared my son, "You already know what the first card is. Why can't you pick the second one going from that?" Why indeed. Ah.. kids. Here's our effort:

{:.language-ruby}
    puts "Here are your next two cards:"
    card_a = rand(12) + 2
    card_b = rand(14 - card_a) + card_a + 1

The `PRINT "JACK"`, `PRINT "QUEEN"` pattern is repeated throughout the game, wherever there is a need to convert the number representing a card to a string. This is slightly unforgivable, as even BASIC had the concept of subroutines (the delightfully named `GOSUB`). A rough equivalent in Ruby:

{:.language-ruby}
    def card_to_s(index)
      if index < 11
        return index.to_s
      elsif index == 11
        return "Jack"
      elsif index == 12
        return "Queen"
      elsif index == 13
        return "King"
      elsif index == 14
        return "Ace"
      end
    end

    puts card_to_s(card_a)
    puts card_to_s(card_b)

Even that is a little verbose. It could rewritten without becoming too obscure using Ruby's `each_with_object`:

{:.language-ruby}
    cards = (2..10).each_with_object({}) { |num, hash| hash[num] = num.to_s }
    cards.merge!(11 => 'Jack', 12 => 'Queen', 13 => 'King', 14 => 'Ace')

    puts cards[card_a]
    puts cards[card_b]

My lad and I pursued the port with some simple rules: (a) No comments, (b) No specs. Just like the good old days.

The source code can be found on [Github](https://github.com/ferrisoxide/basic-computer-games-ruby-edition). There's nothing particular special about the `acey-ducey.rb` code, but it is shorter than the BASIC version by about 40%. It's also - in my opinion - orders of magnitude more readable. Here's a snippet:

{:.language-ruby}
    while money > 0 do

      puts "You have #{money} dollars."

      puts "Here are your next two cards:"

      card_a = rand(12) + 2
      card_b = rand(14 - card_a) + card_a + 1

      puts card_to_s(card_a)
      puts card_to_s(card_b)

      valid_bet = false

      while valid_bet == false do
        puts "What is your bet?"
        bet = gets.chomp.to_i
        if bet == 0
          puts "Chicken!!"
          valid_bet = true
        elsif bet > money
          puts "Sorry, my friend but you have bet to much."
          puts "You have only #{money} dollars to bet."
        elsif
          valid_bet = true
        end
      end
      ...
      ...

The output is reasonably faithful to the original:

{:.language-text}
    Acey Ducey is played in the following manner:
    The dealer (computer) deals two cards face up
    You have an option to bet or not to bet depending
    on whether or not you feel the card will have
    a value between the first two.
    If you do not want to bet, input a 0.
    You have 100 dollars.
    Here are your next two cards:
    8
    Queen
    What is your bet?
    10
    The card was 10.
    You win!!!
    You have 110 dollars.
    Here are your next two cards:
    6
    7
    What is your bet?
    0
    Chicken!!
    You have 110 dollars.
    Here are your next two cards:
    Queen
    King
    What is your bet?
    0
    Chicken!!
    You have 110 dollars.
    Here are your next two cards:
    3
    Queen
    What is your bet?

Exciting, eh? No wonder we needed all those pictures of Beker's robots to keep us going.

What Did I Get Out of This?
---

Well.. not much. As much as BASIC gave me my entry into programming I'd never want to go back there again. My son and I planned on working our way through the rest of the book but I was so turned off by the next game ([Amazing](http://www.atariarchives.org/basicgames/showpage.php?page=3), a nest of `GOTO`s that's harder to traverse than the mazes it generates) it felt better for everyone's state of mind to just walk away. We may come back to it again at some stage as there is some absolute gold in Ahl's book.  [Animal](http://www.atariarchives.org/basicgames/showpage.php?page=4), written well, would be a great intro into binary searches. [Orbit](http://www.atariarchives.org/basicgames/showpage.php?page=124) deals with with [The Law of Cosines](http://en.wikipedia.org/wiki/Law_of_cosines) and other fun maths. And [Super Star Trek](http://www.atariarchives.org/basicgames/showpage.php?page=157) is an absolute gem.

To see these old classics recreated in a modern language I'd be willing to put some more time into revisiting the worlds of BASIC. If only it wasn't for all those goddamed `GOTO`s. But there's only so much time available, and I'd rather be coding new stuff with my kids than indulging in my own nostalgic kick-back.

It's time to let go. So Greg, if you want your book back.. just let me know.

