# More Lists

## Getting Items From Lists

Let's look at how to retrieve an item from a list at a given index. This can be done using the `List.item` function from the `List` module. `List.item` has type `int -> 'a list -> 'a`. As you might expect, the `int` parameter is the index of the item we want, and the returned value of type `'a` is the element itself:

```fsharp
let nthLetter n =
    [ 'a' .. 'z' ]
    |> List.item (n - 1)
```

Here `nthLetter` has type `int -> char` and returns the `n`th letter of the English alphabet. Notice that `n - 1` is passed into `List.item` as the first parameter because `List.item` counts elements in the given list from 0, but for our function we want to count from 1. This way we get `a` when passing in `1`.

Of course, this function won't work if we pass in a number less than `1` or greater than `26`, as we cannot get the "-1th" item, or the 100th item. In this case we know our list has 26 elements in it, but in general we might not know how long our list is. F# has the `List.length` function (of type `'a list -> int`) which takes a list and returns its length.

## Adding Items To Lists

As mentioned previously, once a list has been created it cannot be modified - we cannot change the value of any element, we cannot add elements, we cannot remove elements. However, we can create a new list with the changes we want (as we saw with `List.map` for example). This is what we really mean when we talk about "changing" a list. It is simply more concise to speak as if we are _modifying_ the list when in fact we are creating a _new_ list.

With that being said, we can add a new element to the front of a list using the "cons" operator `::`:

```fsharp
1::[ 2; 3; 4 ]
```

This code produces a list from 1 to 4:

``` {highlight: [2]}
> 1::[ 2; 3; 4 ];;
val it : int list = [1; 2; 3; 4]
```

We can even use the `::` operator to create lists (although this is not done in practice):

```fsharp
1::2::3::4::[]
```

`[]` is an empty list which we than add `4` to, then `3` becomes the new first element, etc. until we end up with:

``` {highlight: [2]}
> 1::2::3::4::[];;
val it : int list = [1; 2; 3; 4]
```

Naturally, VS Code warns us that this is a weird thing to do:

> `x :: []` might be able to be refactored into `[x]`.

A natural question to ask at this point is how do we add an element to the end of a list? There is no equivalent operator for adding an item to the end of a list. This is for technical reasons which we will not go into here. However, there are a few options.

One way is to use the "list concatenation" operator `@`. This operator takes two lists and joins them together (concatenates them, just like `+` concatenates `string`s):

```fsharp
[ 1; 2; 3 ] @ [ 4; 5; 6 ]
```

This gives us a list of numbers from 1 to 6:

``` {highlight: [2]}
> [ 1; 2; 3 ] @ [ 4; 5; 6 ];;
val it : int list = [1; 2; 3; 4; 5; 6]
```

So to add a single element to the end of a list we can create a list with just that element in it and use `@`:

```fsharp
let addToEnd list item =
    list @ [ item ]
```

This function has type `'a list -> 'a -> 'a list` (with `'a` meaning that the compiler cannot work out the exact type, but the type of the elements in the list is the same as the type of the second parameter) and returns `list` with `item` added to the end. Even though F# does not have this function built in, it is extremely easy to write one ourselves if we need it.

As an aside, the `List` module also has a function called `List.append` which does exactly the same thing as `@`. Using `List.append` makes concatenating lists easier in a chain of list functions with `|>` - for example:

```fsharp
[ 1 .. 10 ]
|> List.map (fun x -> x + 1)
|> List.append [ 0; 1 ]
```

Another technique for adding an item to the end of a list is to reverse the list, add the item to the front of the list, and reverse the list again. This sounds convoluted but is sometimes the more natural way of adding an item to the end of a list (for example, if you had reversed the list already for some other reason). We can do this easily with `List.rev` - a function with type `'a list -> 'a list` which reverses a list:

```fsharp
let addToEnd2 list item =
    let reversed = List.rev list
    item::reversed
    |> List.rev
```

This function has the same type as `addToEnd`. Notice the use of `|>` to immediately pass the list with `item` added to the front into `List.rev`.

`List.rev` can be extremely useful in situations, for example, where it may be more natural to construct a list "backwards" and then reverse it afterwards.

## Removing Items From Lists

In the same way in which F# makes it easy to add an item to the front of a list, F# also makes it easy to remove the first item in a list. There are two functions we will look at here.

Firstly, `List.head` (which has type `'a list -> 'a`) takes a list and returns the first element - the "head" of the list. This is equivalent to `List.item 0` but more concise.

Secondly, `List.tail` (which has type `'a list -> 'a list`) takes a list and returns a new list containing all but the first element of the original list - i.e. removes the first element.

Combining `List.tail` and `List.rev`, we can easily write a function which removes the last element of a list:

```fsharp
let removeTail list =
    list
    |> List.rev
    |> List.tail
    |> List.rev
```

This function also has type `'a list -> 'a` and removes the last element of a list:

``` {highlight: [2]}
> removeTail [ 1 .. 5 ];;
val it : int list = [1; 2; 3; 4]
```

If we just wanted to know what the last item in a list was, we could use `List.last`.

## Obtaining Sub-lists

In the final part of this section, let's look at how we can get parts of a given list.

The first function which helps us do this is `List.take` which has type `int -> 'a list -> 'a list`. The first parameter of `List.take` is the number of elements we want to "take" from the _front_ of the list which we pass in as the second parameter. Here's an example:

```fsharp
List.take 3 [ 1 .. 5 ]
```

Which gives:

``` {highlight: [2]}
> List.take 3 [ 1 .. 5 ];;
val it : int list = [1; 2; 3]
```

