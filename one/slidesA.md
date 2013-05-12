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
# This in an improvement! #

<!SLIDE bullets incremental>
# `nil` v `null`

* `nil` is better!

  * Calling any method on a Java `null` => `NullPointerException`
  * `NilClass` defines some handy methods (mainly for working out if it's `nil` or not)

* But it's still rubbish!

<!SLIDE>
# Why is `nil` so rubbish then? #

So far I've just taken it for granted that `nil` is a bad thing

But there are very good reasons for this

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

<!SLIDE bullets incremental>
