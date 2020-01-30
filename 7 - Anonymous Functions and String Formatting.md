# Anonymous Functions and String Formatting

## Printing Other Types

So far we have only printed hard-coded strings. However, `printfn` can do a lot more than just print strings. In fact, the `f` stands for "format" (and the `n` for "new line" as `printfn` automatically puts a new line at the end of whatever we print. We can leave out the new line using the `printf` function, but this is rarely used).

We can pass values into `printfn` using "string formatting". For example, here is a function which takes a string and prints it out:
```fsharp
let printString str = printfn "%s" str
```
This function has type `string -> unit` and if we pass in a string we can see it printed to the console:
```
> printString "hello world!";;
hello world!
val it : unit = ()
```
But how does F# know that the parameter `str` is a `string`? If we try passing in an `int` for example we get:
```
This expression was expected to have type
   'string'
but here has type
   'int'
```
This is because we actually specified that `str` is a string using the string formatting in `printfn`.

Look again at the above function. Notice the first parameter passed into `printfn` is `"%s"`. This `%s` is a placeholder for a string value. We then pass in the value `str` and the compiler knows that `str` has to be a `string` because `%s` requires a `string`. We can add whatever else we want inside the `string` which is passed into `printfn`, for example:
```fsharp
let sayHello name = printfn "Hello %s!" name
```
which prints out:
```
> sayHello "Alice";;
Hello Alice!
val it : unit = ()
```
We can print other types using different placeholders. See the below table:

type | placeholder
:-- | :--:
string | %s
int | %i
float | %f
bool | %b
char |%c

We can pass in multiple values into `printfn` too. Here is an example passing in several of the above types:
```fsharp
let printManyTypes a b c = printfn "int: %i, float: %f, bool: %b" a b c
```
Just by looking at the code (and using the above table for reference) - what is the type of `printManyTypes`? Check your answer by evaluating the function in F# Interactive. Notice the types of the parameters, the order in which they are passed into the printfn string formatting and the order of the `%` signs in the string itself. Remember, it is the `%i`, etc. which tells F# what the types of the inputs are.

If we just wanted to return the string which `printfn` produces, rather than print it out, we can use the `sprintf` function in exactly the same way:
```fsharp
let formatString a b = sprintf "%i is an int. %f is a float." a b
```
This time `formatString` has type `int -> float -> string` because the string is returned rather than printed.

## Anonymous functions

There is an alternative syntax for defining functions in F#. As well as defining a function by giving it a name followed by parameters, we can also use the `fun` keyword and an arrow `->`:
```fsharp
let myFunction = fun a b -> a + b > 0
```
This kind of function (using `fun` and `->`) is called an "anonymous function", or sometimes a "lambda function".
Notice that `myFunction` has type `int -> int -> bool`. We put the parameters after the `fun` keyword, then used a `->`, then wrote the function body. This is equivalent to writing:
```fsharp
let myFunction a b = a + b > 0
```
Defining functions using `fun` makes it much clearer that functions are just values, but the syntax is more verbose so this is not how functions are defined most of the time.

So what's the point of defining functions in this way? Well, the `fun` keyword actually gives us a way of defining functions without giving them a specific name - hence the term "anonymous function". This is useful because it means we can define functions which are going to be passed in as parameters to higher-order functions in-line and without having to name them (because remember, programmers don't like naming things!).

Here's an example:
```fsharp
let printCalculation calc a b =
    let answer = calc (a + b)
    printfn "The answer is: %i" answer 

printCalculation (fun x -> x * x) 4 5
```
First, we define a function where the first parameter is itself a function of type `int -> int`. The next two parameters are `int`s and `printCalculation` returns `unit`. We can call `printCalculation` using an anonymous function by surrounding the anonymous function in brackets `(` `)` so that F# knows it's a single parameter. The above code outputs:
```
> printCalculation (fun x -> x * x) 4 5;;
The answer is: 81
val it : unit = ()
```
We will see many more examples of using anonymous functions later in the guide. They are very common in F# code and can make code much easier to read and understand if used correctly.

## Exercises

Test your understanding:

1. Convert the following into an anonymous function:
    ```fsharp
    let calculate x y = (x + y) * (x - y)
    ```
2. Convert the following into a normal function:
    ```fsharp
    let anon = fun a b -> sprintf "%i - %i = %i" a b (a - b)
    ```
3. Write a function which takes an `int` and returns a `string` with the form `"?? is bigger than 10"` (replacing `??` with the number that was passed into the function) if the number passed in is bigger than `10` and `"?? is smaller than or equal to 10"` otherwise.
4. Rewrite the previous function to take two parameters and return the equivalent strings in the form `"a is bigger than b"` (where `a` is the value of the second parameter, and `b` is the value of the first parameter).
5. Convert the previous function into an anonymous function.