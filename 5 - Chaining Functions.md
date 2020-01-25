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

What if we wanted to check if a number was not divisible by 5? We could use our knowledge of how modulus works are check if the number has a remainder of 1, 2, 3 or 4, but this would be rather verbose. Instead, we should use the `not` function which is build into F#.

`not` has type `bool -> bool` and returns the negation of the input (so `not true` returns `false` for example).

Let's use this to write a function which checks if a number is not a multiple of 5:
```fsharp
let notMultipleOf5 n = not (n % 5 = 0)
```
We need the brackets to tell the compiler that everything inside the brackets should be passed into `not`. This function, of course, has type `int -> bool`.

Notice the blue squiggly line that VS Code puts underneath the function body. If we mouse over it (or open the "Problems" tab next to the terminal) we see the following message:

>`not (a = b)` might be able to be refactored into `a <> b`

VS Code is telling us that we should use the "not equals" operator (`<>`) rather than passing a value into the `not` function. This is a sensible suggestion - it will make our code easier to read and eliminate the need for pesky brackets:
```fsharp
let notMultipleOf5 n = n % 5 <> 0
```
