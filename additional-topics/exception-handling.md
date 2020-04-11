# Exception Handling and I/O

## Input/Output

Input/Output, or I/O for short, is the general term used for actions such as interacting with a file system. As we discussed right at the start of this guide, programming is all about data manipulation. So far we've been creating our own data by creating lists, etc. however when we are writing programs to solve real-world problems we generally have a data source to get our data from.

This data source might be from the web, a database, a local file, etc. The way in which we access each of these data sources is quite different but the general pricinples are the same. For the purposes of this chapter we will look at how to read from and write to local files, as well as the problems that can occur when doing such operations.

## Reading From A File

Let's start by trying to read some data from a file. In order to do this we will need to use functions from modules in the `System.IO` "namespace". Namespaces are simply collections of modules. In order to access modules from `System.IO` we need to "open" the namespace. We can do this by adding the following to the top of our script file:

```fsharp
open System.IO
```

and then sending that line to F# interactive. Now we can access the `File` and `Path` modules which contain the functions we need to read data from files on our file system.

Let's create a file to read from. In the same directory as your script file, create a plain text file called `data.txt`. This can be easily done from within VS Code by clicking the "New File" icon in the explorer tab. Add the following line to `data.txt`:

```
Hello World!
```

Now back in our script file, add the following lines:

```fsharp
let file = Path.Combine(__SOURCE_DIRECTORY__, "data.txt")
File.ReadAllLines(file)
```

Sending these lines to F# interactive should give the following:

``` {highlight: [5]}
> let file = Path.Combine(__SOURCE_DIRECTORY__, "data.txt");;
val file : string = "/path/to/your/file/data.txt"

> File.ReadAllLines(file);;
val it : string [] = [|"Hello World!"|]
```

Of course, your `file` will be different.

Let's break this code down. Firstly, we are creating a value for the location of our `data.txt` file. F# gives us a shorthand for this in the form of `__SOURCE_DIRECTORY__` which, when run in F# interactive, gives us the string form of the path to our current file. To get the file itself we use `Path.Combine` to combine the path to our current directory with the name of the file we want to access.

Next we use the `File.ReadAllLines` function to read from the file itself. Notice the type of this function: `string -> string array`. We can also write `string array` as `string []`, or even `Array<string>`. `Array` is a type which is similar to `List` but with some subtle differences (for example, arrays use `[|` `|]` instead of `[` `]`). In F# we generally prefer to work with the `List` type. We can easily get a `List` from an `Array` by using the `Array` module function `Array.toList`. Let's write a more idiomatic F# function to read from a file in the same directory as our script:

```fsharp
let readFile fileName =
    Path.Combine(__SOURCE_DIRECTORY__, fileName)
    |> File.ReadAllLines
    |> Array.toList
```

That's better, this function has type `string -> string list` and returns a list containing each line of the input file. Let's try adding another line to our `data.txt` file and calling our `readFile` function:

``` {highlight: [8]}
> let readFile fileName =
-     Path.Combine(__SOURCE_DIRECTORY__, fileName)
-     |> File.ReadAllLines
-     |> Array.toList;;
val readFile : fileName:string -> string list

> readFile "data.txt";; 
val it : string list = ["Hello World!"; "This came from a .txt file."]
```

This is all well and good, but what happens if the file doesn't exist? Try calling `readFile` with a different file name. You should see something like `System.IO.FileNotFoundException: Could not find file ...`. What's going on here?

## Exceptions And Exception Handling

Of course, there's no way F# can read from a file that doesn't exist. Rather than doing nothing, F# "throws an exception". Believe it or not, exceptions are your friend. Exceptions occur when something goes wrong and they allow the programmer to handle them however they see fit rather than assuming what the programmer wants to do.

For example, if we were writing a program which relied upon the existance of a particular file and that file didn't exist, we would probably want to tell the user that the file could not be found rather than just doing nothing and leaving the user confused about whether or not the program is working. If F# just did nothing if the file wasn't found we would have no way of notifying the user that something went wrong.

