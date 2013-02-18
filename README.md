## Monads without cheating and without too much pain.

People argue that monads are complex because they take a long time to explain and are mathematical and if they were
practical and useful then their usefulness would be readily apparent. Some people address this by trying to impart a
somewhat incomplete understanding of them by glossing over some of the more theoretical things about them, but I think
that this is a disservice to those of us who are patient enough to read a more holistic treatment of them. Unfortunately
holistic treatments tend to be written more in math than in english. I'm trying to strike a balance here. This article
would be a lot shorter if I assumed fluency in functional programming but instead I assume fluency in c-family programming
and that is who this article is targeted at.

###Monad the Ultimate!

Fans of functional programing have long held [lambda to be The Ultimate](http://lambda-the-ultimate.org). They were
right about lambda being the ultimate for computation. But they were wrong about it being the ultimate for computers.
This is because computers do more than compute. They also are instruments. A great example of how a computer is
an instrument is that it has a screen and a keyboard and mouse or touchscreen that humans use to interact with them, and 
they have wires and wireless networking capabilities so that they can interact with eachother ad hoc and asynchronously.
So they do a whole heck of a lot of computing, but they do more. And so lambda is almost the ultimate, but not quite.
Something else is, and that something, my friends, is The Monad!

###How is Monad The Ultimate?

As an armchair physicist I'm aware that physics is hoping for a grand unifying theory i.e. string theory, etc. Physicists
are theoretical sorts of folks, so this idea of a grand unifying theory that encompasses their many schools is something
they eagerly await with a sense of anticipition. I've found, though, that few programmers are the theoretical sort of
folks and instead look at theories such as lambda calculus, and monads with a sense of hesitation or confusion or
resistance. Which is really a shame because programmers use abstraction every day. They're abstractioners more than
they're programmers because abstraction is necessary to get real world stuff done. If they were sitting at their machines
writing assembly code they woludn't get done all the amazing things that they get done. They embrace abstraction, and
abstraction is fundamentally theoretical. Programmers might call this theory intuition or time and experience, but
they're really doing themselves a disservice in doing so. They've mastered theories through perseverence and patience
but some have avoided embracing this process. Even to the point that I see a huge trend in talking about monads in
non-theoretical terms, but rather in trying to gain an intuition and seeing where they've used them already, etc. These
are great, but they ought to be supplimented with theory, otherwise the beauty of monads isn't fully realized. This guide "feet on the ground" guides.

But let me back up. There are of course, lots of peolpe who love computer science theory. These people even have their
own languages, like Haskell. There are, in a sense, two schools. We can call these schools the Turing School and the
Church School. Before there was this ubiquitous access to computers there was computation. Turing liked to think about
computation by imagining the workings of a machine that was capable of computing. Alonso Church, on the other hand took
a more mathematical approach and developed Lambda Calculus to help him think about computation. Both were interesting
and equally powerfull ways in deciding computability, however some would argue that Church provided a superior formalism.
It allowed us to think and reason about computability in a way that was easier to abstract and reason about.

The Turing school has occupied more programmer mindshare. The reason for this is that it's proven to be so practical. 
Computers are machines that do more than compute, after all. When you embrace their machine nature you've traditionally
been able to get things done in a superior way - especailly in times when computers were just a fraction as powerful
as they are today. C is very close to the metal and C is everywhere. Linus Torvals wouldn't have gone too far had
he attempted to implement linux in, say lisp, or other languages that fell out of the lambda/Church school. Of course,
the best C programmers are aware of lisp and haskell and their code is better for it. They utilize that theory to write
code that's safer and easier to reason about.

So what we're going to see about the Monad is that it provides a bridge between the two schools. This should be readily apparent just seeing the use cases that motivated the Monad. Monads come to us via the world of Haskell. They allowed Haskell, an otherwise "pure" "lambda" type of language, to do more than just compute. They allowed a way to murge the world of computers as an instrument into the world where lambda is the ultimate way of thinking about computation. They are the grand unifying theory that can and probably will bring together the two schools in a post-divided world. The future will not be divided. It will be a Turing/Church world with the Monad being the grand unifying theory.

###Computing is all about function composition

This might not be obvious to people who have little experience with the functional paradigm, but computing is essentially function composition. Gven that prtety much all do is right functions that call a bunch of other functions it probably doesn't even take a functional programming guru to make this leap. However, a basic overview of 'functions as data,' and their dynamic composition should serve as a useful exercise in really understanding how much function composition really is at the heart of computation.

Let's write a function called clockplus that does addition on a 12 hour clock, the formula is bascially:

```java
Int clockplus(Int a, Int b) { (a + b) % 12; }
```

It doesn't take long to realize that hidden among this formula is function composition. The operators `+` and `%` are
really just functions, after all, right? So it becomes obvious we're dealing with composition when we write `+` and `%`
like we do functions rather than in infix operator notation:

```java
Int clockPlus(Int a, Int b) { %(+(a, b), 12); }
```

There's a little bit of funniness going on with our composition. We're not strictly composing `%` and `+`, we're, in fact
composing `%` partially applied with `+`. That "partial application" can be named `modulo12`. What do I man by this? 
Well, function composition is cleanest when we think of a function that only takes one input.

```java
Int modulo12(Int n) { %(n, 12); }
Int clockPlus(Int a, Int b) { modulo12(+(a,b)); }
```

Now we can really see some serious composition taking place! We're revealing the true structure of our program in terms of functions, and remember functions are the fundamentally good idea when it comes to reasoning about computation.

Beautiful things happen when you think of functions as data.

So we revealed how formulas are function composition using typical c-ish notation. But the full beauty of how this all fits together can't really be enjoyed until we start thinking of functions as data. Let's do this be saying a function has
a type:

```java
class Function { Int apply(param: Int) = ... }

Function f = ...

f.apply(3);
```

When you think of types as classes this is what you get, a class that can be instantiated and passed around as on object that has one method named `apply`. That's applied just how functions are. This is a horrible amount of indirection and needless ceremony So let's just change are syntax and think of a function type as

```haskell
f: Int -> Int
```

There we've distilled it down to its essence! And from now on let's put all of our type annotations after instead of
before our values. This is just a syntactic difference, no big deal. We're making use of haskell-ish syntax since it does a really great job of describing functions and function types. You don't need to understand Haskell to follow along, and it might not even be working Haskell, but it's a haskel-esque pseudocode with explanation.

Now that functions are just data, we can actually write a function to compose functions, let's call this function `.`

```haskell
. :: (Int -> Int) -> (Int -> Int) -> (Int -> Int)
. f g = \x -> f(g(x))
```

The first line describes the type of `.` and the second line describes its implemntation, it says `.(f, g) { return x => f(g(x)) }`. The return value is an anonymous function that takes an `x`, gives it first to `g`, and gives the result of `g(x)` to `f` and returns the result of `f` applied to that result. As you can see, when writing functions in haskell, the parameters just follow the function name separated by spaces. Not too bad, right?

`.` is a function that takes `f` and `g` and returns some `h`, its composition.
+ `f: Int -> Int`
+ `g: Int -> Int`
+ `h: Int -> Int`, such that `h` is `(f . g)` i.e. `f` composing `g` i.e. `f(g(x))` for some `x` (`.` uses infix notation).

Note that `.` not only takes two functions but it returns one, so `(f . g)(5)` could be seen as "compose these funcions and invoke it with the argument `5`."

We'll call `x` a point. Which is itself a little noisy. In functional programming we clean things up even more
by getting rid of these points. But before that we need to understand that when we start to think of functions as data
that functions really only ever need one argument. What? But we've already seen several functions that have two arguments
like `+`, `%`, and even `.`, our function composition function. Ok, well, bare with me for a second as we look at `+.`

`+(a: Int, b: Int)` doesn't look much different than `+(a: Int)(b: Int)`

By grouping `+`'s parameters like this and ignore `+`'s implementation we see that we can think of `+` as taking an int
and returning a function that takes and int and returns an int. This is just like before with `.` which returned
a function... so remember our example invocation of `(f o g)(5)`? We'd just invoke `+` similarly, i.e. `+(3)(4)`. Even the
parens become superfluous at this point, because we're just chaining single argument function invocations together:
`\+ 3 4`

Now we can change a pointed function definition to a point free definition, we no longer need that point x.

pointed:

```java
Int plus3(Int x) { +(3)(x) }
```

```haskell
plus3 :: Int -> Int
plus3 x = + 3 x
```

unpointed:

```java
Function plus3() { +(3) }
```

```haskell
plus3 :: Int -> Int
plus3 = (+ 3) -- (remember, + 3 returns a function!)
```

At this point this synergy between thinking about function parameters either as multiple parameters, or a chain of one argument function calls ought to start working in your favor, given the context. This trivial transformation between the two ways of thinking can be called currying. I like to curry my functions and my potatos, both are pretty tasty.

Let's go back to our clock arithmetic and try to designate a point free version.

+ recall our pointed version: 
  + `Int clockPlus(Int a, Int b) = %(+(a, b), 12)`

We have an impediment, the second, not the first, argument of our modulo operator is to be partially applied. 'Functions as data' to the rescue! Simply imagine a function that takes a function and returns that function but with its two parameters flipped, we'll call it `flip`! `flip f  = (\x, y -> f(y, x))` .

So our point free definition becomes:
+ `clockPlus = . (flip % 12) (+) --remember . is our operator that composes two functions`

`clockplus` is the composition of `+` with a flipped modulo partially applied with `12`.

Note that points aren't evil. They're perfectly nice. Some things are nice to read with points, other things get at the crux of the matter better by trying to eliminate the points. Your taste and style will tell you when points are appropriate. Remember when even functions can be values even functions can be points. One rather interesting, and surprising, and in my opinion, mindblowing thing is you can do point-free recursion! That's right, you can call a function from inside that function without ever giving that function a name. It's a crazy curiosity, I suggest you check out [the 'y-combinator'](http://en.wikipedia.org/wiki/Y-combinator#Y_combinator) for the most well-known example of this.

Awesome! So in conclusion we've explored 2 important things: An introduction to functional programming idioms/vocab and an
insight that pure computational programming is all about functional composition, and not much else. (Again we have
the important note that computers do a whole lot of computation but they do more than that.)

###Just a tiny bit of abstract algebra, no big deal!

Abstract algebra is a pretty cool bit of mathematics that everyone has some level of familiarity with just by virtue
of keeping track of time, and money. It sort of tries to find algebraic patterns in things. For example:

+ commutativity: a + b == b + a  //true in both clock math (modulo 12) and regular arithmatic.
+ associativity: a + (b + c) == (a + b) + c //again true in both clock math and regular arithmatic.
+ existence of zero: (regular arithmatic: a == (0 + a) == (a + 0)), (clock math: a == (12 + a) == (a + 12))

Interesting that in clock math zero is 12. For that reason it can be less confusing to call 0/12 the identity element. i.e.
a + identity is a and identity + a is a. It's the thing that does nothing under the operation.

+ Existence of inverses - Another neat algebraic pattern is the existence of inverse elements. we call a' the inverse of a, and say that
(a + a') == (a' + a) == identity

+ in arithmatic: (-3 + 3) == (3 + -3) == 0
+ in clock math: (8 + 4) == (4 + 8) == 12

When all these things hold we call what we're looking at an abelian group. When all hold except for commutativity, we call
it a group. When we ditch the existence of inverse elements for every element we have a monoid. Finally, we can even ditch
identity, in which case we have a semigroup.

"abelian group," "group," "monoid," and "semigroup" are labels we can all apply to our arithmatic and our clock math because
all of our laws hold. But let's look at functions instead of numbers, and function composition instead of addition. We do
this because, as we've already established, function composition is the key to computation. First let's stop writing
Int, since this applies to functions that operate on any data, instead we'll write T for type.

let f, g and h be functions from T to T and * be function composition:
+ f: T -> T
+ g: T -> T
+ h: T -> T
+ *: (T -> T) -> (T -> T) -> (T -> T) => *(f, g) = x -> f(g(x)) //do g, then pass its result to f

*, like +, is a binary operator and we'll write in infix accordingly, i.e. f * g

Intuitively we sort of know that commutativity doesn't hold for *, but associativity does. Proving this is fairly
straight forward, but more math then we need to get into.

So we know that commutativity is gone, so we're not dealing with an abelian group.

What about identity? How about the function that does nothing to its input? i.e. id(x)=x, that seems like a good candidate
for identity.

+ id * f == id(f) == f
+ f * id == f(id) == f

Ok, cool, so we're dealing with more than a semigroup. We have either a monoid or a group.

Identity function is probably starting to sound familiar from algebra, and so too, is the idea of a function's inverse..
let f(x) = x + 3, and g(x) = x - 3, f(g(x)) = g(f(x)) = id(x) = x. You may recall that some functions have inverses
but others don't. the constant function f(x) = 3, for example is not invertible.

So the answer is that for function composition we have a group on the set of invertable functions and a monoid on the set
of all functions.

Why does this matter? Well we have mathematical truth and beauty built into composing computation. Monoids have
been extensively studied, and are an interesting thing. The also are things we already intuitively know that
we've formalized behind a more theoretical framework. This is nice because now we're fairly close to being able
to do the same thing with a Monad without it getting too scary, since this is just stuff you're already somewhat
familiar with.

One note about Category theory. Turns out you don't really need to worry about category theory unless you write compilers.
As a programmer you just do what you've always done, make sure your types match up. What I mean by that, is that
category theory comes into play once we start dealing with functions from one type to another.

+ f: B -> C
+ g: A -> B
+ h: A -> C, let h = f * g

We know that the output of g's type must match the input of f's type, as long as that's the case we can go ahead and
compose as we normally would when only dealing with functions on one type. That's all you need to worry about, just know
that mathemtacially we have to be more sophisticated than introductory abstract algebra, and enter the theory of categories.

