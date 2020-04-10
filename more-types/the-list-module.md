# The List Module

The `List` module has many useful functions. Let's finish off our study of lists by looking at several commonly used, and particularly useful, functions.

## `List.filter`

Suppose we want to remove several items from a list according to some rule. For example, we have a list of integers and want only the even ones. `List.filter` is the function we need. 

This function has type `('a -> bool) -> 'a list -> 'a list`. It takes a function (often called a "predicate" meaning "function that returns a `bool`") and a list and calls the given function on each item in the list returning a list of the items where the predicate returned `true` - i.e. `List.filter` "filters" out any items which do not meet the given condition.

Here's how we might get just the even numbers from a list:

```fsharp
let evenOnly numbers =
    numbers
    |> List.filter (fun x -> x % 2 = 0)
```

Let's try it out on the numbers from 1 to 10:

``` {highlight: [2]}
> evenOnly [ 1 .. 10 ];;
val it : int list = [2; 4; 6; 8; 10]
```

`List.filter` is the most common way to remove elements from a list.

## `List.fold`

Now suppose we want to collapse a list down to a single value. For example, we want to add up all the numbers in a list. In general, we can use the function `List.fold` to "fold" up the list into a single value.

`List.fold` has type `('a -> 'b -> 'a) -> 'a -> 'b list -> 'a` which is easily the most crazy looking type we've seen so far, so let's break it down.

The first parameter is a function of type `'a -> 'b -> 'a` meaning it takes two parameters and returns something which has the same type as the first parameter.

The second parameter of `List.fold` is a value with the same type as the first parameter and return value of the function we need to pass in.

The final parameter is a `'b list`. This is the same `'b` as the second parameter of the function we need to pass in. This suggests that the function we need to pass in is going to be applied to items in this list.

The return type of `List.fold` is `'a`. So we need to pass in a value of type `'a` and we get a `'a` out at the very end. We also need to pass in a `'b list`, and a function which takes an `'a` and a `'b` and returns an `'a`.

What `List.fold` actually does is call the function we pass in on every item in the list we pass in and accumulates the result. This result is passed in with the function for the next item in the list, and so on. We also need to provide a starting value for this to work. Let's see an example:

```fsharp
let sumList numbers =
    numbers
    |> List.fold (fun total value -> total + value) 0
```

`sumList` has type `int list -> int`. The compiler can work this out because we've passed in an `int` (`0`) as the parameter of type `'a`, and we've used `+` so `'b` must also be an `int`, hence `numbers` has type `int list`, and the function returns `int` overall.

What `List.fold` is going to do is call the anonymous function on the first item in `numbers` with `total` being equal to the starting value that we also passed in (`0`) and `value` being the first item in `numbers`.

Next, `List.fold` will do the same with `total` being whatever the result of the function (`total + value`) was last time and `value` being the second item in `numbers`, and so on until the end of the list. Let's try it on a few examples:

``` {highlight: [2]}
> sumList [ 1; 2 ];;
val it : int = 3
```

As we might expect, we get `3` as the result. This is because the following calculations were done:

```
0) 0 + 1 = 1
1) 1 + 2 = 3
```

Let's try with a different list:

``` {highlight: [2]}
> sumList [ 10; 11; 12 ];;
val it : int = 33
```

This time the calculations were:

```
0) 0 + 10 = 10
1) 10 + 11 = 21
2) 21 + 12 = 33
```

As you can see, at each step the result of the previous step is being passed in as `total` and the next item in the list is being used as `value`.

Interestingly, VS Code shows the following message under `sumList`

> If `( + )` has no mutable arguments partially applied then the lambda can be removed.

What this means is we can actually write our function as:

```fsharp
let sumList list =
    list
    |> List.fold (+) 0
```

because `(+)` is `+` in "function" form (i.e. `(+) a b` is the same as `a + b`). All operators in F# can be written in this way if we need to use them like normal functions. When using an operator in between two values such as `a + b` we say `+` is an "infix operator", whereas `(+) a b` is using the operator `+` as a function.

