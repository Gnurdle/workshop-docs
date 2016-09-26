# Chapter 2: Basics

### Dive, Dive!
Often when someone fluent in another programming language encounters Clojure (or Lisp) code, the reaction is akin to staring into an abyss of complexity and confusion.

Lots of parens mixed with familiar and unfamiliar words, no obvious structure, etc.  Whiskey,  Tango, Foxtrot?

In reality, what is going on is actually quite a bit simpler than what you might be using every day, but things do work differently, and need to be thought about differently.  And this thinking differently is where the benefit seems to lie.

So let's dive all the way to the bottom and climb back out, hopefully dispelling any lingering mysticism.

It's way simpler than you might be expecting.

### Data Structures- the building blocks

There are three very important things to keep in mind when you are trying to get your head around Lisp, and by extension Clojure, source code:

  1. code is data
  2. code is data
  3. and finally, **code is data**.

The stuff you build in your text editor is just that - **text**.  That text is then processed by an entity known as the **reader**, which (barring errors) will transform it into **data**.

It does this be employing a certain lexigraphical rules to the incoming text which turns certain sequences of characters into entities that are (historically) called **atoms** (indivisible units).

In Clojure, these are examples of things that  become atoms when the reader encounters them:


| atom             | internal type  | explaination |
| ------------------    | -------------|    |
| nil        | nil  | the non-value, e.g. NULL / missing|
| false      | java.lang.boolean |  logical false |
| true       | java.lang.boolean |  logical true |
| 0, -42, 105  | java.lang.Long  | integer numbers |
| 1.1, -1.2e20 | java.lang.double | ieee floating point numbers
| 42.3M , 1M | java.math.BigDecimal | arbitrary precision numbers
| 1/3, 22/7 |  clojure.lang.Ratio | numbers that are repreented internally as a ratio 
| eggs&ham, y+2,  peach, whiskey  | clojure.lang.Symbol | symbols (note the "&" and "+" are not special!)
| :name, :owner | clojure.lang.Keyword | keywords - special - they are interned
| \H, \e, \l, \l \o | java.lang.Character | single character constants
| "apple", "red"    | java.lang.String | string literal |

This should feel pretty natural to anybody that has used any programming language.  There are couple of points that might be surprising:

   - Characters that you might expect to be "special" are not - aka there are no hard tokens like '=', '+' '&' etc.  In Clojure, these are just part of possible characters in symbols
   
   -  Symbols usually don't have an analog in other languages - perhaps they are what you might construe as an 'identifier' in those languages.
   
   - Keywords are likely a new concept as well.  These are human-readable strings that get "interned" into machine-friendly values.
   
   - character constants, the '\\' .vs. single  quote "'".  In Lisps, the single-quote has an entirely different role.
   
### Values and Evaluation

A subtle point exists between the notion of an **atom** 3042 and the integer **value** 3042, which is obtained by **evaluating** the atom.  It's easy overlook, since they "appear" to be essentially the same thing.

There are other sources of **values**.  These are **containers** (collections of values), evaluation of a **function** (transform a set of input values into an output value), and a **function** itself is also a value (a concept known as **first class** functions)

### Containers   

| reader syntax | explaination 
| --------------    | -------------
| ( v ... )  | a **list** (think singley-linked)
| [ v ... ]  | **vector** (random accessable)
| #\{ v ... \} | a **set** (distinct values)
| \{ k v ... \} | a **map** (associated key/value pairs)

Containers compose recursively because they contain **values**, which include **containers**

```
(1 2 3)          ; a list
(1 (2 3 4) 5)    ; a nested list
(1 {:name "fred" :age 42} (4 (5 6))) ; even nuttier
(sing "song") ; list of a symbol and a string

``` 

### Forms are the Code
A list is clearly **data**.  But a list can also be **code** if it structured in such a way which allows it to be **evaluated**.  Lists of this sort are called **forms**, and this is the stuff we use to build our **code**.  So **code** is just **data** that can be **evaluated**.

so this list (a symbol, integer, integer), which also happens to be a tiny program.

```Clojure
(+ 10 20)
```

We can experiment directly with building these 'tiny programs' and seeing the outcome, for example:

```Cloujure
user> 10          ;; evaluate a simple atom
10
user> +           ;; the value of symbol '+' is the function '+'
#function[clojure.core/+]
user> (+ 10 20)   ;; evaluate a form
30
user> (* 5 (+ 10 20))  ;; and we can nest these...
150

```
And now we are starting to see the **code**, which consists of a number of nested **forms** that can be **evaluated** to produce **values**.

It's really that simple.  You start with a library of functions and special forms, use these to compose more functions an forms, which you eventually use and compose to implement whatever program you set out to build.

You are in effect building upon and continuing a bootstrap process leading from general (the built in primatives) to the specific (your libraries and programs).

Many Lisps have been implemented starting with only a very minimal kernel and bootstrapping (using Lisp) into full-blown implementation.

Clojure itself does a considerable amount of this type of bootstrapping to implement its own core library, so a great deal of Clojure is implemented in Clojure itself.  There is a formidable Java kernel to be sure, but there is also a lot of Clojure involved in the implementation.

So there is no division of "us .vs. them" when it comes to what you can build this way.  Are you extending the language, or writing code?  Is there really any difference?

### Namespaces

