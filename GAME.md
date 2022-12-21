
# [Programming a Guessing Game](https://doc.rust-lang.org/book/ch02-00-guessing-game-tutorial.html#printing-values-with-println-placeholders)

This project was a hands-on way to introduce you to many new Rust concepts: let, match, functions, the use of external crates, and more.

To obtain user input and then print the result as output, we need to bring the `io` input/output library into scope. The `io` library comes from the standard library, known as `std:`

```
use std::io;
```

The `fn` syntax declares a new function; the parentheses, `()`, indicate there are no parameters; and the curly bracket, `{`, starts the body of the function.
`println!` is a macro that prints a string to the screen.
```
fn main() {
```

## Storing Values with Variables
In Rust, variables are `immutable` by default, meaning once we give the variable a value, the value won’t change. 
To make a variable `mutable`, we add `mut` before the variable name.let `mut guess` will introduce a mutable variable named guess. The equal sign `(=)` tells Rust we want to bind something to the variable now. On the right of the equal sign is the value that guess is bound to, which is the result of calling `String::new`, a function that returns a new instance of a String. `String` is a string type provided by the standard library that is a growable, UTF-8 encoded bit of text.
The `::` syntax in the `::new` line indicates that `new` is an associated function of the String type. An associated function is a function that’s implemented on a type, in this case String. This new function creates a new, empty string. You’ll find a new function on many types because it’s a common name for a function that makes a new value of some kind.
In full, the `let mut guess = String::new();` line has created a mutable variable that is currently bound to a new, empty instance of a String.
```
let mut guess = String::new();
```

If we hadn’t imported the io library with use `std::io;` at the beginning of the program, we could still use the function by writing this function call as `std::io::stdin`. The `stdin` function returns an instance of `std::io::Stdin`, which is a type that represents a handle to the standard input for your terminal.
Next, the line `.read_line(&mut guess)` calls the `read_line` method on the standard input handle to get input from the user. We’re also passing `&mut guess `as the argument to `read_line` to tell it what string to store the user input in. The full job of `read_line` is to take whatever the user types into standard input and append that into a string (without overwriting its contents), so we therefore pass that string as an argument. The string argument needs to be mutable so the method can change the string’s content.

The `&` indicates that this argument is a reference, which gives you a way to let multiple parts of your code access one piece of data without needing to copy that data into memory multiple times. References are a complex feature, and one of Rust’s major advantages is how safe and easy it is to use references. Hence, you need to write `&mut guess` rather than `&guess` to make it mutable.

## Receiving User Input && Handling Potential Failure with Result
```
io::stdin()
    .read_line(&mut guess)
    .expect("Failed to read line");
```
If the `read_line` method returns an `Err`, it would likely be the result of an error coming from the underlying operating system. If this instance of Result is an `Ok` value, expect will take the return value that `Ok` is holding and return just that value to you so you can use it. In this case, that value is the number of bytes in the user’s input.
If you don’t call expect, the program will compile, but you’ll get a **warning.** Rust warns that you haven’t used the Result value returned from `read_line`, indicating that the program hasn’t handled a possible error.


## Printing Values with println! Placeholders
The `{}` set of curly brackets is a placeholder: think of {} as little crab pincers that hold a value in place. When printing the value of a variable, the variable name can go inside the curly brackets. When printing the result of evaluating an expression, place empty curly brackets in the format string, then follow the format string with a comma-separated list of expressions to print in each empty curly bracket placeholder in the same order. Printing a variable and the result of an expression in one call to println! would look like this.
```
println!("You guessed: {guess}");
```


## Generating a Random Number
Then we call the `gen_range` method on the random number generator. This method is defined by the `Rng` trait that we brought into scope with the use `rand::Rng;` statement. The `gen_range` method takes a range expression as an argument and generates a random number in the range. The kind of range expression we’re using here takes the form `start..=end` and is inclusive on the lower and upper bounds, so we need to specify `1..=100` to request a number between 1 and 100.
```
let secret_number = rand::thread_rng().gen_range(1..=100);
```

## Comparing the Guess to the Secret Number
First we add another use statement, bringing a type called `std::cmp::Ordering` into scope from the standard library. The `Ordering` type is another enum and has the variants `Less`, `Greater`, and `Equal`. These are the three outcomes that are possible when you compare two values.

The `cmp` method compares two values and can be called on anything that can be compared. It takes a reference to whatever you want to compare with: here it’s comparing `guess` to `secret_number`. Then it returns a variant of the `Ordering enum` we brought into scope with the use statement. We use a match expression to decide what to do next based on which variant of `Ordering` was returned from the call to `cmp` with the values in `guess` and `secret_number`.

A `match` expression is made up of arms. An arm consists of a pattern to match against, and the code that should be run if the value given to match fits that arm’s pattern. Rust takes the value given to match and looks through each arm’s pattern in turn. Patterns and the match construct are powerful Rust features: they let you express a variety of situations your code might encounter and they make sure you handle them all.
```
match guess.cmp(&secret_number) {
    Ordering::Less => println!("Too small!"),
    Ordering::Greater => println!("Too big!"),
    Ordering::Equal => {
        println!("You win!");
        break;
    }
}
```
The `trim` method on a String instance will eliminate any whitespace at the beginning and end, which we must do to be able to compare the string to the `u32`, which can only contain numerical data. The user must press enter to satisfy `read_line` and input their guess, which adds a newline character to the string.

The `parse` method on strings converts a string to another type. Here, we use it to convert from a string to a number. We need to tell Rust the exact number type we want by using let `guess: u32`. The colon `(:)` after guess tells Rust we’ll annotate the variable’s type. Rust has a few built-in number types; the u32 seen here is an unsigned, 32-bit integer.
The `parse` method will only work on characters that can logically be converted into numbers and so can easily cause errors.
```
let guess: u32 = match guess.trim().parse() {
    Ok(num) => num,
    Err(_) => continue,
};
```

## Allowing Multiple Guesses with Looping

The `loop` keyword creates an infinite loop.

```
loop {
    println!("Please input your guess.");
    let mut guess = String::new();
    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");
    let guess: u32 = match guess.trim().parse() {
        Ok(num) => num,
        Err(_) => continue,
    };
    println!("You guessed: {guess}");

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => {
            println!("You win!");
            break;
        }
    }
}
```