Notice now that VS Code has another message for us:

> `List.fold ( + ) 0 x` might be able to be refactored into `List.sum x`.

This is because the `List` module already has a built in function which can sum a list for us - `List.sum`.

Let's write a function which is not simply a more complicated form of a built in `List` module function.

Here's a function which takes a list of `int`s and adds up each number, multiplying the number by `-1` if it is less than zero:

```fsharp
let positiveSum numbers =
    numbers
    |> List.fold (fun total value ->
        match value > 0 with
        | true -> total + value
        | false -> total - value) 0
```

This time we check each value to see if it is `> 0`, if it is we add the value to the total, otherwise we subtract the value (e.g. add `-1 * value`). Let's test it on a list of positive and negative numbers:

``` {highlight: [2]}
> positiveSum [ -1; -1; 1; 1 ];;
val it : int = 4
```

If you find yourself writing a function like this - with a multi-line anonymous function - make sure the body of the function is indented correctly. If the function you are writing is particularly complex you may want to write a named function instead and pass it in as a parameter.

## Other Useful `List` Module Functions

To finish of this chapter, here are some other useful functions from the list module:

| function         |               type               | description                                                                                                                              |
| :--------------- | :------------------------------: | :--------------------------------------------------------------------------------------------------------------------------------------- |
| `List.min`       |         `'a list -> 'a`          | Finds the minimum element (i.e. the smallest number if it's a list of numbers), there also a `List.max` for finding the maximum element. |
| `List.average`   |         `'a list -> 'a`          | Finds the average of a list of numbers.                                                                                                  |
| `List.sort`      |       `'a list -> 'a list`       | Sorts a list in ascending order, there is also `List.sortDescending` for sorting in descending order.                                    |
| `List.contains`  |     `'a -> 'a list -> bool`      | Checks if an element is in a list.                                                                                                       |
| `List.distinct`  |       `'a list -> 'a list`       | Removes duplicates from a list.                                                                                                          |
| `List.find`      | `('a -> bool) -> 'a list -> 'a`  | Returns the first element in the list which satisfies the predicate (i.e. returns `true` for the function you pass in).                  |
| `List.findIndex` | `('a -> bool) -> 'a list -> int` | Similar to `List.find` but returns the index instead of the element.                                                                     |

There are many other functions in the `List` module which you can look up as you progress with F#. Full documentation of the list module can be found [here](https://msdn.microsoft.com/en-gb/visualfsharpdocs/conceptual/collections.list-module-%5bfsharp%5d).

## Exercises

We've done a lot of work with lists. Let's put that knowledge to the test. Complete the following:

1. Write a function which takes a list of `int`s and returns a list with only the elements from the original list which are multiples of 3 or 5.
2. Write a function which takes a list of `char`s and returns a list with only those `char`s which are not vowels. You can assume that your input list only contains lower case `char`s.
3. Write a function which takes a list of `string`s and returns a single string made up of every string in the original list separated by a ` - `. (Don't worry if there is an extra `-` at the start or end of the returned string.)
4. Write a function which takes a list of `int`s and uses `List.fold` to find the difference between the sum of all the even numbers and the sum of all the odd numbers.
5. Use `List.fold` to write a function identical in behavior to `List.map` - i.e. a function which takes a function (of type `'a -> 'a`) and a list (of type `'a list`) and returns a list (of type `'a list`) where each element in the returned list corresponds to applying the given function to the element at that position in the input list.
6. Use `List.fold` to write a function identical in behavior to `List.filter`.

<note>

Exercises 5. and 6. are hard. Don't worry if you can't solve them, either move on or try to look up the solutions online and *understand them*. The point is simply to make you realise the power of `List.fold`, it can be used to implement many of the other `List` module functions (and indeed this is how they are implemented).
 
</note>