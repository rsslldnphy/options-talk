<!SLIDE title-slide>
.notes first slide

# Nils - what are your Options? #
## @rsslldnphy ##

<!SLIDE>
# Who am I? #
## Russell Dunphy
## Ruby developer at On the Beach

### (on the internet I drop those cumbersome vowels, and go by @rsslldnphy)

<!SLIDE>
# So, nils? What's the story?

<!SLIDE bullets incremental>
# A 'historically bad decision' #
* The "null reference"

  * invented by Tony Hoare in 1965
  * part of the type system for ALGOL W

* A "billion dollar mistake"?
  * count up all the `undefined method 'foo' on NilClass` errors you've ever got
  * multiply that by all the programmers in the world
  * count that number back to 1965

<!SLIDE bullets>
# Nil is not a null reference #
* In Ruby, everything is an object
* (almost)
* This includes `nil`
* (the only instance of `NilClass`)

<!SLIDE>
# This is an improvement! #

<!SLIDE bullets >
# `nil` v `null`

* `nil` is better!

  * Calling any method on a Java `null` => `NullPointerException`
  * `NilClass` defines some handy methods
  * (mainly for working out if it's `nil` or not)

* But it's still rubbish!

<!SLIDE>
# Why is `nil` so rubbish then? #

So far I've just taken it for granted that `nil` is a bad thing

But there are very good reasons for saying this

<!SLIDE bullets incremental>
# The two types of `nil`#

* Type One - nil as bug

  * Something has gone wrong in your program
  * Something you expect to have a value doesn't
  * You want to crash as soon as possible!

* Type Two - nil as absence

  * The 'empty' state of a value that may not be set
  * eg. a `Person` may or may not be wearing a `Hat`.
  * This is a perfectly valid state for your program


<!SLIDE>
# It's actually even worse... #

<!SLIDE bullets>

# Are these type one or type two `nils`? #

* `{}[:foo]          #=> nil`
* `@blahblahblah     #=> nil`

<!SLIDE>
# Where do type one (buggy nils) come from?

<!SLIDE subsection>
## There's only one place they *can* come from ##

<!SLIDE>

## And that is... ##
## type two nils that have escaped ##


<!SLIDE incremental>
# How type two nils escape #

## Using `nil` leads to lots of defensive coding

    @@@ ruby
    if !current_user.nil? && !current_user.hat.nil?
      puts "They are wearing a #{current_user.hat}"
    end


<!SLIDE bullets incremental>
# Defensive coding is...
* ugly!
* easy to forget to do
* ...and that's how type two nils escape

<!SLIDE incremental>
# and the worst bit...#
## is that the escaped nil might not blow up straight away... ##
### so finding the cause of the bug can be hard ###
(and I don't like it when fixing bugs is hard)

<!SLIDE>
# But it doesn't have to be this way! #

<!SLIDE>
# It turns out there are better ways to represent 'absence' #


<!SLIDE bullets>
# Option types #
* Found in many functional languages, such as ML, F# and Scala
* Incredibly simple, surprisingly powerful
* (and I've written a Ruby implementation if you want to use it)


<!SLIDE bullets>
# What is an Option?
* It's a container
* It either has a value in it, or it doesn't
* And that's it

<!SLIDE>
## Let's look at some code (finally!) ##

    @@@ ruby
    # constructing an 'optional hat'

    optional_hat = Some[:fedora]

    # and the absence of an optional hat

    optional_hat = None

<!SLIDE bullets>
# Using the value of an option

* You can't just use an option as a value
* You have to retrieve its value first
* (And this is a good thing)

<!SLIDE bullets incremental>
# Getting the value of an option

    @@@ ruby
    # the simplest way to get an option value

    Some[:fedora].value # => :fedora

    # will raise an error if there isn't one

    None.value # => raises ValueOfNoneError

<!SLIDE bullets incremental>
# Making sure an option has a value

    @@@ ruby
    if optional_hat.some?
      puts "Wearing a #{optional_hat.value}"
    elsif optional_hat.none?
      puts "Not wearing a hat"
    end

(this looks a bit like that ugly defensive coding though, doesn't it...?)

<!SLIDE bullets incremental>
# Providing a default value

    @@@ ruby
    # sometimes there's an easy default

    hat_desc = optional_hat.value_or "no hat"

    # but sometimes it might not be so easy

    puts "They are wearing a #{hat_desc}"

    # => "They are wearing a no hat"

<!SLIDE bullets>
# Options are containers, remember!
* That means they implement `Enumerable`
* This leads to loads of cool stuff

<!SLIDE bullets incremental>
# Option#map

    @@@ ruby
    optional_hat.map do |hat|
      "It's a #{hat}!"
    end

    # Some[:fedora] => Some["It's a fedora!"]
    # None          => None

<!SLIDE bullets incremental>
# Option#flatten

    @@@ ruby
    [
      Some[:fedora],
      None,
      Some[:trilby],
      Some[:woolly],
      None
    ].flatten # => [:fedora, :trilby, :woolly]

<!SLIDE bullets incremental>
# Option#each #

    @@@ ruby
    optional_hat.each do |hat|
      puts "* doffs #{hat} *"
    end

<!SLIDE bullets incremental>
## This can be useful in ERB... ##

    @@@ ruby
    <%= render 'hat', collection: @optional_hat %>


<!SLIDE>

# Next: Pattern Matching
## But before we move on - any questions?

<!SLIDE bullets>
# What is 'pattern matching'?
* Common in functional languages
* Usually how they interact with Options
* Asserts on value & extracts data from it

<!SLIDE>
# An example of pattern matching

    @@@ ruby
    optional_hat.match do |m|
      m.some { |hat| puts "Wearing a #{hat}" }
      m.none {       puts "No hat here"      }
    end

<!SLIDE>
# You can also assert on content

    @@@ ruby
    optional_hat.match do |m|
      m.some(:fez)    { puts "Bowties are cool!" }
      m.some(:bowler) { puts "Tea, anyone?"      }
      m.none          { puts "No hat here"       }
    end

<!SLIDE bullets>
# Pattern matching in FP
* Tends to be used a lot
* Can extract from arrays & hashes
* Is even used for method dispatch!

<!SLIDE>
# Pattern matching for method dispatch
In ML, methods only ever have 1 arg

'Multi-argument' methods are actually pattern matches over tuples

    @@@ sml
    fun add (x, y) = x + y

<!SLIDE>
## You can usually define multiple versions of a function with different patterns
    @@@ sml
    fun add (x, y) = x + y

      | add (x, y, z) = x + y + z

(Clojure does something similar with multimethods)

<!SLIDE>
## In some languages such as Erlang, you can even add guard clauses!

    @@@ erlang
    safe_divide(X, Y) when Y == 0 -> 0;

    safe_divide(X, Y) -> X / Y.

<!SLIDE>
# Wouldn't it be cool if you could do this in Ruby....??

<!SLIDE>
# Well...

<!SLIDE>
# Introducing Quincunx
* Attempt to bring Erlang style pattern matching to Ruby
* Multiple dispatch
* Guard clauses
* Not recommended for production use!

<!SLIDE>
# Quincunx - an example

## Let's say we have the following classes / structs

    @@@ ruby
    class Drink; end
    class SoftDrink < Drink; end
    class AlcoholicDrink < Drink; end

    class Lemonade < SoftDrink; end
    class WhiskeySour < AlcoholicDrink; end

    Person = Struct.new(:name, :age)

<!SLIDE smaller-code>
# Quincunx - an example

## Now let's define a `Bar`:

    @@@ ruby
    class Bar
      include Quincunx

      # anyone can order a soft drink
      define :serve, [Person, keys: [:name]], SoftDrink do
        "A soft drink? No problem, #{name}"
      end

<!SLIDE smaller-code>
# Quincunx - an example

What if someone shouldn't be allowed to buy alcohol?

    @@@ ruby
    define :serve,
      [Person, keys: [:name, :age]],
      AlcoholicDrink,
      when: ->{ age < 18 } do

      "No chance, #{name}, you're only #{age}!"
    end

<!SLIDE smaller-code>
# Quincunx - an example

But of course, if you're old enough, that's fine

    @@@ ruby
    define :serve,
      [Person, keys: [:name, :age]],
      [Alcopop, as: :drink],
      when: -> { age >= 18 } do

      "Here you go, #{name}, here's your #{drink.class}"
    end

<!SLIDE>
# Recap!
* nils are a bad way to represent absence
* options are *good* way to represent absence
* pattern matching is cool
* it would be awesome if Ruby had it (properly)

<!SLIDE>
# Code!
* Optional - http://github.com/rsslldnphy/optional
* Quincunx - http://github.com/rsslldnphy/quincunx
* Id - http://github.com/onthebeach/id

<!SLIDE>
# Thanks for listening!
## Find me on twitter: @rsslldnphy

<!SLIDE>
## and...
# On the Beach is hiring
## come say hello in the pub!
