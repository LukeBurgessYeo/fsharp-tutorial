# Introducing Lists

## What Is A List?

Lists are what you might expect them to be - an ordered collection of values. Let's see an example:

```fsharp
let myList = [ 1; 2; 3 ]
```

Here we declare a list by putting the values we want in the list between square brackets `[` `]` with values separated by semi-colons `;`. The spaces are not required but do make the code more readable.

Notice that the type of `myList` is `int list`. Lists can only contain values of the same type. Try changing `2` (which is an `int`) to `2.0` (which is a `float`) you should see the following error:

> All elements of a list constructor expression must have the same type. This expression was expected to have type 'int', but here has type 'float'.

We can create lists with any F# type (including other lists!). But what if we want a list of values of more than one type? Well there is a way to do this, but it will have to wait until later in the guide.

## Creating Lists

### List Comprehension

As well as typing out each value we want in a list, F# gives us a few other ways to create lists.

Firstly, if we just want all the numbers between 1 and 10, for example, we can use F#'s "range" operator `..`:

```fsharp
let oneToTen = [ 1 .. 10 ]
```

If we send this to F# Interactive, we can see the whole list:

``` {highlight: [2]}
> let oneToTen = [ 1 .. 10 ];;
val oneToTen : int list = [1; 2; 3; 4; 5; 6; 7; 8; 9; 10]
```

Note that both `1` and `10` are part of the resulting list.

We can also use two `..`s to define the step size when creating a list:

```fsharp
let oneToOneHundred = [ 1 .. 10 .. 100 ]
```

This gives us a list starting at `1` with each term being `10` more than the next until we reach `100`:

``` {highlight: [2]}
> let oneToOneHundred = [ 1 .. 10 .. 100 ];;
val oneToOneHundred : int list = [1; 11; 21; 31; 41; 51; 61; 71; 81; 91]
```

Notice that `100` is not part of the resulting list this time. That's because 91 + 10 is greater than 100, so the last term that is `91`.

A statement like `[ 1 .. 10 .. 100 ]` is known as a "list comprehension". We can also pass in values, for example here is a function which produces a list of numbers from 1 to 100 with a step size that we can pass into the function:

```fsharp
let customStep n = [ 1 .. n .. 100 ]
```

Try passing in a few different values of `n` and see what happens.

### `List.init`

What if we wanted to create more complicated lists than just numbers increasing by the same step each time? Well, F# has a built in function called `List.init` which we can use to do just that.

`List.init` has type `int -> (int -> 'a) -> 'a list`. The first parameter is the length of the desired list. The second parameter is a function which takes the "index" (a number telling us how far along the list an element is) of the current element and returns whatever type we like. `List.init` then returns a list with elements of that same type.

Let's see a concrete example. Here's how we can use `List.init` to create a list of square numbers:

```fsharp
List.init 10 (fun index -> index * index)
```

We pass in `10` because we want a list with 10 elements in it, then we pass in an anonymous function of type `int -> int` which takes a number and squares it. Evaluating this statement gives:

``` {highlight: [2]}
> List.init 10 (fun index -> index * index);;
val it : int list = [0; 1; 4; 9; 16; 25; 36; 49; 64; 81]
```

Curiously, the resulting list starts from `0`. This is because, in F# and many other programming languages, we start counting the items in a list from `0` (for [historical reasons](https://en.wikipedia.org/wiki/Zero-based_numbering)). `List.init` creates a list by passing in the index of each element in the list (starting from `0`) into the function which we define using the `fun` keyword and using the value which is returned from that function in that position in the list.

This can be seen more clearly by passing in a function which simply returns whatever value it is given:

```fsharp
List.init 10 (fun i -> i)
```

Although VS Code tells us that:

> "fun x -> x might be able to be refactored into id."

`id` is a built in F# function which simply returns whatever value it is given. Let's use this to simplify our code by passing in the named function `id`:

```fsharp
List.init 10 id
```

Passing this into F# Interactive gives:

``` {highlight: [2]}
> let basicList = List.init 10 id;;
val basicList : int list = [0; 1; 2; 3; 4; 5; 6; 7; 8; 9]
```

We can now clearly see that `List.init` is passing in the numbers 0, 1, 2, etc.

Knowing that we can pass in named functions as well as anonymous functions, we could have written our squared numbers example as:

```fsharp
let squareNumber x = x * x
List.init 10 squareNumber
```

However, it is often much more convenient to write an anonymous function because we only need the function in this one place and don't need to refer to it again.

## Working With Lists

We have seen how we can create simple lists using the `..` operator, as well as more complex lists using `List.init` so now let's look at how we can work with existing lists. The main way we work with lists is by using functions in the F# `List` "module". Modules are just groups of functions which we can access using the `.` syntax. We have already seen an example in the form of `List.init`. `List` is the name of the module, `init` is the name of the function inside that module. Let's look at some more functions from the `List` module.

### `List.iter`

`List.iter` is a function which allows us to "iterate" (take each element of the list one at a time) over a list and "do something" with each element.

`List.iter` has type `('a -> unit) -> 'a list -> unit`. The first parameter is a function which takes some type `'a`, which could be any F# type, and returns `unit`. The second parameter has type `'a list` (notice `'a` is the same as the `'a` in the function that is the first parameter). `List.iter` then returns `unit`. Just from the type which `List.iter` has, we can guess that the function which we pass in is going to be applied to the elements of the list.

This is indeed what `List.iter` does. We pass in a function and `List.iter` calls that function on each element in the list in turn. One of the main uses for `List.iter` is to print every item in a list:

```fsharp
let numbers = [ 1 .. 100 ]
List.iter (fun x -> printfn "%i" x) numbers
```

