<!SLIDE title-slide>
.notes first slide

# Nils - what are your Options? #
## @rsslldnphy ##

<!SLIDE>
# Who am I? #
Russell Dunphy - Ruby developer at On the Beach

(on the internet I drop those cumbersome vowels, and go by @rsslldnphy)

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

<!SLIDE bullets incremental>
# Nil is not a null reference #
* In Ruby, everything is an object
* (almost)
* This includes `nil`
* the only instance of `NilClass`

<!SLIDE>
# This is an improvement! #

<!SLIDE bullets incremental>
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

<!SLIDE bullets incremental>

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
    unless current_user.nil?
      puts current_user.name
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


<!SLIDE bullets incremental>
# Option types #
* Found in many functional languages, such as ML, F# and Scala
* Incredibly simple, surprisingly powerful
* (and I've written a Ruby implementation if you want to use it)


<!SLIDE bullets incremental>
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

<!SLIDE bullets incremental>
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

<!SLIDE bullets incremental>
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
# Option#each #

    @@@ ruby
    optional_hat.each do |hat|
      puts "* doffs #{hat} *"
    end

<!SLIDE bullets incremental>
## This also works in Rails views... ##

    @@@ ruby
    <%= render 'hat',
          collection: @optional_hat %>

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
