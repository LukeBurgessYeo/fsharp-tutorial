# Records

# What Are Records?

Suppose you wanted to store many different data points together in one place. Perhaps you are building (yet another) dating app and you want to be able to store all the information in a users profile all in one, convenient, easy to use data type. You could try using a tuple for this...

```fsharp
type Gender =
    | Male
    | Female
    | Other of string

let someUser = ("John Smith", 26, Male, "software engineer", Female, "I suck at writing bios.", true)
```

Notice that `someUser` has type `string * int * Gender * string * Gender * string * bool`. Wow, what a mess. How are we supposed to know what each of the `string`s actually represent? What does the `bool` at the end represent? What happens if we want to add a new feature to our app where we have to store even more information about each user? Every time we want to write a function with users we will have to look up what the tuple is supposed to be and hope we don't make any mistakes. Surely there is a better way...

This is where "records" come in. Records are a way of describing complex data (such as the user of a dating app) when the F# type system doesn't provide enough nuance out of the box. Record types are a kind of blueprint for what a value should look like. Here's how to create a record type:

```fsharp
type User = {
    Name : string
    Age : int
    Gender : Gender
    JobTitle : string
    InterestedIn : Gender
    Bio : string
    Verified : bool
}
```

We can define record types using the `type` keyword and whatever we want to call the type, in this case `User`. Then we put a list of "fields" (sometimes called "members") together with their types between curly brackets `{` `}`. The name of each field and it's type are separated by a colon `:` - this is how the compiler knows the difference between the field `Gender` and the type `Gender`, for example. Fields can be any F# type, including other records.

We can create values with this type by simply giving each field a value (using `let` for the overall value and `=` for each field):

```fsharp
let john = {
    Name = "John Smith"
    Age = 26
    Gender = Male
    JobTitle = "software engineer"
    InterestedIn = Female
    Bio = "I suck at writing bios."
    Verified = true
}
```

Notice that the value `john` has type `User`. Notice also that it is clear just from looking at this code what each `string`, `int`, etc. represent. We were able to guess what most of them meant previously but now the definition of the value `john` tells us explicitly what each piece of data means (including the mysterious `bool` at the end). We can declare the fields in any order we like, it doesn't have to be the same order as the type definition.

# Working With Records

The first thing we may want to do with records is get the value of a particular field. This can be done very easily using "dot" `.` syntax and the name of the field we want to access:

```fsharp
let johnsAge = john.Age
```

`johnsAge` has type `int` as we might expect. We can also pass records into functions:

```fsharp
let getAge user =
    user.Age
```

`getAge` has type `User -> int`. Writing small functions like this, to get fields which are frequently accessed, can be very useful.

We often want to get multiple fields from a record and assign them to values, this can be done in one step using "record deconstruction":

```fsharp
let { Age = johnsAge; Name = johnsName } = john
printfn "%s is %i years old" johnsName johnsAge
```

The syntax is slightly obscure, but here we are creating two values - `johnsAge` and `johnsName` inside what looks like a record (notice we can also write records on on line separating each field with a semicolon `;`), and using `=` to pull the fields from `john`. Running the above code outputs:

```
> let { Age = johnsAge; Name = johnsName } = john;;
val johnsName : string = "John Smith"
val johnsAge : int = 26

> printfn "%s is %i years old" johnsName johnsAge;;
John Smith is 26 years old
val it : unit = ()
```

As wel can see, `johnsName` is `"John"` and `johnsAge` is `26`, and we can use those values however we wish - in a `printfn` statement in this case. This technique is rarely used as it isn't hard to just access each field using dot notation, but it can sometimes make code more concise if several different values need to be created from a record.

What if we wanted to update a record? As always, we cannot change an existing record but we can create a new record value with the updates we want. This is called "record copying" and F# gives us a shorthand syntax to allow us to do it easy:

```fsharp
let updatedJohn =
    { john with
        Age = john.Age + 1
        Bio = "I still suck at writing bios." }
```

The above code creates a new record and assigns it to the value updatedJohn. This record contains the same fields as `john` but with `Age` now set to whatever the previous age was `+ 1`, as well as a different string for the `Bio`.

Equality between two records works just as we would expect. If the fields in each of the two records have the same values then the two records are equal:

