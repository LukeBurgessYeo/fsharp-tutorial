# Sets And Maps

## Sets

### What Are Sets?

"Sets" are simply unordered lists with no duplicate elements. This can sometimes be a useful way to store data, and is the recommended way if you require their to be no duplicates and you don't need your data to be ordered in any particular way. For example, the usernames of your websites' users may have to be unique, so having a `Set` of current usernames may be a good way of ensuring that no duplicate usernames are created.

### Creating Sets

Sets can be creating using `Set.empty` and items added to the set using `.Add()`:

```fsharp
let numbers = Set.empty.Add(2).Add(1).Add(3).Add(1)
```

Notice that `numbers` has type `Set<int>` - this is the same as `int Set` which we can also write, but the `<>` notation is more commonly used for sets and maps. Let's see what this outputs:

```
val numbers : Set<int> = set [1; 2; 3]
```

Notice that `numbers` only contains a single `1` even though we added `1` twice in the original definition. This is because sets do not allow duplicate elements, and so will not add an element if it is already in the set.

The other way to create sets is using the `Set.ofList` function from the `Set` module:

```fsharp
let anotherSet =
    [ 5; 5; 4; 4; 3; 3; 2; 2; 1; 1 ]
    |> Set.ofList
```

Which produces:

```
val anotherSet : Set<int> = set [1; 2; 3; 4; 5]
```

Again, notice that `anotherSet` only contains a single copy of each number in the original list.

### Working With Sets

F# has a module of functions for working with sets, much like the `List` module. We can do things like `Set.map` and `Set.filter` which work just like the `List` module equivalents. However, we can also do set operations just like we would with mathematical sets:

| Operation    | Function         | Description                                                                  |
| :----------- | :--------------- | :--------------------------------------------------------------------------- |
| Union        | `Set.union`      | Returns a set with all elements from both inputs sets                        |
| Intersection | `Set.intersect`  | Returns a set with all the elements which are in both input sets             |
| Difference   | `Set.difference` | Returns a set with the elements in the second set removed from the first set |

All these functions have type `Set<'a> -> Set<'a> -> Set<'a>` as we might expect. These functions also do not change the input sets and instead return new sets - as we have seen with other F# types.

We can turn sets into lists easily using `Set.toList`. There are other `Set` module functions which you may wish to explore for yourself.

Sets are not commonly used but can be useful in certain situations and are certainly worth knowing about.

## Maps

### What Are Maps?

"Maps" (sometimes called "hashmaps" or "dictionaries") are another data structure which we can use to store and organise data. Maps are a collection of "key-value pairs" where we use the "key" to efficiently look up the "value" (much like how we use the word to lookup the definition in a dictionary) - so maps are an explicit mapping from a key to a value.

Like sets, maps are unordered and cannot contain duplicate keys. Maps can contain duplicate values, but keys must be unique so we know which value to look up. It is often useful to store information in this way, for example if you are creating a contacts app the names of the users' contacts should be unique and each name should have a corresponding phone number.

### Creating Maps

Much like sets, we can create maps using `Map.empty` and `.Add()`:

```fsharp
let users = Map.empty.Add("Alice", 1234).Add("Bob", 4425)
```

Notice `users` has type `Map<string, int>` which tells us that the keys are `string`s and the values are `int`s. The above code outputs:

```
val users : Map<string,int> = map [("Alice", 1234); ("Bob", 4425)]
```

Notice how F# Interactive prints out `users` - as a list of tuples. This suggests that we can also use `Map.ofList` to create maps, just like we did with sets, but this time using a list of tuples to represent out key-value pairs:

```fsharp
let anotherMap =
    [ ("Alice", 1234); ("Bob", 4425); ("Bob", 3546) ]
    |> Map.ofList
```

Let's see what happens when we try and create a map with two `"Bob"` keys:

```
val anotherMap : Map<string,int> = map [("Alice", 1234); ("Bob", 3546)
```

Just as with sets, we do not get any duplicate entries. We only get the first `"Bob"` in the list. As with sets, we can create a list from a map using `Map.toList`.

### Working With Maps

As you might expect, there is also a `Map` modules with similar functions to the `List` and `Set` modules (like `Map.map`, `Map.filter`, `Map.fold`, etc.).

The map specific functions which are of most interest are:

| Function      | Description                                       |
| :------------ | :------------------------------------------------ |
| `Map.find`    | Gets an element from a map by key                 |
| `Map.tryFind` | Safer version of `Map.find` - returns an `option` |
| `Map.remove`  | Removes an element from a map by key              |

`Map.remove` in fact returns a new map with the element removed, see below:

```
> users |> Map.remove "Alice";;
val it : Map<string,int> = map [("Bob", 4425)]

> users;;
val it : Map<string,int> = map [("Alice", 1234); ("Bob", 4425)]
```

`users` remains unchanged and `Map.remove` in fact returned a different map.

Again, there are many more `Map` module functions which you may wish to explore. Maps are extremely useful for storing data in a way which allows for easy lookup.

## Exercises

Let's test your understanding of sets and maps:

1. Create a set with the names of five people using `Set.empty.Add` and again using `Set.ofList`.
2. Write a function which takes two word lists (i.e. `string list`s) and returns a list of only those words which are in both input lists.
3. Write a function which takes two word lists and returns a list of words which only appear in exactly one of the lists.
4. Create a `Map<string,string>` of countries and their capital cities using both `Map.empty.Add` and then again with `Map.ofList`.
5. Using the map from the previous exercise, write a function which takes the name of a country (as a `string`) and returns it's capital city (from the map you created). Return an "empty string" `""` if the country isn't in your map.
6. Write a function which takes a list of words and counts how many times each word appears in the list - i.e. returns a `Map<string,int>` with each word and the number of times it occurred.
7. If you didn't do this already, try and do the previous exercise with with only one pass over the input list (i.e. by only checking each element in the list once).
