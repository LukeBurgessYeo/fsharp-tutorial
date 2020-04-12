# String Manipulation

## What Are Strings?

So far in this guide we have done relatively little with strings. We have seen how to create strings using `sprintf` but what about manipulating existing strings?

Firstly, it is important to realise that a `string` is really just a list of `char`s. We can obtain that list using the `System.String.ToCharArray()` function on a `string`:

``` {highlight: [2]}
> "Hello".ToCharArray();;
val it : char [] = [|'H'; 'e'; 'l'; 'l'; 'o'|]
```

Again, this is an example of a C# focused function. In F# we'd rather pass a `string` into a function than use `.ToCharArray()` on a particular `string`, we'd also prefer a `List`. We should immediately be thinking of writing our own F# wrapper function like so:

```fsharp
let getChars (str : string) =
    str.ToCharArray()
    |> Array.toList
```

The type annotation is required because the compiler does no know that `str` is a `string`, and so does not know if we can use `.ToCharArray()`. 

<note>

We could use the built in function `string` of type `'a -> string` and use `(string str).ToCharArray()` instead of a type annotation. It's up to you.

</note>

Running our function in F# interactive gives the following:

``` {highlight: [7]}
> let getChars (str : string) =
-     str.ToCharArray()
-     |> Array.toList;;
val getChars : str:string -> char list

> getChars "Hello";;
val it : char list = ['H'; 'e'; 'l'; 'l'; 'o']
```

So now we have a `char list` we can use all our knowledge about the `List` type to manipulate the characters as we see fit. We can get back to a `string` by converting the `List` back into an `Array` and using `System.String.Concat` to get the final `string`:

```
> [ 'H'; 'e'; 'l'; 'l'; 'o' ] |> List.toArray |> System.String.Concat;;
val it : string = "Hello"
```

Which again we could wrap into a function to make it a little easier to use:

```fsharp
let charsToString (chars : char list) =
    chars
    |> List.toArray
    |> System.String.Concat
```

You might be thinking at this stage that this is all a bit convoluted, and you would be correct.

## The `String` module

For doing operations which essentially involve turning a `string` into a `char list`, manipulating the list and converting back into a `string`, F# has a module called `String` which contains several functions which do all that hard work for us.

The `String` module contains functions such as `String.map` and `String.filter` which behave exactly as you would expect. For example, we could write a function which removes all the vowels from a string:

```fsharp
let notVowel c =
    match c with
    | 'a' | 'e' | 'i' | 'o' | 'u'
    | 'A' | 'E' | 'I' | 'O' | 'U' -> false
    | _ -> true

let removeVowels = String.filter notVowel
```

We define two functions - firstly, `notVowel` which has type `char -> bool` and returns `true` if the `char` is not a vowel. Secondly, `removeVowels` which has type `string -> string` and is the result of partially applying `String.filter` with `notVowel`. Let's test it on a string:

``` {highlight: [2]}
> removeVowels "Hello World!";;
val it : string = "Hll Wrld!"
```

The full documentation on the `String` module can be found [here](https://msdn.microsoft.com/visualfsharpdocs/conceptual/core.string-module-%5bfsharp%5d).

## More `System.String` Functions

In many cases, the F# `String` module isn't actually enough for our needs. Let's look at a few common examples.

Splitting a string into multiple stings using `.Split`. For example splitting a string into separate words:

```fsharp {highlight: [2]}
let getWords (str : string) =
    str.Split " "
    |> Array.toList
```

``` {highlight: [2]}
> getWords "Hello World!";;
val it : string list = ["Hello"; "World!"]
```

Getting a substring using `.Substring`:

```fsharp {highlight: [2]}
let getSubString start length (str : string) =
    str.Substring(start, length)
```

``` {highlight: [2]}
> "Hello World!" |> getSubString 2 5;;
val it : string = "llo W"
```

Testing if a string starts with a given substring using `.StartsWith`:

```fsharp {highlight: [2]}
let startsWith (test : string) (str : string) =
    str.StartsWith test
```

``` {highlight: [2]}
> "Hello World!" |> startsWith "H";;
val it : bool = true
```

There is also a `.EndsWith` which behaves similarly for the end of a string.

We can replace a substring with another substring using `.Replace`:

```fsharp {highlight: [2]}
let replaceWith (oldStr : string) (newStr : string) (str : string) =
    str.Replace(oldStr, newStr)
```

``` {highlight: [2]}
> "Hello World!" |> replaceWith "l" "_";;
val it : string = "He__o Wor_d!"
```

Other useful functions include `.Trim` to replace white space from the start or end of a string, `.Contains` to test is a string contains a substring, `.ToLower` and `.ToUpper` to change a string to be upper or lower case.

Occasionally even all these functions are still not enough and we need to turn to "[regular expressions](https://en.wikipedia.org/wiki/Regular_expression)". However, there are plenty of good regular expression [tutorials](https://medium.com/factory-mind/regex-tutorial-a-simple-cheatsheet-by-examples-649dc1c3f285) online so we will not go into them here.

## Exercises

Let's test your ability to manipulate strings in F#:

1. Write a function which reverses a string, i.e. inputting `"hello"` returns `"olleh"`.
2. Write a function which reverses the words in a string, i.e. inputting `"Hello World!"` returns `"World! Hello"`.
3. Write a function which checks if a string is a "palindrome". i.e. inputting `"eye"` should return `true`.

    <note>

    A _palindrome_ is a string which is the same backwards as forwards when ignoring case, punctuation and spaces. You will need to filter out all non-alphanumeric characters and convert the string into either all upper or all lower case.

    </note>

4. Write a function which returns the longest word in a string, i.e. inputting `"one, two, three"` returns `"three"`.
5. Write a function which takes an integer `n` and a string `str` and replicates the string `n` times with a space `" "` in between each `str`. Inputting `2 "hello"` should return `"hello hello"`.
6. Write a function which tests if a string contains a numeric character, i.e. inputting `"hell0"` returns `true`.
7. BONUS: Do the previous exercise using regular expressions. 