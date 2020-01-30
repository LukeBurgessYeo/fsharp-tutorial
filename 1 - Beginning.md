# Setting Up Your Environment

## Tools

We will need to download and install some software in order to write, compile and run our F# code. The tools are:

- [VS Code](https://code.visualstudio.com/Download) - the best text editor for writing F# code in
- [Visual Studio Build Tools 2019](https://visualstudio.microsoft.com/downloads/#build-tools-for-visual-studio-2019) - which allows VS Code to understand F#
- [Dotnet Core](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install) - which allows us to build and run F# code

Once VS Code is installed we will also need the [Ionide](http://ionide.io/) extension which can be installed from the "Extensions" tab on the left-hand side of the VS Code window. Search for "Ionide-fsharp" in the search bar and click "install".

Ionide turns VS Code from a simple text editor into a fully featured F# coding environment. It includes everything we need to make writing F# code quick and easy, as well as building and running F# code, all from inside VS Code itself.

## Writing Your First Program

It is traditional for the first program you ever write in a given programming language to be a ["Hello World!" Program](https://en.wikipedia.org/wiki/%22Hello,_World!%22_program). So let's start there.

Firstly, create a folder somewhere (perhaps inside your `Documents` folder) called something like `FSharp`. Then open this folder in VS Code by selected `File -> Open Folder...` from the VS Code toolbar and selecting your folder.

Next, create a file by selecting the "Explorer" tab at the top of the left-hand side menu and clicking the "New File" button in the pane which opens up, or by right-clicking inside the pane and selecting "New File". Name this file `Script.fsx`. The `.fsx` file extension is important so that VS Code knows you are trying to create an F# script file.

Select `Script.fsx` to open it in the main window and paste the following code into it:

```fsharp
printfn "Hello World!"
```

This is the most simple "Hello World!" program in F#. To run the code, highlight it and press `ALT+ENTER` to send the code to "F# Interactive". This will open a terminal at the bottom of the window with "F# Interactive" running. The code will then, hopefully, execute and you should see the following output:

```
> printfn "Hello World!";;
Hello World!
val it : unit = ()
```

As you can see, the message "Hello World!" was printed to the console and we have successfully executed our first F# program. The line before that, starting with a `>`, shows the code that was executed. Don't worry about the last line, we will cover what that all means later.

## Troubleshooting

If this did not happen then there are several things which may have gone wrong. It is almost certainly the case that one of the components listed above was not installed properly, or perhaps does not work with the operating system which you are running.

Try searching online for any of the error messages that you see and work through the suggestions that you find. Make sure to also read the official installation guides for the tools themselves. Setting up your development environment can often be much more difficult and time consuming than programming itself, but this is just how technology works.

## Cloud Environment

As an alternative to setting up your local environment, you can use online F# editors. For script files you can use [Katacode](https://www.katacoda.com/courses/fsharp/playground). Just make sure to type `cd /home/scrapbook/tutoral` in the terminal before trying `fsharpi app.fsx`. Alternatively you can just type `fsharpi` in the terminal and then copy+paste your code (using the mouse) into the terminal.

There is also [Repl.it](https://repl.it/languages/fsharp). However this environment compiles your code as an application so you will not get the F# Interactive outputs, or be able to run code interactively.
