# Discriminated Unions

## What Are Discriminated Unions?

The other major feature of F#s algebraic type system is the ability to create types which may be one of several different types. These types are called "discriminated unions" and are defined like so:

```fsharp
type Shape =
    | Square
    | Triangle
    | Circle
```

This is the first time we have come across the `type` keyword. We use `type` to define our own types which are not already built into F#. We did not need this keyword with tuples because there we were just combining types that already exist, here we are creating something brand new.

When we create values of type `Shape` we have to choose between one of the three options in the type definition above:

```fsharp
let mySquare = Square
let myTriangle = Triangle
let myCircle = Circle
```

Notice that all three of these values have type `Shape`. We can also give each of the options in our discriminated union some data which they can carry by putting `of` and then the type after the name of each case. For example, we could give the side lengths of our shapes:

```fsharp
type Shape =
    | Square of float
    | Triangle of float * float * float
    | Circle of float
```

We now have to update the values we created:

```fsharp
let mySquare = Square 5.0
let myTriangle = Triangle (3.0, 4.0, 5.0)
let myCircle = Circle 3.0
```

## Pattern Matching Discriminated Unions

This is where discriminated unions truly shine. When we create a discriminated union we explicitly state every possibility. This means we can very easily pattern match every possibility and the compiler will also know when we've covered every case. Furthermore, we _have_ to pattern match a value which has a discriminated union type if we want to get the data out that we stored there.

Let's demonstrate this by writing a function which calculates the area of a `Shape`:

```fsharp
let area shape =
    match shape with
    | Square side -> side * side
    | Circle radius -> System.Math.PI * radius * radius
    | Triangle (a, b, c) ->
        let p = 0.5 * (a + b + c)
        sqrt <| p * (p - a) * (p - b) * (p - c)
```

This function has type `Shape -> float` as we might expect. The area of a square is just the side length squared so that was easy enough. The area of a circle is $\pi r^{2}$, $r$ is the radius of the circle which we stored in the definition of `Circle` and we can get the value of $\pi$ from `System.Math.PI`. However, we normally write `open System` at the top of our files so that we don't have to type `System.` before anything from the `System` "namespace" (and then send the line `open System` to F# Interactive), so you may wish to do that instead.

Calculating the area of a triangle from it's three side lengths is a little more involved. The formula we are using is **[Heron's Formula](https://www.mathopenref.com/heronsformula.html)**:

<latex-js baseURL="https://cdn.jsdelivr.net/npm/latex.js@0.11.1/dist/">
\documentclass[18pt]{article}
\begin{document}
$$
Area = \sqrt{p(p - a)(p - b)(p - c)}
$$

$$
\text{where} \quad p = ({a + b + c})/{2}
$$
\end{document}
</latex-js>

So first we define `p` as half the perimeter and then use the built in `sqrt` function to calculate the square root of the product in the above formula.

Let's test our function:

```
> area mySquare;;
val it : float = 25.0

> area myCircle;;
val it : float = 28.27433388

> area myTriangle;;
val it : float = 6.0
```

## The `option` Type

Discriminated unions are so important that F# even has one built in. It is called option and has two cases: Some 'a and None. Here are some examples:

```fsharp
let someInt = Some 25
let nothing = None
```

Notice that `someInt` has type `int option` but nothing has type `'a option`. The compiler has no way of knowing what `'a` should be as we have only used the `None` case. However, we can pass both these values into a function which takes an `int option` parameter:

```fsharp
let trySquare maybeInt =
    match maybeInt with
    | Some x -> x * x
    | None -> 0
```

`trySquare` has type `int option -> int` and returns the square of the number which is passed in in the case of a `Some` value and `0` if a `None` is passed in. Here is what happens if we pass in our previously defined values:

```
> trySquare someInt;;
val it : int = 625

> trySquare nothing;;
val it : int = 0
```

The main reason for the `option` type is so that we can safely check things like whether there is an element in a list. Of course, one way to do this would be to use `List.exists` first (which returns a `bool` telling us if such an element is in the list) and then write two different cases for `true`/`false`. However, a much easier way to do this same operation is to use `List.tryFind` which returns an `option`. We can then pattern match on the `option`, getting the value itself in the `Some` case and handling the `None` case however we like. Here's an example:

```fsharp
let tryGetEvenNumber numbers =
    let maybeN =
        numbers
        |> List.tryFind (fun x -> x % 2 = 0)
    match maybeN with
    | Some n -> printfn "We found %i!" n
    | None -> printfn "No even numbers found."
```

This function has type `int list -> unit` and prints out the first even number in the given list, and a message saying there are no even numbers if not. Most of the work is done by `List.tryFind` - we pass in a function which checks if a number is even and `List.tryFind` returns `Some x` where `x` is the first number in the list which returns `true` for the function we passed in, and `None` otherwise. Then all we have to do is pattern match on the result.

Here's what happens if we pass in a list with an even number, and one without:

```
> tryGetEvenNumber [ 1 .. 2 .. 11 ];;
No even numbers found.
val it : unit = ()

> tryGetEvenNumber [ 1; 3; 5; 7 ];;
No even numbers found.
val it : unit = ()
```

There are plenty of other built in F# functions with "try" in the name (`List.tryItem` for example) which generally return an `option`. Now that you know how to use these functions it is recommended that you use them whenever possible and handle both the `Some` and `None` cases. For example, your code will just stop if you use `List.find` and no element is found, however if you use `List.tryFind`, the compiler will force you to handle the case when an element isn't found, which is a good habit to have.

## Exercises

Let's test your understanding of discriminated unions with the following exercises:

1. Create a discriminated union type called `Milkshake` with several different flavours, then write a function which takes a value of type `Milkshake` and returns the name of the flavour as a `string`.
2. Create a discriminated union type called `Pet` with three cases: `Dog`, `Cat`, `Other`. The `Dog` and `Cat` cases should each take an `int` representing the age of the pet in years.
3. Write a function which takes a list of `Pet`s and returns the sum of the ages of the pets.
4. If you did the previous exercise using `List.fold` try doing it with `List.sumBy`, which you may have to look up, instead. Visa versa if you used `List.sumBy`.
5. Write a function which takes a list of `int`s and returns the first number which is divisible by `3` or `5`, and `0` if no element is found.
