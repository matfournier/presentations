---
title: Getting Func-ey 
author: Mathew Fournier
patat:
  theme:
    syntaxHighlighting:
      decVal: [bold]
  images:
    backend: auto
...

# INTRO 

--- 

# YOU ALREADY DON'T WRITE JAVASCRIPT 

You probably already *dont* write legit javascript:
  - run your code through babel? basically transpiling 
  - here we write _typescript_ not _javascript_ 
    - typescript transpiles to javascript
    
---

# JS IS PRETTY FUNCTIONAL ALREADY

But isn't javascript functional already?  Depends on your definition of
functional, but yes: 

- first class functions 
- simple anonymous function syntax 
- immutability (via things like immutable.js) 
- algebraic structures via fantasty-land compliant libraries: 
	* Either, Maybe, etc.
	* Futures via Fluture 

Which can be used in conjunction w/ something like Flux/Redux/Vuex etc
 "unidirectional data flow" popular in most js UI frameworks these days

But that's a big difference than going into *statically-typed functional
programming*

---

# RICH ECOSYSTEM OF TRANSPILED JS LANGS:w


The functional ones tend to come from an ML lineage and look the same in the
same way that all C-inspired languages look the same 

- Haskell: Elm, PureScript, GHCJS 
- ocaML: BuckleScript, Reason (facebook messenger is written in this)
- F#: Fable 

These are definitly not mainstream, but there are jobs floating around that use
these in production. 

---

# A peek at react (hooks to be specific)

- "In terms of React, the problem was this: classes were the most common form of
   components when expressing the concept of state. *Stateless functional
   components* were also quite popular, but due to the fact that they could only
   really render, their use was limited to presentational tasks."

- "Hooks address these concerns by allowing us to define a component's stateful
   logic using only *function calls.* These function calls become more 
   *compose-able, reusable, and allows us to express composition in functions*
   while still accessing and maintaining state"
     - Sarah Drasner (on React and Vue Hooks)

---

# WHY STATICALLY TYPED FUNCTIONAL JS

If you are going to use a transpiler already, why not go whole-hog and get: 

- typed functional composition (and more w/ HKT) 
- *MUCH stricter* typing (move more errors from runtime to compile time) 
- Immutability by default 
- Compiler that helps you with unidirectional data flow type design 
- Explicit side effect management (can't mutate state just anywhere, ajax calls
  become part of your type sig, etc.)

All of this really depends on whether or not statically-typed functional
programming resonates with you as a way to solve problems.

---

# another JS example about React's Hook

- "The State Hook allows us to use state in React functional components: 
  - This gets us a step closer to using *functional components* over class components.
  - React Effects Hooks are similar to componentDidMount, componentDidUpdate, and componentWillUnmount
  - This is what the Effect Hook is for. Side-effects are things you want your application to make like:
    - Fetching data
    - Manually changing the DOM (document title)
    - Setting up a subscription"
 
from Getting Started w/ React Hooks 

---

# ELM AND PURESCRIPT

ELM: A delightful language for reliable webapps 