Of course, whenever possible, we should be writing code that doesn't throw exceptions but in cases like reading from files we have no choice but to accept that something might go wrong and so we must handle those cases, let's look at how to do that now.

### `try .. with`

In F# whenever we are doing a "risky" operation that might throw an exception - like reading from a file - it is good practice to wrap that code in a `try .. with` block. We put the risky part after the `try` and handle anything that goes wrong using `with`. Let's write a funtion which prints all the lines of a file:

```fsharp {highlight: [2, 5]}
let printAllLines fileName =
    try
        readFile fileName
        |> List.iter (printfn "%s")
    with ex ->
        printfn "Something went wrong! %s" ex.Message
```

We know that our `readFile` function can throw an exception so putting it inside a `try` block means that if an exception is thrown, it will be "caught" by the `with` block. We can in fact pattern match on the exception which is thrown, but in this case we simply bind the exception to the value `ex`, which has type `exn`, and use it to print out a message. All exceptions come with a message telling us what went wrong, we can access this message by using `ex.Message`. Let's test our function:

``` {highlight: ['2-3', 7]}
> printAllLines "data.txt";;
Hello World!
This came from a .txt file.
val it : unit = ()

> printAllLines "other.txt";;
Something went wrong! Could not find file '/.../other.txt'.
val it : unit = ()
```

This time we don't see the exception in the output when we try and read a file that doesn't exist. Instead, a nicely printed message tells us that the file could not be found.

In general it is good practice to put code which could thrown an exception inside a `try .. with` block to handle the exception.

## Creating And Writing To Files

### Creating Files

We've read from a file so now let's see how we can create a new file and write to it. Similarly to how we read from a file we need to get the path to our new file and then create it:

```fsharp
let newFile = Path.Combine(__SOURCE_DIRECTORY__, "myNewFile.txt")
File.Create(newFile) |> ignore
```

Here we use `|> ignore` because `File.Create` actually returns a `FileStream`. `FileStream`s can be used to read from and write to files but we will not be covering that here, so we just pipe it into `ignore` to give us a `unit`.

As before, we can easily turn this into a more generic F# function:

```fsharp
let createFile fileName =
    Path.Combine(__SOURCE_DIRECTORY__, fileName)
    |> File.Create 
    |> ignore
```

Notice that `createFile` has type `string -> unit` because we use `ignore` at the end of the function. Use `createFile` to create the `other.txt` file that we've been missing so far.

Notice also that this is the second time this chapter where we've used `Path.Combine` together with `__SOURCE_DIRECTORY__`. In the spirit of not repeating ourselves, let's pull this out into it's own function:

```fsharp
let getPath fileName = Path.Combine(__SOURCE_DIRECTORY__, fileName)
```

We can now use this to make our `readFile` and `createFile` functions a little nicer:

```fsharp
let readFile fileName =
    fileName
    |> getPath
    |> File.ReadAllLines
    |> Array.toList

let createFile fileName =
    fileName
    |> getPath
    |> File.Create 
    |> ignore
```

Or we could even drop the `fileName` parameters:

```fsharp
let readFile =
    getPath
    >> File.ReadAllLines
    >> Array.toList

let createFile =
    getPath
    >> File.Create 
    >> ignore
```

making use of the `>>` function composition operator. This is a purely cosmetic change but a good example of the "point-free" programming style, where we don't name the parameters of our functions. You will have to judge for yourself which style you prefer, but both styles are commonly used.

### Writing To Files

Writing to files is again very similar to our previous operations, however we have a chocie to make. We can either write over the current contents of a file using `File.WriteAllLines`, or we could add to the end of a file using `File.AppendAllLines`:

```fsharp {highlight: [4, 9]}
let writeFile fileName contents =
    let path = getPath fileName
    let lines = contents |> List.toArray
    File.WriteAllLines(path, lines)

let appendToFile fileName contents =
    let path = getPath fileName
    let lines = contents |> List.toArray
    File.AppendAllLines(path, lines)
```

Both functions have type `string -> string list -> unit`. Notice, however, that we need to convert `contents` from `List` to `Array` using `List.toArray` because `File` module functions use the `Array` type, and in F# we prefer to use the `List` type.