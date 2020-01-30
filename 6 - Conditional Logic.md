# Conditional Logic

## What Is Conditional Logic?

So far in this guide, we have written several functions which return `true` or `false` depending on the input - but what if we wanted to run different code depending on whether a value is `true` or `false`? This is what is meant by "conditional logic" - the ability to choose what to do next based on the checking of some condition.

In F# we use a technique called "pattern matching" to achieve this. Pattern matching is extremely powerful and we will see many more examples of more complex pattern matching later in the guide, but for now let's look at how we can use pattern matching to choose what to do next after checking the value of a `bool`.

## Introducing Pattern Matching

Let's write a function which takes an int and writes a different message to the console depending on whether the input is greater than, or less than 100:

```fsharp
let isItSmall n =
    match n > 100 with
    | true -> printfn "It is big!"
    | false -> printfn "It is small."
```

This function has type `int -> unit` as `printfn` returns `unit`. Let's unpick the syntax. Firstly the match statement itself:

```fsharp
match something with
```

tells the compiler that you want to examine `something`. In our case, we pass in a `bool` (in the form of `n > 100`) so the compiler knows that you need to check both `true` and `false` cases. Try removing the `false` line:

```fsharp
let isItSmall n =
    match n > 100 with
    | true -> printfn "It is big!"
```

Notice how VS Code throws a warning (with a yellow squiggle under `n > 100`):

> "Incomplete pattern matches on this expression. For example, the value 'false' may indicate a case not covered by the pattern(s)."

This is because the compiler knows that you are checking a `bool` and so you need both a `true` and `false` case. The compiler does similar checks for other F# data types when they are passed into a match expression.

Next we add cases using the `|` syntax. Putting each case on a new line is not required but does make the code more readable:

```fsharp
match something with
| value1 -> doThing ()
| value2 -> doAnotherThing ()
| value3 -> ...
```

Immediately after the `|` we put the value we want to check for. In our case we are matching a `bool` so we need two cases, one for `true` and one for `false`. We then put a `->` after the value meaning "if we matched this value then..." followed by whatever code we want to run. In our case we want to print a message to the console, so we use `printfn` and the message we want to print.

The order in which we list our cases does matter in general, but in the case of a `bool` the order is irrelevant. This is because a `bool` is either `true` or `false` so there is no way in which the value we are checking can be both.

## Pattern Matching On Strings

Pattern matching can be used on any F# type, so let's have a go at pattern matching a `string` value:

```fsharp
let greetings language =
    match language with
    | "English" -> printfn "Hello!"
    | "Spanish" -> printfn "Hola!"
    | "French" -> printfn "Bonjour!"
```

This time greetings has type `string -> unit`. We do not tell the compiler what the data-type of language is, but the compiler knows it's a `string` because we have put `string`s in our match cases. If we had put another type, `int`s for example, in our match cases then the compiler would have deduced that the type of our function greetings is `int -> unit`.

Each case will be matched if the input string `language` is exactly the same as the string in the match case. E.g. the `"English"` case is matched if `language = "English"` evaluates to `true`.

Notice the yellow squiggle under language in the match statement. The warning message says:

```
Incomplete pattern matches on this expression. For example, the value '"a"' may indicate a case not covered by the pattern(s).
```

The compiler has noticed that we are matching on `string`s and that there is a value that isn't covered by our current cases, `"a"` being the example that it gives. We can't write out every possible string manually so F# gives us a way to deal with this situation by using a "wildcard" character (`_`) to match "anything else". Let's see how this is used:

```fsharp
let greetings language =
    match language with
    | "English" -> printfn "Hello!"
    | "Spanish" -> printfn "Hola!"
    | "French" -> printfn "Bonjour!"
    | _ -> printfn "Sorry, I don't know that language!"
```

Now the compiler warning is no longer there. It is important to put the "catch-all" case using `_` as the last line. See what happens if we put it in any other position:

```fsharp
let greetings language =
    match language with
    | "English" -> printfn "Hello!"
    | "Spanish" -> printfn "Hola!"
    | _ -> printfn "Sorry, I don't know that language!"
    | "French" -> printfn "Bonjour!"
```

Now `"French"` is underlined with the warning:

> "This rule will never be matched"

This is because F# tries each case from top to bottom in order. If a case matches everything then the compiler will warn us that every case after that will never be matched.

The fact that the compiler warns us when we are missing cases, or putting cases in the wrong order is what makes pattern matching such a useful technique. We don't need to run our code against multiple test inputs to check if we've made these simple mistakes as the compiler tells us straight away.

## Pattern Matching With Guards

Suppose we wanted to print a difference message depending on whether an integer was in a particular range. One way to do that, inspired by the first `match` statement we wrote this chapter, would be:

