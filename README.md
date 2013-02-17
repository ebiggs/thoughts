thoughts
========

Monad the Ultimate!

Fans of functional programing have long held [lambda to be The Ultimate](http://lambda-the-ultimate.org). They were
right about lambda being the ultimate for computation. But they were wrong about it being the ultimate for computers.
This is because computers do more than compute. They also are instruments. A great example of how a computer is
an instrument is that it has a screen and a keyboard and mouse or touchscreen that humans use to interact with them, and 
they have wires and wireless networking capabilities so that they can interact with eachother ad hoc.
So they do a whole heck of a lot of computing, but they do more. And so lambda is almost the ultimate, but not quite.
Something else is, and that something, my friends, is The Monad!

How is Monad The Ultimate?

As an armchair physicist I'm aware that physics is hoping for a grand unifying theory i.e. string theory, etc. Physicists
are theoretical sorts of folks, so this idea of a grand unifying theory that encompasses their many schools is something
they eagerly await for with a sense of anticipition. I've found, though, that few programmers are the theoretical sort of
folks and instead look at theories such as lambda calculus, and monads with a sense of hesitation or confusion or
resistance. Which is really a shame because programmers use abstraction every day. They're abstractioners more than
they're programmers because abstraction is necessary to get real world stuff done. If they were sitting at their machines
writing assembly code they woludn't get done all the amazing things that they get done. They embrace abstraction, and
abstraction is fundamentally theoretical. Programmers might call this theory intuition or time and experience, but
they're really doing themselves a disservice in doing so. They've mastered theories through perseverence and patience
but some have avoided embracing this process. Even to the point that I see a huge trend in talking about monads in
non-theoretical terms, but rather in trying to gain an intuition and seeing where they've used them already, etc. These
are great, but they ought to be supplimented with theory, otherwise the beauty of monads isn't fully realized.

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

So what we're going to see about the Monad is that it provides a bridge between the two schools. This should be readily
apparent just seeing the use cases that motivated the Monad. Monads come to us via the world of Haskell. They allowed
Haskell, an otherwise "pure" "lambda" type of language, to do more than just compute. They allowed a way to murge the
world of computers as an instrument into the world where lambda is the ultimate way of thinking about computation. They
are the grand unifying theory that can and probably will bring together the two schools in a post-divided world. The
future will not be divided. It will be a Turing/Church world with the Monad being the grand unifying theory.

Computing is all about function composition
===========================================

Let's write a function called clockplus, the formula is bascially:

Int clockplus(Int a, Int b) = (a + b) % 12

It doesn't take long to realize that hidden among this formula is function composition. The operators + and % are
really just functions, after all, right? So it becomes obvious we're dealing with composition when we write + and %
like we do functions rather than in infix operator notation:

Int clockPlus(Int a, Int b) = %(+(a, b), 12)

There's a little bit of funniness going on with our composition. We're not strictly composing % and +, we're, in fact
composing % partially applied with +. That "partial application" can be named modulo12. What do i man by this? 
Well, function composition is cleanest when we think of a function that only take one input.

Int modulo12(Int n) = %(n, 12)
Int clockPlus(Int a, Int b) = modulo12(+(a,b))

Now we can really see some serious composition taking place! We're revealing the true structure of our program in
terms of functions, and remember functions are the fundamentally good idea when it comes to reasoning about computation.

Beautiful things happen when you think of functions as data.

So revealed how formulas are function composition using typical c-ish notation. But the full beauty of how this all fits
together can't really be enjoyed until we start thinking of functions as data. Let's do this be saying a function has
a type:

class Function { Int apply(param: Int) = ... }

Function f = ...

When you think of types as classes this is what you get, a class that can be instantiated and passed around as on
object that has one method named apply. That's applied just how functions are. This is a horrible amount of indirection
So let's just chaneg are syntax and think of a function type as

f: Int -> Int

There we've distilled it down to its essence! And from now on let's put all of our type annotations after instead of
before our values. This is just a syntactic difference, no big deal.

Now that functions are just data, we can actually write a function to compose functions, let's call this function 'o'

o(f: (Int -> Int), g: (Int -> Int)): (Int -> Int) = h(x: Int): Int = f(g(x)) 

o is a function that takes f and g and returns some h, its composition.
f: Int -> Int
g: Int -> Int
h: Int -> Int, such that h is (f o g) i.e. f composing g i.e. f(g(x)) for some x

Not that o not only takes two functions but it returns one so  (f o g)(5) could be seen as compose thes funcions and invoke. 

We'll call x a point. Which is itself a little noisy. In functional programming we clean things up even more
by getting rid of these points. But before that we need to understand that when we start to think of functions as data
that functions really only ever need one argument. What? But we've already seen several functions that have two arguments
like +, %, and even o, our function composition function. Ok well bare with me for a second as we look at +

\+(a: Itn, b: Int) doesn't look much different than +(a: Int)(b: Int)

By grouping +'s parameters like this and ignore +'s implementation we see that we can think of + as taking an int
and returning a function that takes and int and returns an int. This is juts like before with 'o' which returned
a function... so remember our example invocation of (f o g)(5)? We'd just invoke + similarly, i.e. +(3)(4). Even the
parens become superfluous at this point, because we're just chaining single argument function invocations together:
\+ 3 4

Now we can change a pointed function definition to a point free definition, we no longer need that point x.

pointed:

plus3(x: Int): Int -> Int = +(3)(x)

unpointed:

plus3 = + 3 (remember, + 3 returns a function!)
