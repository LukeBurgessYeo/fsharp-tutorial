# The Basics

## Bindings And Values

So we've written and executed our first F# program. Now it's time to properly learn the language and how to program in it. The first thing we are going to look at is defining "values". The code below defines a value called `x` and binds the integer `5` to it:
```fsharp
let x = 5
```
Notice that VS Code puts a grey `// int` after the 5. This is the data-type of the value `x`. The keyword `let` binds whatever comes after the `=` to the name which you put before the `=`.

`let` bindings are useful because we may want to define a value which we refer to multiple times later on in our code. For example, we may want to multiply several numbers by the same amount, so defining a value with that amount and  referring to the value instead means we only have to change one line of code in order to change how much we multiply by each time.

Another use of values comes when we are retrieving data from somewhere and we don't know what that data is. In such cases we can just bind a value and refer to the value so we don't need to know what the data is.

Add the following line below:
```fsharp
let y = 10.5
```
Now VS Code is telling you that `y` is of type `float`. `float` and `int` are two different types of numeric value. They cannot interact with each other. Try adding `x` and `y` together using the `+` operator:
```fsharp
let z = x + y
```
Notice several things:

1. `z` has type `'a` which in this case means that the compiler doesn't know the type.
2. The `+` and `y` are underlined with red squiggles.
3. There is a number 2 in the "problems" tab of the terminal (you can click on the tab to see the problems).
4. If you hover your mouse over `y` you can see a message: "The type float does not match the type int".
5. If you send all three lines to F# Interactive (by highlighting all three and pressing `ALT+ENTER`) you get the following output:

```
> let x = 5
- let y = 10.5
- let z = x + y;;

Script.fsx(4,13): error FS0001: The type 'float' does not match the type 'int'
```
What's going on here? 

## Types

F# considers integers (`int`) and decimals (`float`) to be different things and so does not let you add them together (or any other maths operation - `-`, `*`, `/` - for that matter).

If we want to add these numbers together we are going to have to make `x` into a `float`. The simplest way to do this is to change `x` to be `5.0`. Then the compiler will detect that `x` is now a `float` and the code will run as expected:
```
> let x = 5.0
- let y = 10.5
- let z = x + y;;
val x : float = 5.0
val y : float = 10.5
val z : float = 15.5
```
The other primitive types which are of interest are:

Name | Type | Example
--- | --- | ---
String | `string` | `"Hello World!"`
Boolean | `bool` | `true`/`false`
Unit | `unit` | `()`

Let's start with `string`s. These are the type that is used to represent written words (in English, for example). Next, `bool`s are used to represent either `true` or `false`. They cannot be anything else, so if you have something of type `bool` you know it is either `true` or `false`.

The last type, `unit`, is a little strange. It represents the lack of any other type. It is useful, however, because everything in F# must have a type. This includes statements like `printfn "Hello World!"` which does not have a natural type like the number 5, or a string like "hello world". Therefore it is given the type `unit`. `unit` is treated just like any other type in F# and so cannot interact with other types.

## Exercises

Now that we've seen some basic F#, let's put your knowledge to the test. Complete the following tasks:

1. Use `let` to bind `7` to the value `x`.
2. Define another value, `y`, equal to `2`.
3. Use the `/` operator to divide `x` by `y` and bind the result to `z`.
4. Run the code you've written in F# interactive and check that `z` has type `int` and value `3`.
5. Why do we get `val z : int = 3` and not `3.5`?