```fsharp
john = { john with Name = "John Smith"; Age = 26 }
john = updatedJohn
```

Running this code gives:

```
> john = { john with Name = "John Smith"; Age = 26 };;
val it : bool = true

> john = updatedJohn;;
val it : bool = false
```

Even though we copied `john` to a new value in the first example, the fields contained the same data and so the two records are equal. In the second example, `updatedJohn` is not equal to `john` as the data is different.

# Using Records In Functions

As we saw earlier, we can pass records into functions like any other type. We also saw that the compiler can infer the type of the parameter based on which fields we access with `.` syntax. However, what if we have two different types which both have a field with the same name?

Suppose our app allows users to choose which city they are in when they search for matches. We could create a `City` type like so:

```fsharp
type City = { Name : string; Users : int }
```

As we don't like coming up with other names for things, we use `Name` again, but this time to refer to the name of a city. Notice also the inline syntax where each field definition is separated by a semicolon `;`. For shorter records this can often be easier to read.

Now let's write a function which returns the name of something:

```fsharp
let getName thing =
    thing.Name
```

`getName` has type `City -> string` if you defined `City` after `User`, or type `User -> string` if you defined `User` last. This is slightly annoying. We don't want to have to keep track of which order we need to define types in in order for our functions to work as expected. Fortunately there is a way for us to explicitly specify the types of our parameters - using "type annotations". In this case, let's force the type of our parameter to be `User`:

```fsharp
let getName (thing : User) =
    thing.Name
```

Putting the parameter in brackets with `: User` after the name of the parameter forces the parameter to have that type. Now `getName` has type `User -> string`. In general, it is good practice to leave off type annotations unless absolutely necessary. The compiler should be able to infer the types in well written F# code, relying on type annotations is often a sign that you are not writing idiomatic F#. Sometimes, however, you do need to specify the types - and this is how you do it.

We can also specify the return type of a function by putting `: Type` after all the parameters, but this should never be necessary:

```fsharp
let getName (thing : User) : string =
    thing.Name
```

It can sometimes be helpful to use type annotations like this when first writing a function, but try and remove them once you've finished writing the function and adjust your code if the compiler still cannot infer the type.

# Pattern Matching Records

As with any F# type, we can pattern match with records. Here is an example:

```fsharp
let printInfo user =
    match user with
    | y when y.Age < 18 -> printfn "User is too young."
    | { Name = name; Verified = b } when not b -> printfn "%s is not verified." name
    | x -> printfn "%A" x
```

`printInfo` has type `User -> unit`. We can match on a record, binding the value and then using a guard to check particular fields like we do here with `y when y.Age < 18`. We can also use record deconstruction to bind particular fields to values, as we do with `{ Name = name; Verified = b } when not b`. We can also print out an entire record (nicely formatted) using `%A` as we do here in the final case.

# Exercises

Let's test your understand of records. It wouldn't be a proper programming tutorial if we didn't make an animal type, so let's do that!

1. Create a record type called `Animal` with three fields: `Name` which is a `string`, `Age` which is an `int` and `Species` which is a `string`.
2. Write a function which takes an `Animal` and prints out the sound it makes - i.e. if the `Species` is `"dog"` then print `"woof!"`, similarly a cat should `"meow"` and maybe add one more case of your own. If an any other species of animal is passed in, print out `"I don't know what sound that animal makes."`. Create several `Animal` values to test all the different possible outputs of your function.
3. Write a function which updates the `Name` of an `Animal` - i.e. accepts an `Animal` and a `string` and returns a new `Animal` with the `Name` field updated to the given `string`.
4. Create a `Dog` type with a `Colour` field of type `string` and a `FavouriteToy` field also of type `string`. Create another type called `Cat` also with two `string` fields, one called `Colour` and the other `FavouriteFood`. Finally, create a discriminated union type called `Species` with three options: `Dog` of type `Dog`, `Cat` of type `Cat` and `Other` (with no type).
5. Update your `Animal` type so that `Species` is of type `Species` (the discriminated union you just created in the previous exercise). Write a function which takes this updated `Animal` type and prints out the name, colour and favourite toy if the animal is a `Dog`, the name, colour and favourite food if the animal is a `Cat` and simply prints the whole record for any other animal. Create several new `Animal` values to test all the different possible outputs of your function.
