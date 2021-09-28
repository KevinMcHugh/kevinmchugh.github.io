---
layout: post
title:  "8 Years of Ruby"
date:   2021-09-28
tags: programming
---

At my new job I'm going to be writing golang. This will be a change for me. I've been writing ruby since some time in 2013. It's bittersweet for me. I've been writing ruby for the length of high school and college combined. I know there are things I will miss, and I know I'll probably do a lot of my hobbyist development in ruby still. It's just so productive and fun to write. Here though, are some thoughts on ruby after nearly a decade of using it.

Enumerable is the greatest API in the language. There's always a lot of ways to do common tasks in ruby, and you should do the most idiomatic thing. Enumerable provides a lot of the idioms.

`tap` is great. It took me 3 or 4 times of really looking at it to understand when to use it. So I'll try to explain it a couple ways and give a couple common use-cases. Use tap when you want to do something with a value just before returning it. Use tap when you don't want to declare a local variable for a value that's going to be returned. When you want to log a value, tap is a good way to express that. tap is great for adding a binding.pry to a method without affecting its return value.

The REPL is your best friend. pry in particular has all kinds of useful features and it's very possible to play with code, build up to an expression or a method that you'll need, then copy and paste it into the file that you'll actually add to source control.

Avoid metaprogramming. You don't have type guarantees, it can be hard to find where things are implemented. `grep` is my most trusted tool for finding things in a ruby codebase, and if I can't find something with grep I have to look really hard.

If you're metaprogramming, write lots of tests.

If you encounter or write difficult code, comment it, then use your comments to rewrite the code so the comments can be deleted. Sometimes comments explain what a particularly twisted method does - those can usually be used to reimplement in a more straightforward manner. Comments can lie, but code can't. You know that whatever code was written, whatever its intention, that's what's running. If I'm trying to debug complicated code, I assume the comments might not perfectly describe the action of the code. Spotting a difference between the meaning conveyed by the comments and the actual functioning of the code can reveal bugs. You usually can't tell if the comments convey intention (what the code is supposed to do) or the code as its understood. For example:

```ruby
  x = foo % 2 ? "bar" : "baz" # sets x to 'bar' if foo is odd, else sets it to 'baz'
```

This is a real example someone gave me once for a good use of comments. It's not a strawman. If you encounter this line of code somewhere, you don't know if the comment is wrong or the implementation is. There's good advice that says to write "why" comments and not "how" comments. This is a how comment, a wrong one. While `% 2 ` in a ternary is a pretty normal way to check even/oddness of a value, you can make your code more straight-forward and less necessary of a comment:

```ruby
  foo_description = foo.even? ? "bar" : "baz"
```

We still don't know _why_ this has to happen - `foo` is pretty opaque and we have no more context - but the intent here is so much more obvious. You don't need a comment, and even if you went to write one you couldn't get it wrong. The code describes its purpose.

The bigger point here is that method names which communicate your intent make your code easier to understand. Another reason I love Enumerable. Sure, you could write the following, but it requires reading all 3 lines to learn what's happening:
```ruby
  descriptions = []
  response_bodies.each { |body| descriptions << body[:description] }
  descriptions
```
(you could even write the looping mechanism yourself, ruby has `for` loops, but I can't remember ever using them when `each` and all of `Enumerable` was available)
```ruby
  response_bodies.map { |body| body[:description] }
```

When I see `map` I know what you're doing - you're getting an array of values derived from the values you've called `map` on. When I see `reduce` I know you're combining a bunch of values into a single value. Even though it takes twice as many iterations, I greatly prefer:
```ruby
  shopping_cart_items.map(&:cost).reduce(:+)
```
to a faster, manual iteration over the values with a for or each loop.

You can always apply this idea - always use the most specific method. `Array#join` is just `reduce` but specifically for string concatenation. 

This isn't a matter of DRY, or "don't reinvent the wheel". I know we can write `each` loops that do the same thing as the `map`. I know we can use `reduce` to `join`. This is about communicating intent faster. We spend so much time reading other peoples'* code that idiomatically communicating the purpose of each line of code is hugely helpful. 

* The you of 6 months ago counts as a different person