Because `numbers` is an `int list` we need to use `%i` to print each element of the list. Running this code in F# Interactive produces:

``` {highlight: [2, 3, 4]}
> let numbers = [ 1 .. 100 ]
- List.iter (fun x -> printfn "%i" x) numbers;;
1
2
...
```

However, there is a more idiomatic way to write the above code - using the `|>` operator to pass in the list as the last parameter to `List.iter`. Also, in this case, we do not need to explicitly write the anonymous function. We can in fact use partial application with `printfn`:

```fsharp
[ 1 .. 100 ]
|> List.iter (printfn "%i")
```

This is much more like the sort of code you would see from experienced F# programmers (who don't want to name values or parameters). The brackets around `printfn "%i"` tell the compiler that `printfn "%i"` is a single parameter being passed into `List.iter`. To see why this works notice that the function:

```fsharp
let printNumber = printfn "%i"
```

has type `int -> unit` and so is a completely valid function which we can pass into `List.iter`.

`List.iter` also has a sister function called `List.iteri` which makes use of the index of each item in the list.

`List.iteri` has type `(int -> 'a -> unit) -> 'a list -> unit` which is very similar to the type of `List.iter` just with an extra `int` as the first parameter of the function which we pass in.

This `int` corresponds to the index of the element in the list. This can be useful if we want to keep track of the number of items in our list. For example, the below code creates a list of square numbers and then prints out each number together which the number that was squared to produce it:

```fsharp
List.init 100 (fun i -> i * i)
|> List.iteri (fun index number -> printfn "%i * %i = %i" index index number)
```

Try experimenting with this code - changing the size of the list and the function parameters - to understand what each of the List functions does, as well as the functions being passed in as parameters.

### `List.map`

Suppose we had a list but we wanted to transform each element of the list. Perhaps we have a list of numbers and we want to know which are odd and which are even. The function that lets us do this is `List.map`.

`List.map` has type `('a -> 'b) -> 'a list -> 'b list`. This looks a little crazy, so let's break it down.

The first parameter is `('a -> 'b)` which is a function that takes some type `'a` and returns another (maybe the same, maybe different) type `'b`. For example, it could be a function which takes an `int` and returns a `bool`.

The next parameter has type `'a list`, i.e. a list with elements of the same type as the parameter of the function which we just looked at (e.g. it must be an `int list` if we passed in a function of type `int -> bool`).

Finally `List.map` returns a `'b` list, i.e. a list which has the same type as the return type of the function we passed in. So if we passed in an `int -> bool` function we would get a `bool list` from `List.map`.

Just with this information we can take a guess at what `List.map` actually does. It takes each element from the list we pass in, calls the function we pass in on that element, and returns a new list made up of the values that were returned by the function we passed in.
Here's an example:

```fsharp
[ 1; 3; 5; 8; 12; 102 ]
|> List.map (fun x -> x % 2 = 0)
```

`List.map` takes each element of the list one at a time and "maps" it to a new value using the function which we pass in as the first parameter. In this case we take a list of `int`s and check if each element is even (using `%`). This gives the following result:

``` {highlight: [3]}
> [ 1; 3; 5; 8; 12; 102 ]
- |> List.map (fun x -> x % 2 = 0);;
val it : bool list = [false; false; false; true; true; true]
```

Notice we get a `bool list` - as we passed in a `int -> bool` function - with each element in the list corresponding to the whether or not the corresponding element in the original list was even. Importantly, this is a new list. F# does not change the input list. We can check that this is the case by assigning the input list to a value and checking it before and after we've passed it into `List.map`:

``` {highlight: [2, 5, 8]}
> let numbers = [ 1; 3; 5; 8; 12; 102 ];;
val numbers : int list = [1; 3; 5; 8; 12; 102]

> let evens = numbers |> List.map (fun x -> x % 2 = 0);;
val evens : bool list = [false; false; false; true; true; true]

> numbers;;
val it : int list = [1; 3; 5; 8; 12; 102]
```

As you can see, numbers is the same before and after using `List.map`, so evens is in fact a brand new list. In general in F# we cannot change values once they have been created, instead we create new values with whatever changes we want to apply. That way we can always refer back to the original value knowing that it hasn't been changed unexpectedly.

`List.map` also has a sister function `List.mapi` which is defined in much the same way that `List.iteri` is defined given `List.iter`. `List.mapi` has type `(int -> 'a -> 'b) -> 'a list -> 'b list`. Again, this is very similar to the type of `List.map` but with an extra `int` parameter in the function which we pass in. As before, this `int` corresponds to the index of the element which the function is being applied to. For example:

```fsharp
[ 'a' .. 'z' ]
|> List.mapi (fun index letter -> sprintf "Letter number %i is %c" index letter)
```

Yes, we can use the `..` operator with `char`s too.

## Exercises

The `List` module functions that we have seen so far are extremely important for working with lists. Make sure you understand them before moving on to the next chapter:

1. Use list comprehension (i.e. the `..` operator) to write a function which takes an `int` (called `n` for example) and returns a list from `0` to `n` in steps of `3`.
2. Rewrite the previous function using `List.init`.
3. Use `..` to create a list of all integers from `1` to `20` then, using only `List.iter`, print out all the squares of integers from `1` to `20`.
4. Write a function which takes an `int list` and returns a `bool list` where an element is `true` if the corresponding element in the original list was divisible by 3, and `false` otherwise.
5. Write a function which takes an `int list` and returns a `string list` where each element in the input list has been replaced with `"zero"` if the input element was `0`, `"positive"` if the input element was positive, and `"negative"` if the input was negative.
