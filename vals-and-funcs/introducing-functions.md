# Introducing Functions

## What Are Functions?

In F#, a function is a value which takes an input, and returns an output. Functions are useful if you want to use the same piece of code multiple times with different inputs, because you can write the function once and refer back to it whenever you need it without having to rewrite the code (just like with normal values).

## Defining A Function

The syntax for defining a function is almost identical to the syntax for defining a value (because functions are actually just values in disguise!). Let's define a function that multiplies an integer by two:

```fsharp
let timesBy2 input = input * 2
```

In the above code `timesBy2` is the name of the function, and `input` is the name of the parameter that we are passing in. We can call our functions and parameters whatever we like. To return a value from the function we just need to write the value that we want, which in this case is the input multiped by 2.

We can call our function by writing the name of the function followed by the value we wish to pass in as the parameter:

```fsharp
timesBy2 8
```

Sending both these lines to F# interactive gives the following output:

``` {highlight: [3, 4]}
> let timesBy2 input = input * 2
- timesBy2 8;;
val timesBy2 : input:int -> int
val it : int = 16
```

And we get `16` as expected.

Notice that the type of `timesBy2` is `int -> int`. What's going on here?

Well, the compiler has worked out that we've defined a function which takes an `int` and returns an `int`. The arrow (`->`) indicates that we are taking something of the type on the left to something of the type on the right.

Here's another example:

```fsharp
let isBiggerThan10 n = n > 10
```

This time `isBiggerThan10` has type `int -> bool`. The `>` is the "greater than" boolean operator and returns true if the number on the left is bigger than the number on the right. (There is also `<` which is "less than", as well as "greater than or equal to" `>=` and "less than or equal to" `<=` operators).

Trying calling this function on `5` and then `15`. You should get the following output:

``` {highlight: [4, 7]}
> let isBiggerThan10 n = n > 10
- isBiggerThan10 5;;
val isBiggerThan10 : n:int -> bool
val it : bool = false

> isBiggerThan10 15;;
val it : bool = true
```

As expected, `5` is not bigger than `10` and `15` is bigger.

## Type Inference

With both these above functions, how did F# know that we were going to pass in an `int`? We didn't tell it that we wanted to pass in an `int`.

However, if we tried to pass in a `float` we would get the following error:

``` {highlight: ['4-7']}
> let isBiggerThan10 n = n > 10
- isBiggerThan10 5.5;;

Script.fsx(14,16): error FS0001: This expression was expected to have type
    'int'
but here has type
    'float'
```

Of course, we discovered in the last chapter that `float`s and `int`s cannot interact (and this is again true with boolean operators like `>`) so the error is expected. The F# compiler figured out that because we were applying our inputs to operators with `int`s that our inputs must also be `int`s. This is an F# feature called "type inference" and is extremely useful.

Type inference means that - in most cases - we do not need to tell F# what types our functions take as inputs, instead F# will work them out itself and stop us from passing in parameters of the wrong type. As we've seen, types are extremely important in F# and having a compiler which keeps track of types for us means that we don't have to worry about them so much, safe in the knowledge that the compiler is doing the work for us.

## Functions With Multiple Parameters

Functions with one parameter are good, but there is nothing stopping us adding more parameters. Let's define a functions which multiplies two numbers together and checks to see if the result is bigger than 100:

```fsharp
let isLarge a b =
    let product = a * b
    product > 100
```

This function is a little more complicated. Firstly, we multiply the two inputs together and bind the result to the value `product`. This is done in exactly the same way as we did in the last chapter. The only difference is that the value `product` is only defined inside this function block.

We tell F# what is inside a block by indenting our code by the same amount (in this case 4 spaces, or 1 tab). If the indentation isn't consistent then the compiler will throw an error. Composing `let` blocks like this is a great way to build up complex functions.

Now we just need to check if `product` is bigger than `100` and return the result. This is done by using the `>` operator, then whatever is on the last line of the function block is returned by the function.

Let's run our code with `5` and `10` as inputs:

``` {highlight: [5, 6]}
> let isLarge a b =
-     let product = a * b
-     product > 100
- isLarge 5 10;;
val isLarge : a:int -> b:int -> bool
val it : bool = false
```

As expected, `5 * 10` is not bigger than `100`.

Notice the type of the function this time is `int -> int -> bool`. This means a function which takes two `int`'s and returns a `bool`. The reason for writing the type in this way (with two `->`s) will become clear in later chapters.

Notice again that the compiler worked out that the inputs both needed to be of type `int`. Of course we could also have written this function on a single line:

```fsharp
let isLarge a b = a * b > 100
```

## Exercises

Let's test your understanding of functions. Answer the following:

1. By just looking at the code below, what is the type of the following function:

```fsharp
let whatType x y = x - y <= 13.2
```

paste the code into F# interactive and check your answer.

2. Write a function which takes three inputs and multiplies all three together.
3. Write another function which takes three inputs and adds them together.
4. Write another function which takes three inputs and checks whether the sum of all three is greater than the product of all three inputs. Try and use the functions from exercises 3. and 4. instead of writing the code from scratch.
5. Write a function which takes a `unit` and returns a `string`.