```fsharp
let printRange n =
    match n < 0 with
    | true -> printfn "negative"
    | false ->
        match n < 50 with
        | true -> printfn "positive, small"
        | false ->
            match n < 100 with
            | true -> printfn "positive, kinda small"
            | false -> printfn "positive, big!"
```

Of course, there is no reason why we cannot nest `match` statements like this as a `match` statement is just a code block. Note the indentation of each line. The cases corresponding to each `match` block are intended the same amount as the line with "`match`" on it.

This code is rather ugly and difficult to read though. Fortunately F# gives us an alternative syntax called a "guard":

```fsharp
let printRange n =
    match n with
    | a when a < 0 -> printfn "negative"
    | b when b < 50 -> printfn "positive, small"
    | c when c < 100 -> printfn "positive, kinda small"
    | d when d >= 100 -> printfn "positive, big!"
```

Immediately this is easier to read and understand. The `when` syntax is what is known as a "guard". A line is matched if the statement after `when` and before `->` returns `true`. At the start of the line we bind the value we are testing (`n`) to a new value and refer to this value in the guard clause.

For example `| a when a < 0` means "match anything which is less than 0". The name we use can be anything we like (we could have used `negativeNumber` instead of `a` for example) and does not need to be different from case to case. It is just a way of naming the value we want to check after `when`. We can also refer to this value after the `->` e.g. `| x when x > 10 -> x * 2` would return whatever `x` is multiplied by `2` if this case was matched.

Notice that we have covered every possible integer in our match expression. The first line matches on any number less than `0`. The second on any number from `0` to `49` - and we don't need to put `when b >= 0 && b < 50` because we can only match the second case if the first isn't matched, so we know our number is already `>= 0`. The third line matches from `50` to `99` and the final line anything greater than or equal to `100`. However, the compiler still throws the warning:

> "Incomplete pattern matches on this expression."

Why is this? We have covered every case, the compiler hasn't given us an example of an unmatched case. The reason is that the compiler cannot check our guards (the code in our `when` clauses) without running the code, so we could have missed a case. The way to deal with this is by replacing the final case with the wildcard `_`:

```fsharp
let printRange n =
    match n with
    | a when a < 0 -> printfn "negative"
    | b when b < 50 -> printfn "positive, small"
    | c when c < 100 -> printfn "positive, kinda small"
    | _ -> printfn "positive, big!"
```

Now we no longer get a warning. It is preferable not to use the wildcard wherever possible (when matching on `bool`s for example we can explicitly write both the `true` and `false` cases) but sadly sometimes we have no choice, or it is at least rather awkward to modify the code to eliminate the need for a wildcard without getting a warning.

## Matching Multiple Cases

The final feature of pattern matching we are going to look at for now is running the same code for multiple cases. Let's write a function which checks whether a letter is a vowel:

```fsharp
let isVowel letter =
    match letter with
    | 'a' | 'e' | 'i' | 'o' | 'u'
    | 'A' | 'E' | 'I' | 'O' | 'U' -> true
    | _ -> false
```

This function has type `char -> bool`. `char` is the type that represents a single "character" - i.e. a single letter. As with `string`s, `char`s are case sensitive, so in our `isVowel` function we have to check for all upper and lower-case vowels.

The syntax for checking multiple cases at once is simply to leave the `->` off and write the next case. Cases can be written inline, or on multiple lines. Here it makes the code more readable to put the lower-case values on one line and the upper-case values on the next line, so it is clear that we are checking all possible vowels.

Finally, having checked for all vowels, we use the wildcard `_` to match anything which isn't a vowel.

Being able to specify multiple cases to match on in this way means we can explicitly write every case we want to match without having to repeat the same code for multiple different cases. In general we don't want to repeat ourselves, this is known as the the "D.R.Y." (Don't Repeat Yourself) principle.

## Exercises

Let's use pattern matching to write some more complex functions then we have done previously.

1. Write a function which returns the string representation of a particular number between 0 and 9 (e.g. inputting `1` returns `"one"`). Return `"other number"` if the input is not a number from 0 to 9.
2. Using the modulus operator, write a function which returns the last digit of a number (e.g. inputting `54` returns `4`).
3. Using the function you just wrote, write another function which returns the number of tens in a two digit number (e.g. inputting `54` returns `50`).
4. Write a function which takes an int and returns the English name of the input when rounded down to the nearest 10. E.g. inputting `54` returns `"fifty"`. Return `"small number"` if the input is less than 10 and `"big number"` if the input is greater than 99.
5. Use the functions you've written to create a function which takes an `int` and returns the string representation of the number (for two digit numbers). Hence `54` should return `"fifty four"`. (HINT: You can join strings together using the "concatenation" operator `+`. Remember to put a space `" "` between the words - e.g. `"hello" + " " + "world"` gives `"hello world"`).
6. BONUS: Try to improve your code to work for numbers between 10 and 20, and to not return the `"zero"` when passing a number like `50`. Perhaps also return a more specific message if the number is less than 0 or greater than 99.
