# More Functions

## Boolean Operators

Before we look at some more aspects of functions, let's first quickly go over some other ways we can work with boolean values.

The two operators we are going to introduce are the "AND" operator `&&`, and the "OR" operator `||`.

Let's start with the AND operator. `&&` returns `true` if the value of the left hand side, and the value on the right have side are both `true`. For example, try pasting the following line into F# Interactive:
```fsharp
(10 > 5) && (10 < 20)
```
You should see the following output:
```
> (10 > 5) && (10 < 20);;
val it : bool = true
```
OR (`||`) behaves similarly except this time only one side needs to be `true`:
```fsharp
(10 > 5) || (10 < 0)
```
```
> (10 > 5) || (10 < 0);;
val it : bool = true
```
In both examples, the brackets were not required but are there for readability.

Just for fun, let's write a function which takes two integers and checks if both numbers are greater than 10, or if their sum is greater than 10.
```fsharp
let biggerThan10 a b = a > 10 && b > 10 || a + b > 10
```
Let's test it:
```
> let biggerThan10 a b = a > 10 && b > 10 || a + b > 10
- biggerThan10 1 2;;
val biggerThan10 : a:int -> b:int -> bool
val it : bool = false

> biggerThan10 12 13;;
val it : bool = true

> biggerThan10 5 6;;
val it : bool = true
```
Note the type is `int -> int -> bool` as we expected.

However, this function could be improved. Notice that we explicitly wrote (or "hard coded") the number `10` three times! It would be better to bind `10` to a value and refer to the value in case we wanted to change it later:
```fsharp
let biggerThan10 a b = 
    let num = 10
    a > num && b > num || a + b > num
```
But why stop there? We could go one step further and pass `num` in as a parameter:
```fsharp
let biggerThanNum num a b = a > num && b > num || a + b > num
```
Notice the type is `int -> int -> int -> bool` as we are now passing in three `int`s. The reason why `num` is listed first will become clear soon.

Now what if we did in fact want to use the `biggerThan10` function a few times? We don't want to have to pass 10 in as a parameter every time. We could define a value equal to 10 and refer to that every time we called `biggerThanNum`, but is there a better way?

## Currying And Partial Application

This is where the fact that functions are values becomes important. Because functions are values, there is no reason why we cannot return a function from a function just like we return `int`s and other types. So, let's define `biggerThan10` once again, but this time using our more general `biggerThanNum` function:
```fsharp
let biggerThan10 a b = biggerThanNum 10 a b
```
In fact, we don't even need to explicitly write the parameters:
```fsharp
let biggerThan10 = biggerThanNum 10
```
The type of `biggerThan10` is now `int -> int -> bool`, just like before. The compiler is smart enough to return a function with the remaining type after we passed in the first `int`. We can just as easily define other functions like `biggerThan25` without having to repeat the main logic of the general `biggerThanNum` function. We have abstracted the logic away into a more general function and "baked in" the exact values that we care about.

The process of returning a function from another function is known as "partial application" (i.e. not passing in all the parameters). This works, and returns a function, because in F# parameters are "curried" (i.e. passed in one at a time). Writing partially applied functions is an extremely powerful technique in F#.

The fact that parameters are curried is also the reason that function types in F# are represented as `type -> type -> ... -> type`. The arrows indicate that you could either pass in all the parameters and return the final type in the chain, or just pass in some of the parameters and get a function of the type of the rest of the chain. In our case we had a `int -> int -> int -> bool` and created a `int -> int -> bool` by only passing in the first parameter.

## Higher-Order Functions And Composition

So we can return functions from functions, but as functions are just values like everything else - can we pass functions as parameters to functions?

Yes we can, and functions which take other functions as parameters are known as "higher-order functions". Let's see an example:
```fsharp
let checkNums func x y = func x && func y || func (x + y)
```
This function has type `(int -> bool) -> int -> int -> bool`. The brackets around the first two types indicate that the compiler has worked out that our first parameter (`func`) is a function with type `int -> bool`.

Let's define a function to pass into `checkNums`:
```fsharp
let lessThan5 n = n < 5

let checkLess5 = checkNums lessThan5
```
Note that `checkLess5` has type `int -> int -> bool` as we have supplied the first function parameter and so we get a function back with the expected type.

It is actually possible to define our `biggerThanNum` function using `checkNums`:
```fsharp
let greaterThan b a = a > b

let biggerThanNumsAgain n = checkNums (greaterThan n)
```
The brackets around `greaterThan n` are required so that the compiler knows that `greaterThan n` is a single parameter.

The process of passing functions into other functions is called "function composition" and is an extremely important part of programming in F#.

Being able to work with functions in this way (passing functions as parameters and returning functions from functions) is why F# is known as a "functional" programming language. Functions in F# are no different from any other value like a string or an integer. Being comfortable working with functions is essential to becoming a confident F# programmer. 

## Exercises

We've tackled some tough concepts. Don't worry if you don't fully understand them just yet, and knowing the technical terms certainly isn't a necessary (but might be useful if you want to do some further reading). Try the following exercises to test your understanding:

1. Write a function called `between10and20` which checks if the product of two numbers is between 10 and 20. (e.g. 3 * 5 is 15 so the function should return `true`, but 5 * 10 is 50 so it should return `false`.)
2. Write another function - called `betweenAandB` - which is the same as `between10and20` but takes the upper and lower bound as parameters. Rewrite `between10and20` in terms of `betweenAandB` by passing in just the first two parameters.
3. Write another function - called `testNumbers` - which is the same as `betweenAandB` but takes a function as the first parameter and applies that function to the final two parameters, checking if the results are between the second two parameters.

<details>
  <summary>Click to show hint</summary>

`testNumbers` should have type `(int -> int -> bool) -> int -> int -> int -> int -> bool` and should start something like:
```fsharp
let testNumbers func upper lower a b =
```
</details>

4. Write another function - called `times` - which takes two integers and multiplies them together. Rewrite `betweenAandB` in terms of `times` and `testNumbers`.
5. Write a new function - called `outside50and100` - which takes two integers and checks if their sum is less than 50 OR greater than 100. Define this function only in terms of `testNumbers` and it's parameters.