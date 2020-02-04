# Tuples

# What Are Tuples?

F# has what is known as an an "algebraic type system" (inspired by the field of abstract algebra in mathematics). One of the things that this means is that we can combine types in F# together in certain ways. The first way we are going to look at can be thought of as "multiplying" together two or more types. The resulting types we get from this operation are called "tuples". Let's see an example:

```fsharp
let stringAndInt = ("hello", 26)

let intAndBoolAndString = (18, false, "goodbye")
```

Tuples are created simply by placing whatever values you like, separated by commas, in between two brackets. Notice that `stringAndInt` has type `string * int`, and that `intAndBoolAndString` has type `int * bool * string`. The choice of `*` as the symbol for tuples is no accident - tuples are very much like multiplying types.

Tuples can be as long as you like although they do become more and more awkward to work with the longer they get.

# Getting Values From Tuples

So, if we have a tuple, how do we get individual values out of it? By pattern matching of course. Here's an example:

```fsharp
let getFirstValue tuple =
    let (a, b) = tuple
    a
```

This function has type `'a * 'b -> 'a`. There is pattern matching going on, even though there is no `match ... with` statement. The `let (a, b)` is where we are pattern matching. There are no other cases for us to check as our function only takes a tuple with two elements, so we can simply bind the value `a` to the first element in the tuple using the `let (a, b) = tuple` syntax. Notice that `b` is greyed out and the message "This value is unused" appears if you mouse over it. We can replace `b` with the wildcard `_` as we don't use the value.

We can also do the pattern matching in the parameter itself:

```fsharp
let getFirstValue (a, _) = a
```

This is the same function but written much more concisely. Although we can pass values into functions in this way - as tuples - it is almost always better to pass in parameters individually so that we can make use of partial application.

We don't, in fact, even need to write the above function as F# already has this function built in. It's called `fst` and returns the first value of a length two tuple. Let's try it on `stringAndInt`:

```
> fst stringAndInt;;
val it : string = "hello"
```

There is a similar function which returns the second item called `snd`, define like so:

```fsharp
let snd (_, b) = b
```

Here's what happens if we try it on `stringAndInt`:

```
> snd stringAndInt;;
val it : int = 26
```

F# does not have built in functions for handling tuples with more than two elements in them, so we have to write our own using the same technique:

```fsharp
let third (_, _, c) = c
```

# Using Tuples Inside Match Statements

Using tuples inside `match` statements is similar to how we use tuples in parameters:

```fsharp
let testTuple tuple =
    match tuple with
    | (x, y) when x > y -> printfn "first value is bigger"
    | (x, y) when x < y -> printfn "second value is bigger"
    | _ -> printfn "values are the same"
```

`testTuple` has type `'a * 'a -> unit` (where `'a` is some type which can be used with `>` and `<`). This technique is particularly useful if your functions takes two parameters and you need to test both together:

```fsharp
let checkCoords x y =
    match x, y with
    | (a, b) when a = b -> "equal"
    | (a, b) when a + b < 0 -> "negative"
    | (a, b) when a + b > 0 -> "positive"
    | _ -> "something else"
```

`checkCoords` has type `int -> int -> string`. We create a tuple with `x` and `y` (omitting the brackets for readability) and pattern match on it. This can make writing match statements which depend on more than one value much simpler and much easier to read, rather than having to nest match statements to test each input.

Finally, there is a "double forward pipe" operator `||>` which takes a tuple of length two and passes both values into a function individually. For example:

```fsharp
(10, 12) ||> checkCoords
```

This is equivalent to:

```fsharp
checkCoords 10 12
```

There is also a "triple forward pipe" `|||>` which works on length three tuples. These operators aren't used too frequently, but now you know what they are if you do come across them.

# Exercises
Let's practice using tuples. Complete the following exercises:

1. Without running the code, what is the type of the following value:
    ```fsharp
    let tuple = ("hello", "world", true)
    ```
2. Write a function which takes a tuple of three `int`s and returns their sum.
3. Write a function which takes a length two tuple and returns the same tuple with the values swapped.
4. Write a function which takes a length two tuple and a value and returns a length three tuple with the values from the input tuple in as the first two elements, and the single value as the third element in the tuple.
5. Write a function which takes two `int`s (`x` and `y`) and returns the name of the quadrant that the coordinate `(x, y)` would be in if it was plotted on a graph. For example `"upper right"` if `x` and `y` are both positive. Also, return `"origin"` if `x` and `y` are both `0`.
6. Write a function which takes a list of tuples of length two and returns a list with all the values from the first elements of the tuples.