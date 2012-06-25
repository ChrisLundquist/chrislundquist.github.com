---
layout: post
title: "everything you need to know to migrate from ree 1.8.7 to 1.9.3"
category: Ruby On Rails
tags: [Ruby Rails REE]
---
{% include JB/setup %}

# Too Long Didn't Read
Most of your time will be spent updating gems to 1.9 versions then going through
and replacing things like String#each with String#each_line
* YAML use  YAML::ENGINE.yamler = 'syck'
* Use stdlib where possible
* Time#parse
* UTF-8 encode everything
* next instead of return is syntax error now instead of Segfault
* Rspec tests ending in Group
* Nil, True, False classes are frozen, can't dup them
* "when :", kill it
* Rails 2.3.14 gotchya with 1.9.3 loading helpers
* Range#include? -> Range#cover?
* String#each -> String#each_line
* Enumerable#choice -> Enumerable#sample

## Marshalling params or BigDecimal with YAML

One of the more difficult things to track down was a bug in marshalling using
the default serializer, yaml, and its default yamler.

The problem was that whenever you tried to to_yaml or Marshall#dump
you got a big nasty error saying something like

"example of BigDecimal wrong number arugments 2 for 0"

Here was the fix I implemented in config/boot.rb
<pre>
 # FIXME remove the below hack when we at rails 3
 # http://answerpot.com/showthread.php?2494452-Error+With+BigDecimal+To_yaml
 # http://stackoverflow.com/questions/6530412/cant-define-singleton-method-encode-with-for-bigdecimal
  if RUBY_VERSION.to_f == 1.9
    require 'yaml'
    YAML::ENGINE.yamler = 'syck'
    # Set up gems listed in the Gemfile.
    gemfile = File.expand_path('../../Gemfile', __FILE__)
  end
</pre>


## Let Dead Gems Die, Use Stdlib
We were using several gems that were old and designed for 1.8.x
For the most part the fixes were going through and removing colons after when
statements or adding the
<a href="www.example.com"> magic UTF-8 encoding tag</a>.
However, one particularly nasty
thing was the
<a href="www.example.com"> ruby-aes-optimized</a>.
It broke in 1.9.x because string[n] now returned
a character "A" instead of the ascii value 65. It was also basically some C example
implementation that was regexped into ruby. As such we found out later that it didn't
quite agree with how OpenSSL did things. After talking about it briefly, we looked at
<a href="www.example.com"> ruby-fast-aes </a>,
but saw that it only did weak encryption. We figured the best way
to get this done would be to write ruby bindings to OpenSSL's implementation.
Coincidentally, someone had done just that in the <a href="www.example.com"> Stdlib </a>.

## Time...
In short...
https://gist.github.com/873699

## SystemTimer, It's Dead Jim
Because of how 1.8.7 used <a href="www.example.com">Green threads</a> you had to
use a gem to gem real interrupts. In Ruby 1.9.x the theading model is changed
so this is no longer required. You can now use
<a href="www.example.com"> Timeout::timout(seconds) </a>

## Ruby 1.9.3 and Rails 2.3.14 includes all the helpers!
Another sticking point that almost made us stick with ruby 1.9.2
was that there is an issue with trying to load helpers for every controller
when ApplicationController says helpers :all. I poked at it a few times
but was focused on the 1.9.2 issues. Fortunately as soon as I was ready with 1.9.2
<a href="www.example.com">Mr.Arko published just the fix</a>

## next instead of return is syntax error now instead of Segfault
Sometimes when refactoring code I have forgotten to remove a next or
left in a return. In ree-1.8.7 this would cause a segfault when you reached
the line. Now fortunately it will be a syntax error.

## Rspec Tests Ending in Group
We had a UserGroup model, so naturally we had user_group_spec.rb. In 1.8.7 this
worked just fine. However in 1.9.x it tried to run it as a shared_behavior
and blew up with some rather unhelpful stack trace. I ended up needing to look
at the rspec source. I renamed the spec from describe UserGroup to describe User
and that fixed it. ( I felt dirty so I wrapped it in a context ).I might hunt for a
better solution later.

## Nil, True, False classes are frozen
This will only effect tests for the most part when you try to mock out nil objects.
The easy fix is to mock one level lower too. Or create the data in a before / let block
depending on what the data is.

## Method changes
Range dropped include? for some reason and moved it to covers?
it is important not to cast it to an array. Especially with time objects.
Otherwise it will do a linear search through a giant array.
*This change is not backwards compatible, you will have to switch
on Ruby 1.9 / Ruby 1.8 and use the correct behavior*

String#each -> String#each_line. On the plus side this guy is backwards
compatible. I would guess the change was made because of the principal of
least surprise. One might think String#each would yield characters.

Enumerable#choice -> Enumerable#sample. Both are only decent names.
It returns a random element from the collection.
*This change is not backwards compatible, you will have to switch
on Ruby 1.9 / Ruby 1.8 and use the correct behavior*