Namespaces are used to segregate names into organized subdivisions.

Clojure code is always running in some namespace, and this is typically an amalgam of your code plus some other namespaces you have 'used' (and henced merged) with yours through a variety of techniques.

The namespace 'clojure.core' contains all the core language elements, and typically you want these close at hand when writing your own code - so you merge it into yours.

Other namespaces can be referred to by spelling them out for example 'clojure.core/+' or 'org.goofco.mylib/foobar'.  It looks like a Java convention, because it pretty much is.

The topic can become complex, so for now, just be aware that they exist, and they keep names organized in separate namespaces.

### Vars

Variables are created with `def`

```
(def a "apple")

(def weight 5)
```

These get defined in the namespace of the file they are defined in.

Note that these are not the customary "bash in place" variables you might be used to.  We are simply **binding** a **symbol** to a **value** here.


### Functions
Functions - those formerly mystical **values** that can be **evaluated** to produce **values** - where do they come from?

Why, there is a form to create them, of course.

```Clojure
(fn [name]
   (clojure.string/upper-case name))
   
```

Evaluating this form produces a function (a value) which does what it says.  It **binds** name to the argument passed, and then uses that to call another function ('upper-case', which is in the namespace 'clojure.string') and returns that value.

This one is anonymous - it went *poof* right after it became a value.  We had the value, but the function was never used.  Not so useful, let's do something different - 

```Clojure
(def uppername
  (fn [name]
    (clojure.string/upper-case name)))

... 
(uppername "gilgamesh")  ;; evaluate this function
==> "GILGAMESH"
 
```
There, now we have a function we call call by name.

In fact, that particular drill is so common, there is a short-cut

```Clojure
(defn uppername
    "a function to convert a name to upper case"
    [name]
    (clojure.string/upper-case name))
```
accomplishes the just same thing, and also lets you add some documentation.

Clojure defines a named function using this syntax:

```clojure
(defn name
  optional-doc-string?
  params-vector
  exprs*)
```

1. `defn` - start of the 'defn' form
2. `name` - what you call the function.
3. `doc-string?` - an optional description of the function.
4. `params-vector` - a vector of symbols naming the functions arguments.
5. `exprs*` - expressions that make up the function.  The value of the last one is the return value from the function.

But why have the ability to create an anonymous function in the first place?

Because doing so created a value, that could have been used in a context where a function was called for, for example, as the input to another function.  The point here is that they are a little different than subroutines or methods.  They can be quite ephemeral, used in context, and then discared.  It is the functional way.

### Ready, Set, Plop!
We have the core  concepts, and we also have all the [documentation](https://clojuredocs.org) of the forms and functions, but yet....

Wanting to write a program, but can't get any traction?

We've Been there.  There is a lot of stuff, and it is well organized if you know what you are looking for, but there is seemingly no place to get that first bite, and get started - even if you've been writing imperative code for decades.

So this section is going to try to be a whirlwind tour of where you might find (or not find) some of the stuff you might be looking for.

#### printing things

```Clojure

user> (println "hello world")
hello world
nil

user> (println (format "%s %6.2f" "hello" (+ 0.002 100)))
hello 100.00

;; or a bit more functionally:

user> (defn spy [thing] (println thing) thing)
#'user/spy

user> (< 100 (spy (+ 10 2)))
12
false

```

####  building things up with expressions - where are all the variables?

the (let...) form is useful for this

```Cloujure
(let [x 100
      y (+ x 10)
      z (* x y)]
      z) ;; the let-form returns value of 'z'
```

#### where are all the operators?
they are all functions.  All the arithmetic, all the logicals, all the bit fiddling - functions.

```Clojure 
;; the c-way : if(a > b && b < 100) return 10; else return 20;

(if (and (> a b)
         (< b 100)) 
         10
         20)
```

#### Where are the loops?

They went lots of places.

First there are a lot of functions that work on and generate sequences.

```Clojure
user> (range 10)
(0 1 2 3 4 5 6 7 8 9)
user> (last (range 100))
99
user> (reverse (range 10))
(9 8 7 6 5 4 3 2 1 0)
```

Clojure tends to view loops as applying a function to a container to get another container.

There are certain types of loops that happen a lot, so there are built-in forms to implement these.

**map** applies a function to every element in a container, producing a new container containing the result applied to each element:

```
user> (map even? (range 10))
(true false true false true false true false true false)
```

**filter** takes a function, and a container, and yields a new container which only contains elements for which the function returns a truthy result (usually called predicates).

```Clojure
user> (filter even? (range 10))
(0 2 4 6 8)
```

**reduce** 
Tricky to explain, but really powerful.  It amounts to starting with an accumulator value which is either the first element, or something you specify.  The container is walked and a two argument function is called (fn [a v] ... ) for every v, and at each step the accumulator is replaced by the value of the function, and the final value is returned.

The customary example is something like this:

```Clojure
user> (reduce + [1 2 3 4])
10

```
but that hardly does it justice.  If you have some loop in mind that doesn't fit one of **map**, **reduce** or **filter**, then perhaps you haven't quite grokked the possibilities availed by **reduce**.

These are common loop constructs that get re-use through the different behavior of the function applied, so instead of writing out these loops, you just provide the behavior, not the actual loop body.


























Next [Chapter 3: REPL](/Pages/3-repl.md)
