# Recursion

## What Is Recursion

No functional programming tutorial would be complete without a mention of recursion. Recursion is simply a function calling itself, and is an extremely powerful technique. Let's see an example:

```fsharp
let rec factorial n =
    match n with
    | x when x <= 1 -> 1
    | _ -> n * factorial (n - 1)
```

Here we define the `factorial` function (with type `int -> int`), using the `rec` keyword because `factorial` is a recursive function.   

In mathematics the factorial of a positive integer is defined as the product of that integer with all positive integers lower than it. We can write a function which does this by first checking if the input is less than or equal to `1`, in which case we return `1`, otherwise we return the product of the input `n` with the `factorial` of `n - 1`. This results in the following computation for `n = 3`:

```
factorial 3 
    = 3 * factorial 2
    = 3 * 2 * factorial 1
    = 3 * 2 * 1 
    = 6
```

To show that this is really what is happening, let's add the line `printfn "factorial %i" n` as the first line in our function and test it out:

``` {highlight: [5]}
> factorial 3;;
factorial 3
factorial 2
factorial 1
val it : int = 6
```

As you can see `factorial` was called three times, each time with input one less than the previous time.

In general when we define a recursive function we need to define a "base case" when the recursion should stop - i.e. `n <= 1` in `factorial` - and then a way of calling the function again so that we get down to that base case - i.e. calling `factorial (n - 1)`.

Let's see another example, this time using recursion to compute the sum of a list of integers:

```fsharp
let sum numbers =
    let rec loop nums total =
        match nums with
        | [] -> total
        | n::tail -> loop tail (total + n)
    loop numbers 0
```

Here we define `sum` of type `int list -> int` but the recursive function is actually `loop` (which has type `int list -> int -> int`) defined inside `sum`. 

`loop` takes a list of integers and another integer which is used to hold the sum or all integers in the list so far. `loop` then takes the first item off the list (using pattern matching and the `::` operator) and adds it to `total` before calling `loop` again with the remaining items in the list. The recursion terminates when the list is empty and `total` is returned. We call `loop` for the first time with out input with `numbers` and `0`, and the result of that function call is what is returned from `sum`. 

Let's test our function:

``` {highlight: [2]}
> sum [ 1 .. 5 ];;
val it : int = 15
```

Notice that when `loop` is called recursively, no other operations are being done. `loop` simply takes two new inputs. Compare this with `factorial` where we multiplied our current input with the result of the recursive call. This difference is important. If the very last that that a function does is call itself again we say that it is "tail recursive". Tail recursive functions are much more efficient than non-tail recursive functions (for technical reasons which we won't go into). If you are curious, here is what a tail recursive factorial function looks like:

```fsharp
let tailFact n =
    let rec loop k total =
        match k with
        | x when x <= 1 -> total
        | _ -> loop (k - 1) (total * k)
    loop n 1
```

Notice again the use of an inner `loop` function with a `total` parameter to keep track of the ultimate return value. This is a common pattern when writing tail recursive functions. Don't worry too much about writing tail recursive functions for now, it is more important to understand how recursion works.

## Recursive Types

We can also define types that refer to themselves. This is how we can define a data structure known as a "[binary tree](https://en.wikipedia.org/wiki/Binary_tree)". A binary tree consists of nodes each containing some data and two child nodes (apart from the "leaf" nodes which have no children). Here's how we can define a binary tree in F#:

```fsharp {highlight: ['3-4']}
type Node = {
    id : int
    left : Node option
    right : Node option
}
```

As you can see the definition of `Node` includes `Node` itself. Here `id` has type `int` but it could be any type (or even a generic type). `left` and `right` are `Node option`s because a `Node` might not have a child. More general tree and graph types are defined in a similar way. We can create values of type `Node` like so:

```fsharp
let tree = {
    id = 1
    left = Some { id = 2; left = None; right = None }
    right = Some { id = 3; left = None; right = None }
}
```

`tree` is a fairly simple tree with a root node `1` and two child nodes `2` and `3`.

How might we work with such a data structure? Using recursion of course. Here is a function which traverses a tree (using "[depth first search](https://en.wikipedia.org/wiki/Depth-first_search)") and returns a list of all the `id`s:

```fsharp
let getIds tree =
    let rec loop node total =
        match node.left, node.right with
        | Some l, Some r -> total @ (loop r [ r.id ]) @ (loop l [ l.id ])
        | Some l, None -> total @ (loop l [ l.id ])
        | None, Some r -> total @ (loop r [ r.id ])
        | None, None -> total
    loop tree [ tree.id ]
```

Looking at the inner function `loop` which has type `Node -> int list -> int list`, we pattern match on the possible combinations of left and right children of our input `node`. Any time there is a child, we pass that child back into `loop` together with a list consisting of the `id` of that child. We then append the resulting list to `total`. Of course if we have a leaf node there are no children (`| None, None`) so we just return `total`. Here's what happens when we pass in our previously defined `tree`:

``` {highlight: [2]}
> getIds tree;;
val it : int list = [1; 3; 2]
```

Don't worry if you don't understand this function or how it works, there's a lot going on here. The important thing is that you understand that we can recursively call functions, and one extremely useful application of this is to traverse recursive data structures.

### Mutually Recursive Types

To finish off this chapter, let's quickly look at types which refer to other types that are defined in terms of that first type. These types are known as "mutually recursive types" and are defined like so:

```fsharp
type One =
    | Second of Two

and Two =
    | First of One

let rec one = Second two
and two = First one
```

This is a totally contrived example, but this technique is sometimes very useful. Notice the use of the `and` keyword which let's the compiler know that this type or value is referring to a previously defined type or value. If you ever see code like this, now you know what's going on!

## Exercises

We've covered a lot this chapter, let's test your understanding of the concepts:

1. Write a function which calculates the nth term in the Fibonacci Sequence.
    
    <note>
    
    The Fibonacci Sequence starts `1, 1, 2, 3, 5, ...` and each term is calculated by adding together the previous two terms.

    </note>

2. Write a recursive function identical in behaviour to `List.map`. I.e. a function which takes a "mapping function" and a list and returns the input list with each element transformed according to the mapping function. (This is in fact how the `List` module functions are ultimately implemented).
3. Here is the definition for a very simple "tree" (known as a "[linked list](https://en.wikipedia.org/wiki/Linked_list)") where each node has at most one child:
    ```fsharp
    type Node = {
        data : int
        next : Node option
    }
    ```
    create a value using this type with nodes `1, 2, 3, 4` (in that order).
4. Write a function which computes the sum of a linked list. Check that the sum of the linked list you defined in the previous exercise is `10`.
5. Write a function which returns the last element in a linked list (i.e. it should return `4` when given your previously defined list).

<note>

F# lists are actually implemented as linked lists. This is why it is easy to split a list into it's first item and the remaining list, and also why `List` modules functions are implemented using recursion.

</note>