# Chaining Functions

## More Operators

There are a few more built in operators and functions which are worth introducing at this stage. The first is the mathematical "modulus" operator `%`. This operator takes two `int`s and returns the remainder when the left-hand side is divided by the right-hand side. For example:

```
> 8 % 5;;
val it : int = 3

> 51 % 2;;
val it : int = 1
```

The `%` operator is extremely useful for detecting if a number is divisible by another number. To do this, we need to use the "equality" operator `=`. This operator checks if two values are equal (and works on all F# types, not just numbers) and returns `true` if they are and `false` if not.

Let's define a function which checks if a number is even:

```fsharp
let isEven n = n % 2 = 0
```

`isEven` has type `int -> bool` as we might expect. Try applying this function to several different numbers to check that it does indeed detect even numbers correctly.

What if we wanted to check if a number was not divisible by 5, for example? We could use our knowledge of how modulus works and check if the number has a remainder of 1, 2, 3 or 4, but this would be rather verbose. Instead, we should use the `not` function which is built into F#.

`not` has type `bool -> bool` and returns the negation of the input (e.g. `not true` returns `false`).

Let's use this to write a function which checks if a number is not a multiple of 5:

```fsharp
let notMultipleOf5 n = not (n % 5 = 0)
```

We need the brackets to tell the compiler that everything inside the brackets should be passed into `not`. This function, of course, has type `int -> bool`.

Notice the blue squiggly line that VS Code puts underneath the function body. If we mouse over it (or open the "Problems" tab next to the terminal) we see the following message:

> `not (a = b)` might be able to be refactored into `a <> b`

VS Code is telling us that we should use the "not equals" operator (`<>`) rather than passing a value into the `not` function. This is a sensible suggestion - it will make our code easier to read and eliminate the need for those pesky brackets:

```fsharp
let notMultipleOf5 n = n % 5 <> 0
```

## Chaining Functions

Suppose we wanted to apply multiple functions to a value. One way to do this would be to use `let` to bind the result of applying each function to a new value and then reference that value in the next function call, like below:

```fsharp
let times4 n = n * 4
let subtract3 n = n - 3
let isEven n = n % 2 = 0

let x = 10
let x1 = times4 x
let x2 = subtract3 x1
let x3 = isEven x2
```

Sending all of this into F# Interactive gives the following output:

```
> let times4 n = n * 4
- let subtract3 n = n - 3
- let isEven n = n % 2 = 0
-
- let x = 10
- let x1 = times4 x
- let x2 = subtract3 x1
- let x3 = isEven x2;;
val times4 : n:int -> int
val subtract3 : n:int -> int
val isEven : n:int -> bool
val x : int = 10
val x1 : int = 40
val x2 : int = 37
val x3 : bool = false
```

Notice the types of the functions we defined. Both `times4` and `subtract3` have type `int -> int`. Remember also that `isEven` has type `int -> bool`. This is what allows us to take the output from `times4` and then call `subtract3` on that value - because `x1` is an `int`. Similarly, we can take the output from `subtract3` (`x2` which has type `int`) and call `isEven` on that value.

This is important because we can only combine together functions like this if we can match up the input and output types. We could not, for example, call `subtract3` on the output of `isEven` as we would get a compiler error:

```
> let y = isEven 10
- subtract3 y;;

Script.fsx(11,11): error FS0001: This expression was expected to have type
    'int'
but here has type
    'bool'
```

The compiler was expecting `y` (the value being passed into `subtract3`) to have type `bool`, but instead `y` has type `int`.

This may all seem rather obvious. However, the concept of matching up the types of your functions in order to chain functions together is a fundamental concept in F#.

## The Forward Pipe Operator

Defining a new value every time we want to apply a function in a chain like this is a little tedious, especially given [how hard naming values is](https://www.slideshare.net/pirhilton/how-to-name-things-the-hardest-problem-in-programming).

One solution might be to define a function which applies all the smaller functions we want and returns the result:

```fsharp
let doMaths n =
    let n1 = times4 n
    let n2 = subtract3 n1
    isEven n2
```

Notice that, overall, `doMaths` has type `int -> bool`.

However, this approach hasn't really improved things as all we have done is wrap our verbose code in a function. Fortunately, F# has a simple answer to this common problem. The "forward pipe" operator `|>` (often just called the "pipe" operator).

The pipe operator takes a value and a function and passes the value into the function _as the last parameter_ (in the case a multi-parameter function). For example, we can call `times4` on a number using `|>` like so:

```fsharp
10 |> times4
```

We can then pipe the result of this straight into another function:

```fsharp
10 |> times4 |> subtract3
```

And finally, a much more elegant representation of the original chain of functions:

```fsharp
let x =
    10
    |> times4
    |> subtract3
    |> isEven
```

Putting each new function call on a new line for readability - which all lines indented the same amount so that the compiler knows that they all belong to the same `let` block. `x` now has type `bool` as this is the type the the last function, `isEven`, returns.

Of course, there is nothing special about the number 10, so let's turn the above code into a function:

```fsharp
let doMaths n =
    n
    |> times4
    |> subtract3
    |> isEven
```

which has type `int -> bool`.

## Piping With Multi-Parameter Functions

The pipe operator only passes a single value into a function, so what if we have a function which takes two parameters? Let's define the following function:

```fsharp
let sumSquares x y = x * x + y * y
```

which has type `int -> int -> int`.

Suppose we are using `sumSquares` in a chain of functions, adding it into our `doMaths` function for instance:

```fsharp
let doMaths n =
    n
    |> times4
    |> subtract3
    |> sumSquares
    |> isEven
```

this gives the following error:

```
Script.fsx(22,8): error FS0001: Type mismatch. Expecting a
    '(int -> int) -> 'a'
but given a
    'int -> bool'
The type 'int -> int' does not match the type 'int'
```

Just passing a single value into the two-parameter function `sumSquares` returns an `int -> int` as we learnt in the last chapter. The next function in the chain, `isEven`, is expecting an `int`, but we are instead passing it an `int -> int`.

In order to fix this, we need to provide `sumSquares` with the first parameter. This will give us a partially-applied single parameter function of type `int -> int`, which is exactly what we need in the chain. The pipe operator will then pass the result of `subtract3` into `sumSquares` as the second parameter, meaning that `isEven` will receive the `int` it is expecting:

```fsharp
let doMaths n =
    n
    |> times4
    |> subtract3
    |> sumSquares 5
    |> isEven
```

The `|>` operator is an extremely important part of writing F# code. It may seem weird at first, but once you have some practice with it, you will find that it makes your code much easier to write and read.

Compare this final version of the code with the original attempt (which had one fewer function in it!). We have removed all the unnecessary extra code and are left with the bare bones of what we are trying to do - call several functions in a row.

As well as being more aesthetically pleasing, `|>` also allows us to quickly build up complex functions from smaller, more basic, functions. A mature F# codebase will typically have lots of little functions which do very specific things, but are combined together to create new functions which are much more complex. This is the essence of functional programming.

## Exercises

Test your understanding of this chapter by completing the following exercises:

1. Write a function which takes a number and returns the sum of that number and the number one greater than it (e.g. inputting `1` returns `3`, and `5` gives `11`).
2. Write another function which takes a single input and adds `9` to it.
3. Write a third function which divides the input by `2`.
4. Write another function which takes two inputs and subtracts the first input from the second. E.g. (if the function was called `subtract`) `subtract 6 10` returns `4`, and `subtract 3 5` returns `2`.
5. Use all these functions, and the `|>` operator to define a function which takes a single input and returns the result of chaining all these functions together in the order in which we defined them, passing the original input in as the first parameter to the function defined in step 4. Check that this final function returns `5` for any number you input.