- both a haskell-inspired language AND a UI-framework
- *great* error messages 
- Lots of resources, way easier to get started 
- No typeclasses, no higher-kinded types :( 
- interfacing with JS (aka: FFI) is limited :( 

PURESCRIPT: A strongly typed functional programming language that compiles to JS 

- more or less a strict version of Haskell 
- *challenging* error messages :(
- resources harder to understand (need to 'level up' first)  :( 
- Type-classes, higher-kinded types! 
- Multiple UI frameworks (some wrap react, some do their own thing) :( 
- interfacing with JS (FFI) is fantastic 

--- 

# Ok I'm cautiously optimistic, wth does this stuff even look like? 

- you will notice parts look a lot like react JSX e.g.

```javascript
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

(show web examples)

--- 

# Purescript - PSCI Is your new best friend 

- type *expression* get *result*
- inspect *types* via `:t` 
- everything is a *function*
- everything is *immutable* 

```haskell
f :: Int -> Int 
f x = x + 1 

let a = 3 in 
    a := a + 1  -- compiler error 
```

---

# Purescript - everything is an expression

```haskell

let a = if someBool then 1 else 0 in 
    a + (let b = 2 in b) 
```
---

# Purescript - No side-effects!? 

```haskell

foo :: Int -> String 

-- but what if I want to print to console?

foo :: Int -> String 
foo x = print (x + 1)  -- will get an error!

-- :t of foo is really 
-- foo :: (Show a, Num a) => a -> IO () 

foo :: Int -> String
foo x = show (x + 1)  -- we get back a string, but we haven't printed to console 

```

So it's no side-effects, *unless explicitly stated*
- once you are in a side-effect you can't get out until you deal with the
  potential for failure 
- this is why there are so many things like monad/functor/etc. for dealing with
  "something in a context", in our case IO, that make doing this easy. 

---

# Language time - type inspections

```haskell 
$ pulp repl 

> :type 1.0 
Number

> :type "test"
String

> :type true
Boolean

> :type 1 
Int 

> :type [1,2,3]
Array Int 

> :type [true, false]
Array Boolean
```

---

# Language time - Functions

```haskell

addInt :: Int -> Int -> Int -- the function signature. 
addInt x y = x + y          -- the function itself 

```

```haskell
:paste 
addInt :: Int -> Int -> Int
addInt x y = x + y
(ctrl-D)
```

---

# Curried functions, partial application

All functions are really curried functions that take a single arguement.

- there are implicit parens `AddInt :: Int -> (Int -> Int)` 
- give AddInt an Int, and it returns you a function that if you give it another
  Int it will give you an Int 
  
```haskell
-- addInt :: Int -> Int -> Int 

addTwo :: Int -> Int 
addTwo 2 = addInt 2 
```

---

# Type Inference 

Purescript will infer the type to the most general thing possible. The type
signature is often _not optional_ if you need to be restrictive 

```haskell
> addInt' x y = x + y
> :t addInt'
forall a. Semiring a => a -> a -> a
> addInt' 2 3 
5
> addInt' 2.0 3.0 -- THESE ARE NOT INTS! 
5.0               -- :( 
```

So what's this semiring? Well obviously it's an additive commutative monoid with
identity zero (uhhhhhhhh......). For instance, the set of natural numbers (including zero)
under addition or multiplication forms a semiring. 

- it's not important
- but where is this Semiring thing coming from anyways? 
- *Semiring* is a defined *typeclass* and that *addInt* will work for any type
  that implements Semiring.
  - think of it like an interface constraint. We'll cover typeclasses later.

---

# Anonymous Functions

- the syntax for an anonymous function is `\x -> exp`
- we could have written *addInt* as:

```haskell

addInt :: Int -> Int -> Int
addInt = \x y -> x + y 

```

Or we could truly leave it anonymous in the repl: 

```haskell
> (\x y -> x + y) 2 3 
5 
```

---

# Types 

Types are the workhorse of purescript and come in a few flavours: 

- type aliases 
- data types
- newtypes 
- record syntax (MUCH nicer story than haskell) 

---

# Type aliases 

- uses the `type` keyord 
- run `Bower install purescript-strings purescript-tuples --save` and restart psci 

```haskell
> import Data.String 
> type URL = String 
> :paste
urlComponents :: URL -> Array URL 
urlComponents url = split (Pattern "/") url 
(ctrl-D)
> testUrl = "www.blah.com/blah"
> urlComponents testUrl 
["www.blah.com", "blah"]
```

But this just aliasing for readability. The type of urlComponents is still
`String -> Array String` (see for yourself) 

---

Other examples:
```haskell
> type Point = {x :: Number, y :: Number}
> :paste
origin :: Point 
origin = {x: 0.0, y: 0.0}
> origin.x 
0.0
> origin.y
0.0

```

---

# Data types 

defining a type alias like *Point* doesn't let us really construct a point though 
e.g.

```haskell 
> PointRec {x: 0.0, y: 0.0}
-- error: Unknown data constructor PointRec
```

---

# Data Types 

We have *SUM* types and *PRODUCT* types 

- *SUM TYPE*: BOOL
  - it has a cardinality of 2: true and false 
  - in scala: traits + case class / case object lets you make SUM types 

- *PRODUCT TYPES*: what every other language generally has 
  - e.g. `case class (x: Int, y: String)`:
    - cardinality is Int x String 
    - so 2^16 x Infinity? 

---

# Data immutable

- No methods, just data 
- No modifiers, no annotations 
- No default parameters 
- No private members 

What's left? *CONSTRUCTORS* 

---

# The data Keyword

- `data None = None` a sum type with a single member  
- `date MyBool = TRUE | FALSE` a sum type with two members. Compared to scala: 

```scala
// in scala 
sealed trait MyBool 
case object TRUE extends MyBool
case object FALSE extends MyBool 
```
- `data Minutes = Minutes Int` .  What is the `:t` of Minutes?

```scala
// in scala 
case class Minutes(minutes: Int)
```

- `data Person = Person String String` . Person "Mat" "Fournier" constructs a Person. 

---

### Anatomy of a data type 

```
    type constructor
      |
data Minutes = Minutes Int 
                 |
              data constructor 

```

- Minutes is actually a _function_ than when given an Int gives you Minutes
  - this means your data can also be partially applied and is _curried_ 
- the type constructor and data constructor don't have to be the same 
- `data Minutes' = Doggo Int` 
  - What is the type of Minutes' ?
  - What is the kinds of Minutes' ? (`:k`)

---

# The almighty "data" keyword 

Look familiar? : 

```haskell
data Option a = Nothing | Some a -- this is called a Tagged Union  
```

The world's easiest linked list implementation: 

```haskell
data List a = Nil | Cons a (List a)
```

In scala: 

```scala
sealed trait List[+A]
case object Nil extends List[Nothing]
case class Cons[+A](head: A, tail: List[A]) extends List[A] 
```

---

# Newtype

- `newtype` instead of `data` work when:
  - you have *exactly one* constructor
  - that constructor has *exactly one arguement*
  
```haskell
newtype Pixels = Pixels Number
newtype Inches = Inches Number 
newtype PhoneNumber = PhoneNumber String 
```

- So *PhoneNumber* is actually represented as a JavaScript string at runtime. 
- newtype gives us type safety _without_ runtime overhead of a function call
  (unlike Data)

--- Newtype 

# Newttype JS 

If we compare the generated code for PhoneNumber as `data` vs `newtype`: 

```javascript
var NewtypePhoneNumber = function (x) {
      return x;
  };

var DataPhoneNumber = (function () {
      function DataPhoneNumber(value0) {
          this.value0 = value0;
      };
      DataPhoneNumber.create = function (value0) {
          return new DataPhoneNumber(value0);
      };
      return DataPhoneNumber;
  })();
```

---

# The almighty "data" keyword

```haskell 
data Point = Point 
  { x :: Number
  , y :: Number
  }
```

# Deriving common typeclasses  

- the story here isn't as nice as haskell

```haskell
> data Minutes = Minutes Int 
> Minutes 10 

-- ERROR : No type class instance was found for Data.Show.Show Minutes 

```

Unlike case classes in scala, you get nothing from your data by default: no EQ, HashCode,
print, etc.

In haskell you can just do this 

```haskell
data Minutes = Minutes Int deriving (Show, Eq)
```

---

# Deriving in purescript 

But in purescript we either have to implement it by hand

Much boilerplate, so wow :( 

```haskell
-- give an instance of the typeclass Show for Option provided 
-- that the generic `a` held on by Option also implements Show 

instance showOption :: Show a => Show (Option a) where
  show (Some a) = "(Some " <> show a <> ")"
  show Nothing  = "Nothing"
```

note: `<>` is more or less a generic *concat* (go look at it's type like this
`:t (<>)` since it's an infix operator)

--- 

Or via this more clunky deriveGeneric 

- `bower install purescript-generics-rep --save`

```haskell
import Data.Generic.Rep as GR
import Data.Generic.Rep.Show (genericShow)

data Option a = Nothing | Some a 
derive instance genericOption :: GR.Generic (Option a) _ 
instance showOption :: (Show a) => Show (Option a) where show = genericShow

Some 10 -- will actually print now.
Some "Hello" 
Some [1,2,3] 
```

- this *sucks* but Elm isn't even this nice ! 
- other typeclasses you may want: Ord, Eq, Enum .. 

---


# Data desconstruction

- we unapply in the function and make a new Minutes  (we never mutate in place)
```haskell
data Minutes = Minutes Int 

addMinutes :: Minutes -> Minutes -> Minutes 
addMinutes (Minutes mx) (Minutes my) = Minutes (mx + my)

instance showMinutes :: Show Minutes where
  show (Minutes m) = "Minutes " <> show m 

> addMinutes (Minutes 10) (Minutes 20) 
Minutes 30 

```

---

# Data desconstruction

```haskell
import Data.Tuple 

data Point = Point 
  { x :: Number
  , y :: Number 
  }
  
components :: Point -> Tuple Number Number  
components (Point {x, y}) = Tuple x y 

> components(Point {x: 3.0, y: 5.0})
(Tuple 3.0, 5.0)

-- alternatively can do this 
componentsAlt :: Point -> Tuple Number Number 
componentsAlt (Point p) = Tuple p.x p.y
```

---

# Where and Let and other control structures 

If you haven't noticed every line is an expression so how do you write
complicated expressions? You don't want *hundreds of one line functions*

```haskell
-- where
import Math

sumSquareOrSquareSum :: Number -> Number -> Number
sumSquareOrSquareSum x y = if sumSquare > squareSum 
                           then sumSquare 
                           else squareSum 
  where sumSquare = (pow x 2.0) + (pow y 2.0)
        squareSum = pow (x + y) 2.0 

```

This is equivalent to: 

```haskell
-- let

sumSquareOrSquareSum :: Number -> Number -> Number
sumSquareOrSquareSum x y = let sumSquare = (pow x 2.0) + (pow y 2.0)
                               squareSum = pow (x + y) 2.0 
                           in
                             if sumSquare > squareSum 
                             then sumSquare
                             else squareSum 
```

---

# Simple Pattern Matching 

Pattern matching is much more baked into the language than scala and when you
talk about pattern matching it looks a lot more like method overloading: 

```haskell 
gcd :: Int -> Int -> Int 
gcd n 0 = n 
gcd 0 m = m
gcd n m = if n > m 
          then gcd (n - m) m 
          else gcd n (m - n)
```

There are also *case expressions* which are more similar to what you see in
Scala 

---

# Pattern matching with guards 

Guards are simple boolean expression which much be satisfied in addition to the
constraints of the pattern:

```haskell
gcd :: Int -> Int -> Int
gcd n 0 = n 
gcd 0 n = n 
gcd n m | n > m     = gcd (n - m) m 
        | otherwise = gcd n (m - n)
```

---

# Pattern matching array patterns and nested patterns (records)

```haskell
isEmpty :: forall a. Array a -> Boolean 
isEmpty [] = true
isEmpty _ = false
```

```haskell
type Address = { street :: String, city :: String }

type Person = { name :: String, address :: Address }

livesInVan :: Person -> Boolean
livesInVan { address: { city: "Vancouver" } } = true -- this is quite the match !
livesInVan _ = false
```

---

# Case expressions

Similar to what we see in scala 

```haskell

sumOfElementsIsBalanced :: Array -> Boolean
sumOfElementsIsBalanced [] = true 
sumOfElementsIsBalanced xs = case sum xs of 
                               0 -> true 
                               _ -> false 
```

---

# Next time 

- We can either do
  - typeclasses! 
  - functors, applicatives, monads, do notation! 
  - What the hell do `$ , <$>, <*>, >>=` mean anyways?

- Or throw caution to the wind and make an app ? 

Homework: check this stuff out. Poke around some blogs. Look at some tutorials.

---

# Resources

## PureScript 

- PureScript By Example -  free, great, effects section out of date 

## Haskell

- Get Programming With Haskell by Will Kurt - 100% worth the 50% 
- HaskellBook - The latter half is great for
  monad/applicative/functor/traversable/foldable, others. One author is a total
  d-bag, if you are curious about this book come see me later. 
- Learn you a haskell for great good - free, but not great 
- Parallel and concurrent programming in scala - mind-blowing second haskell
  book
  
---

## Elm 

- Official docs are pretty good! lots of great examples
- Elm In Action by Richard Feldman is also great
- An Outsiders Guide to Statically Typed Functional Programming 
  - can be had for free.  is not bad 

--- 

## Scala

Lots of the functional stuff is available in Scala! 

- Scala With Cats (awesome, awesome, awesome) 
- Scalaz for mere mortals (not a good intro book) 
- FP in Scala (lukewarm recommendation depending on what you want to do)

## Javascript 

Lots of functional JS floating around 

- Professor Frisby introduces Composable Functional JavaScript course on
EggHead.io
   - hilarious puppet teaches JS? I don't know how to describe it. The content
     is actually amazing and you can get through it in a weekend. This is
     immediately useful to your JS day life. 
- Professor Frisby's Mostly Adequate Guide to Functional Programming 
- Fantasy-land / Santuary libraries 
- Flutures 
   - Fantasy land compliant (monadic) alternative to Promises 