The next function which we will look it is `List.skip`. `List.skip` is similar to `List.take` - even having the same type - but instead of taking elements from the front of a list, `List.skip` "skips" the first elements and returns the rest of the list. For example:

```fsharp
List.skip 3 [ 1 .. 5 ]
```

Which produces:

``` {highlight: [2]}
> List.skip 3 [1 .. 5 ];;
val it : int list = [4; 5]
```

With these two functions together, we can write a function which returns all the elements of a list between two indexes (inclusive):

```fsharp
let getSubList list first last =
    list
    |> List.skip first
    |> List.take (last - first + 1)
```

This function has type `'a list -> int -> int -> 'a list`. The `+ 1` is required so that our function returns the elements at index `first` and index `last`. Let's test our function:

``` {highlight: [2]}
> getSubList [ 'a' .. 'z' ] 2 3;;
val it : char list = ['c'; 'd']
```

The item at index `2` is `'c'` and the item at index `4` is `'d'`, so our function returns the correct result.

Using all the functions we've seen so far we can now manipulate lists however we wish, adding items, removing items and changing the values of items. See the exercises for details.

## Pattern Matching On Lists

When writing functions which accept lists as parameters, it's extremely important to know whether or not the list is empty. If the list is empty we generally want to do something different to if the list is non-empty. Thinking back to the chapter on conditional logic, this is exactly the sort of situation where pattern matching can help us out. So let's look at how we can use pattern matching with lists.

Let's write a function which prints some information about a given list:

```fsharp
let printInfo list =
    match list with
    | [] -> printfn "the list is empty"
    | head::tail ->
        printfn "The list is non-empty."
        printfn "The first item is %A" head
        printfn "Then the list has %i other items" <| List.length tail
```

There's quite a lot going here, so let's break it down.

Notice `printInfo` has type `'a list -> unit` as it takes a list and simply prints information about it.

When we pattern match on lists we need to check two main cases.

1. The list is empty (i.e. the `[]` case)
2. The list has items in it.

There are a few ways to match a non-empty list. We could have just written:

```fsharp
match list with
| [] -> printfn "The list is empty"
| x -> printfn "The list contains %i items" <| List.length x
```

In which case `x` is the same as `list`, when `list` is non-empty. Sometimes this is all we need.

However, we can also match `list` with `| head::tail`. This use of the cons operator (`::`) binds the first item in `list` to the value `head` and everything else to `tail`. We can do this because we know that `list` is non-empty (as we just checked that case on the previous line). If we did not do this, but still wanted the head and tail of `list` we would have had to do this instead:

```fsharp
...
| x when List.length x > 0 ->
    let head = List.head x
    let tail = List.tail x
...
```

Pattern matching allows us to use a much more concise syntax to immediately give us access to the head and tail of the list we are matching on.

Now that we have `head` and `tail` we can use them to print out some information about `list`. Notice the use of `%A` in the second print statement of the non-empty case. `%A` is a string formatting parameter which means "anything" and so will print out any F# type in a nicely formatted way. This means we do not need to know what the type of the elements in list `list` are.

In the final print statement we also see `<|` the "reverse pipe operator". This is the opposite of `|>` and here is just used so that we don't need to put brackets around `List.length tail` to indicate that we want to use the result of `List.length tail` in our print statement (where `%i` is). `<|` just passes whatever value is on the right of it into the function on the left of it.

Sometimes we want to get the head or tail of the list we are pattern matching on but we do not care what the value of the other is. In this case do can use the wildcard `_` to omit binding to a value:

```fsharp
let ignoreTail list =
    match list with
    | [] -> printfn "empty"
    | first::_ -> printfn "%A" first
```

There is nothing wrong with using something of the form `head::tail` in this case but you may well see this use of `_` when one of the values is not used. Note also that the names can be anything, but `head` and `tail` are commonly used.

Other patterns we can match on include:

```fsharp
let lotsOfMatches list =
    match list with
    | [] -> printfn "empty"
    | [ head ] -> printfn "one item - %A" head
    | first::[ second ] -> printfn "two items - %A and %A" first second
    | x when List.length x > 2 -> printfn "at least three items"
    | _ -> printfn "in this case, this will never be matched"
```

## Exercises

Complete the following:

1.  Write a function similar to `nthLetter` from earlier in the chapter but which takes a list as the first parameter, and an `int` as the second parameter and returns the "nth" item in the list. However, if "n" is too small return the first item and if n is too large return the last item.
2.  Write a function which takes a list and an `int` (called `n` for example) and returns the last `n` items in the list. This should behave exactly like `List.take` except taking from the end of the list rather than the start. The items should also be returned in the same order that they were in in the original list.
3.  If you used `List.rev` in the previous exercise, rewrite the function using `List.skip`. If you used `List.skip`, rewrite the function using `List.rev` instead.
4.  Write a function which takes an `int` (called `n` for example) and a list and returns a list with the item at index `n` removed. (Don't worry about checking if `n` is a valid index.)
5.  Write a function which takes three parameters - an index, a list and an item to add to the list - and returns a list with the item added after the element at the given index. (Don't worry about checking if the index is valid.)
6.  Write a function similar to the previous exercise but update the element at the given index with the given value. (Don't worry about checking if the index is valid.)

<note>

A lot of the time accessing elements directly by index isn't required, but sometimes we do need to use this technique. In these situations it matters a great deal if the index we are using is valid. Learning how to deal with these situations will come later, but as an additional exercise maybe you could think about what we might do?

</note>
