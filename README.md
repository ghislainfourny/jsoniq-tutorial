# JSONiq for JSON users
This tutorial introduces the JSONiq language, which declaratively manipulates JSON data.
Why don't you go ahead can try the queries of this document on our [online demo interface](http://try-zorba.28.io)?


## JSON

As explained on the [official JSON Web site](http://www.json.org/), JSON is a lightweight data-interchange format designed for humans as well as for computers. It supports as values:
- objects (string-to-value maps)
- arrays (ordered sequences of values)
- strings
- numbers
- booleans (true, false)
- null

JSONiq provides declarative querying and updating capabilities on JSON data.

## Elevator Pitch

JSONiq is based on XQuery, which is a W3C standard (like XML and HTML). XQuery is a very powerful declarative language that originally manipulates XML data, but it turns out that it is also a very good fit for manipulating JSON natively.
JSONiq, since it extends XQuery, is a very powerful general-purpose declarative programming language. Our experience is that, for the same task, you will probably write about 80% less code compared to imperative languages like JavaScript, Python or Ruby. Additionally, you get the benefits of strong type checking without actually having to write type declarations.
Here is an appetizer before we start the tutorial from scratch.

    let $stores :=
    [
      { "store number" : 1, "state" : "MA" },
      { "store number" : 2, "state" : "MA" },
      { "store number" : 3, "state" : "CA" },
      { "store number" : 4, "state" : "CA" }
    ]
    let $sales := [
       { "product" : "broiler", "store number" : 1, "quantity" : 20  },
       { "product" : "toaster", "store number" : 2, "quantity" : 100 },
       { "product" : "toaster", "store number" : 2, "quantity" : 50 },
       { "product" : "toaster", "store number" : 3, "quantity" : 50 },
       { "product" : "blender", "store number" : 3, "quantity" : 100 },
       { "product" : "blender", "store number" : 3, "quantity" : 150 },
       { "product" : "socks", "store number" : 1, "quantity" : 500 },
       { "product" : "socks", "store number" : 2, "quantity" : 10 },
       { "product" : "shirt", "store number" : 3, "quantity" : 10 }
    ]
    let $join :=
      for $store in jn:members($stores), $sale in jn:members($sales)
      where $store("store number") = $sale("store number")
      return {
        "nb" : $store("store number"),
        "state" : $store("state"),
        "sold" : $sale("product")
      }
    return [$join]
    [
      { "nb" : 1, "state" : "MA", "sold" : "broiler" },
      { "nb" : 1, "state" : "MA", "sold" : "socks" },
      { "nb" : 2, "state" : "MA", "sold" : "toaster" },
      { "nb" : 2, "state" : "MA", "sold" : "toaster" },
      { "nb" : 2, "state" : "MA", "sold" : "socks" },
      { "nb" : 3, "state" : "CA", "sold" : "toaster" },
      { "nb" : 3, "state" : "CA", "sold" : "blender" },
      { "nb" : 3, "state" : "CA", "sold" : "blender" },
      { "nb" : 3, "state" : "CA", "sold" : "shirt" }
    ]

## And here you go

### Actually, you already knew some JSONiq

The first thing you need to know is that, as a rule of thumb, a well-formed JSON document is a JSONiq expression as well.
This means that, most of the time, you can copy-and-paste a JSON document into a query. The following are JSONiq queries that are "idempotent" (they just output themselves):

    { "pi" : 3.14, "sq2" : 1.4 }
    { "pi" : 3.14, "sq2" : 1.4 }
    [ 2, 3, 5, 7, 11, 13 ]
    [ 2, 3, 5, 7, 11, 13 ]
    {
      "operations" : [
        { "binary" : [ "and", "or"] },
        { "unary" : ["not"] }
      ],
      "bits" : [
        0, 1
      ]
    }
    {
      "operations" : [
        { "binary" : [ "and", "or" ] },
        { "unary" : [ "not" ] }
      ],
      "bits" : [
        0, 1
      ]
    }
    [ { "Question" : "Ultimate" }, ["Life", "the universe", "and everything"] ]
    [ { "Question" : "Ultimate" }, [ "Life", "the universe", "and everything" ] ]

This works with objects, arrays (even nested), strings, numbers, booleans, null. The exceptions to this rule (but we are working on it!) are that:

1.  empty objects are not recognized and must be written {| |}.
2.  characters escaped with the \ in JSON strings are not recognized (XQuery uses another means of escaping characters).
It also works the other way round: if your query outputs an object or an array, you can use it as a JSON document.
JSONiq is a declarative language. This means that you only need to say what you want - the compiler will take care of the how. In the above queries, you are basically saying: I want to output this JSON content, and here it is.

## JSONiq basics

### The real JSONiq Hello, World!

Wondering what a hello world program looks like in JSONiq? Here it is:

    "Hello, World!"

    -> Hello, World!

Not surprisingly, it outputs the string "Hello, World!".

### Numbers and arithmetic operations

Okay, so, now, you might be thinking: "What is the use of this language if it just outputs what I put in?" Of course, JSONiq can more than that. And still in a declarative way. Here is how it works with numbers:

    2 + 2

will output:

    4

whereas
    (38 + 2) div 2 + 11 * 2

will output

    42

(mind the division operator which is the "div" keyword. The slash operator has different semantics).

Like JSON, JSONiq works with decimals and doubles:

    6.022e23 * 42
    2.52924E25

### Logical operations

JSONiq supports boolean operations.

    true and false
    false
    (true or false) and (false or true)
    true
    
The unary not is a function (yes, JSONiq has functions):

    not(true)
    false

### Strings

JSONiq is capable of manipulating strings as well, using functions:

    concat("Hello ", "Captain ", "Kirk")
    -> Hello Captain Kirk

    substring("Mister Spock", 8, 5)
    -> Spock

JSONiq comes up with a rich string function library out of the box, inherited from its base language. These functions are listed here (actually, you will find many more for numbers, etc.)

### Sequences

Until now, we have only been working with single values (an object, an array, a number, a string, a boolean). JSONiq supports sequences of values. You can build a sequence using commas:

    (1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
    -> 1 2 3 4 5 6 7 8 9 10

    1, true, 4.2e1, "Life"
    -> 1 true 42 Life

The "to" operator is very convenient, too:
    (1 to 100)
    -> 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20
    21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40
    41 42 43 44 45 46 47 48 49 50 51 52 53 54 55 56 57 58 59 60
    61 62 63 64 65 66 67 68 69 70 71 72 73 74 75 76 77 78 79 80
    81 82 83 84 85 86 87 88 89 90 91 92 93 94 95 96 97 98 99 100

Some functions even work on sequences:
    sum(1 to 100)
    -> 5050

    string-join(("These", "are", "some", "words"), "-")
    -> These-are-some-words

    count(10 to 20)
    -> 11

    avg(1 to 100)
    -> 50.5

Unlike arrays, sequences are flat. The sequence (3) is identical to the integer 3, and (1, (2, 3)) is identical to (1, 2, 3).

## A bit more in depth

### Variables

You can bind a sequence of values to a (dollar-prefixed) variable, like so:

    let $x := "Bearing 3 1 4 Mark 5. "
    return concat($x, "Engage!")
    -> Bearing 3 1 4 Mark 5. Engage!

    let $x := ("Kirk", "Picard", "Sisko")
    return string-join($x, " and ")
    -> Kirk and Picard and Sisko

You can bind as many variables as you want:

    let $x := 1
    let $y := $x * 2
    let $z := $y + $x
    return ($x, $y, $z)
    -> 1 2 3

and even reuse the same name to hide formerly declared variables:

    let $x := 1
    let $x := $x + 2
    let $x := $x + 3
    return $x
    -> 6

### Iteration

In a way very similar to let, you can iterate over a sequence of values with the "for" keyword. Instead of binding the entire sequence of the variable, it will bind each value of the sequence in turn to this variable.

    for $i in 1 to 10
    return $i * 2
    -> 2 4 6 8 10 12 14 16 18 20

More interestingly, you can combine fors and lets like so:

    let $sequence := 1 to 10
    for $value in $sequence
    let $square := $value * 2
    return $square
    -> 2 4 6 8 10 12 14 16 18 20

and even filter out some values:

    let $sequence := 1 to 10
    for $value in $sequence
    let $square := $value * 2
    where $square < 10
    return $square
    -> 2 4 6 8
    
Note that you can only iterate over sequences, not arrays. To iterate over an array, you can obtain the sequence of its values with the [] operator, like so:

    [1, 2, 3][].
    -> 1 2 3

### Conditions

You can make the output depend on a condition with an if-then-else construct:

    for $x in 1 to 10
    return if ($x < 5) then $x
                       else -$x
    -> 1 2 3 4 -5 -6 -7 -8 -9 -10

Note that the else clause is required - however, it can be the empty sequence () which is often when you need if only the then clause is relevant to you.

### Composability of Expressions

Now that you know of a couple of elementary JSONiq expressions, you can combine them in more elaborate expressions. For example, you can put any sequence of values in an array:

    [ 1 to 10 ]
    -> [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ]

Or you can dynamically compute the value of object pairs (or their key):
    {
      "Greeting" : (let $d := "Mister Spock"
                    return concat("Hello, ", $d)),
      "Farewell" : string-join(("Live", "long", "and", "prosper"),
                               " ")
    }
    -> { "Greeting" : "Hello, Mister Spock", "Farewell" : "Live long and prosper" }

You can dynamically generate object singletons (with a single pair):

    { concat("Integer ", 2) : 2 * 2 }
    -> { "Integer 2" : 4 }

and then merge lots of them into a new object with the {| |} notation:

    {|
      for $i in 1 to 10
      return { concat("Square of ", $i) : $i * $i }
    |}
    -> {
    "Square of 1" : 1,
    "Square of 2" : 4,
    "Square of 3" : 9,
    "Square of 4" : 16,
    "Square of 5" : 25,
    "Square of 6" : 36,
    "Square of 7" : 49,
    "Square of 8" : 64,
    "Square of 9" : 81,
    "Square of 10" : 100
    }

## JSON Navigation

Up to now, you have learnt how to compose expressions so as to do some computations and to build objects and arrays. It also works the other way round: if you have some JSON data, you can access it and navigate.
All you need to know is: JSONiq views
an array as an ordered list of values,
an object as a set of name/value pairs

### Objects

You can use an object as if it were a function and pass the call an argument of type xs:string. It will return the value associated thereto:

    let $person := {
      "first name" : "Sarah",
      "age" : 13,
      "gender" : "female",
      "friends" : [ "Jim", "Mary", "Jennifer"]
    }
    return $person("first name")
    -> "Sarah"

You can also ask for all keys in an object:

    let $person := {
      "name" : "Sarah",
      "age" : 13,
      "gender" : "female",
      "friends" : [ "Jim", "Mary", "Jennifer"]
    }
    return { "keys" : [ jn:keys($person)] }
    -> { "keys" : [ "name", "age", "gender", "friends" ] }

### Arrays

You can use an array as if it were a function and pass the call an argument of type xs:integer. It will return the entry at that position:

    let $friends := [ "Jim", "Mary", "Jennifer"]
    return $friends(2)
    -> Mary

It is also possible to get the size of an array:

    let $person := {
      "name" : "Sarah",
      "age" : 13,
      "gender" : "female",
      "friends" : [ "Jim", "Mary", "Jennifer"]
    }
    return { "how many friends" : jn:size($person("friends")) }
    -> { "how many friends" : 3 }

For convenience, there is a function that returns all elements in an array, as a sequence:

    let $person := {
      "name" : "Sarah",
      "age" : 13,
      "gender" : "female",
      "friends" : [ "Jim", "Mary", "Jennifer"]
    }
    return jn:members($person("friends"))
    -> Jim Mary Jennifer

### Relational Algebra

Do you remember SQL's SELECT FROM WHERE statements? JSONiq inherits selection, projection and join capability from XQuery, too.

    let $stores :=
    [
      { "store number" : 1, "state" : "MA" },
      { "store number" : 2, "state" : "MA" },
      { "store number" : 3, "state" : "CA" },
      { "store number" : 4, "state" : "CA" }
    ]
    let $sales := [
       { "product" : "broiler", "store number" : 1, "quantity" : 20  },
       { "product" : "toaster", "store number" : 2, "quantity" : 100 },
       { "product" : "toaster", "store number" : 2, "quantity" : 50 },
       { "product" : "toaster", "store number" : 3, "quantity" : 50 },
       { "product" : "blender", "store number" : 3, "quantity" : 100 },
       { "product" : "blender", "store number" : 3, "quantity" : 150 },
       { "product" : "socks", "store number" : 1, "quantity" : 500 },
       { "product" : "socks", "store number" : 2, "quantity" : 10 },
       { "product" : "shirt", "store number" : 3, "quantity" : 10 }
    ]
    let $join :=
      for $store in jn:members($stores), $sale in jn:members($sales)
      where $store("store number") = $sale("store number")
      return {
        "nb" : $store("store number"),
        "state" : $store("state"),
        "sold" : $sale("product")
      }
    return [$join]
    -> [
        { "nb" : 1, "state" : "MA", "sold" : "broiler" },
        { "nb" : 1, "state" : "MA", "sold" : "socks" },
        { "nb" : 2, "state" : "MA", "sold" : "toaster" },
        { "nb" : 2, "state" : "MA", "sold" : "toaster" },
        { "nb" : 2, "state" : "MA", "sold" : "socks" },
        { "nb" : 3, "state" : "CA", "sold" : "toaster" },
        { "nb" : 3, "state" : "CA", "sold" : "blender" },
        { "nb" : 3, "state" : "CA", "sold" : "blender" },
        { "nb" : 3, "state" : "CA", "sold" : "shirt" }
     ]

### Access external data

Our implementation supports collections of (and indices on) JSON objects or arrays:

    dml:collection("my:data")
    -> { "foo" : "Your" }
    { "foo" : "Collection" }
    { "foo" : "of" }
    { "foo" : "JSON" }
    { "foo" : "objects" }
    
It is also possible to get JSON content with an HTTP request, or by parsing it from a string. The EXPath http-client module (described in the Zorba documentation)  allows you to make HTTP requests, and the jn:parse-json() function allows you to use the body as an object or an array.

## I want more

JSONiq supports JSON updates. You can declaratively update your JSON data. JSONiq provides updating expressions. The list of updates that is eventually output by your program is then applied to your JSON data.

    copy $people := {
      "John" : { "status" : "single" },
      "Mary" : { "status" : "single" } }
    modify (replace json value of $people("John")("status") with "married",
            replace json value of $people("Mary")("status") with "married")
    return $people
    -> { "John" : { "status" : "married" }, "Mary" : { "status" : "married" } }

Other updates are insertion into an object or an array, replacement of a value in an object or an array, deletion in an object or an array, renaming an object pair, appending to an array. This is documented on jsoniq.org.

## Even more

JSONiq can do way more that what is presented here. Here are a couple of highlights:
- JSONiq is a strongly typed language, but is smart enough to not bother you with types when unnecessary. It potentially supports static typing as well to detect errors before you even execute your program.
- You can define your own functions and modules.
- JSONiq provides you with loads of available modules shipped with Zorba.
- JSONiq has tons of further features such as switch, typeswitch and try-catch expressions, universal/existential quantifiers, path expressions, filtering expressions, functors, mappings, grouping, windowing.
More is not enough

- JSONiq supports XML. Yes: you can manipulate JSON and XML with the same language! JSONiq is actually a superset of XQuery, a W3C standard, and extends its data model to support JSON.
- JSONiq supports scripting. If you need to write a full-fledged, side-effecting Web application, scripting is for you.
The complete JSONiq specification is available on http://jsoniq.org/
