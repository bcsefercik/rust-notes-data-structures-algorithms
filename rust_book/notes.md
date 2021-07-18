# The Rust Programming Language
## https://doc.rust-lang.org/book/title-page.html

## Contents

- [CH 1: Intro](#ch-1-intro)
- [CH 2: Guessing Game](#ch-2-guessing-game)
- [CH 3: Common Concepts](#ch-3-common-concepts)
- [CH 4: Ownership](#ch-4-ownership)

## CH 1: Intro
**Cargo**: Included dependency manager and build tool, makes adding, compiling, and managing dependencies painless and consistent across the Rust ecosystem.  
**rustfmt**: A tool for formatting Rust code according to style guidelines. https://github.com/rust-lang/rustfmt  
**rustup**: a command line tool for managing Rust versions and associated tools  

Install: `curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh`  
To check installation: `rustc --version`

Install *Rust Enhanced* for syntax highlighting in Sublime Text.

To run: `rustc main.rs && ./main`

- Using a `!` means calling a macro to be eloborate in CH 19.

### Cargo
 
Cargo is Rust’s build system and package manager.
```
 cargo new <project_name>
``` 

- It has also initialized a new Git repository along with a `.gitignore` file. Git files won’t be generated if you run cargo new within an existing Git repository; you can override this behavior by using `cargo new --vcs=git`

- TOML = Tom’s Obvious, Minimal Language
- Cargo expects your source files to live inside the src directory. The top-level project directory is just for README files, license information, configuration files, and anything else not related to your code. Using Cargo helps you organize your projects. There’s a place for everything, and everything is in its place.

```
cargo build  # in root
```

We can also use `cargo run` to compile the code and then run the resulting executable all in one command.

- `cargo check` quickly checks your code to make sure it compiles but doesn’t produce an executable

- We can use `cargo build --release` to compile it with optimizations. This command will create an executable in _target/release_ instead of _target/debug_.


## CH 2: Guessing Game
**Topics**: let, match, methods, associated functions, using external crates

```
let immutable_var = 13;
let mut mutable_var = 13;
```

The `::` syntax in the `::new` line indicates that new is an associated function of the `String` type. An associated function is implemented on a type, in this case `String`, rather than on a particular instance of a `String`. Some languages call this a static method.

If we hadn’t put the use `std::io` line at the beginning of the program, we could have written this function call as `std::io::stdin`. The stdin function returns an instance of `std::io::Stdin`, which is a type that represents a handle to the standard input for your terminal.

The job of `read_line` is to take whatever the user types into standard input and append that into a string (without overwriting its contents), so it takes that string as an argument. The string argument needs to be mutable so the method can change the string’s content by adding the user input.

The `&` indicates that this argument is a _reference_, which gives you a way to let multiple parts of your code access one piece of data without needing to copy that data into memory multiple times.

As mentioned earlier, read_line puts what the user types into the string we’re passing it, but it also returns a value—in this case, an io::Result. Rust has a number of types named Result in its standard library: a generic Result as well as specific versions for submodules, such as io::Result.

The Result types are enumerations, often referred to as enums. An enumeration is a type that can have a fixed set of values, and those values are called the enum’s variants. Chapter 6 will cover enums in more detail.

For Result, the variants are Ok or Err. The Ok variant indicates the operation was successful, and inside Ok is the successfully generated value. The Err variant means the operation failed, and Err contains information about how or why the operation failed.

The purpose of these Result types is to encode error-handling information. Values of the Result type, like values of any type, have methods defined on them. An instance of io::Result has an expect method that you can call. If this instance of io::Result is an Err value, expect will cause the program to crash and display the message that you passed as an argument to expect. If the read_line method returns an Err, it would likely be the result of an error coming from the underlying operating system. If this instance of io::Result is an Ok value, expect will take the return value that Ok is holding and return just that value to you so you can use it. In this case, that value is the number of bytes in what the user entered into standard input.


- In order to install new _crate_, we first need to edit Cargo.toml.

-  The number `0.8.3` is actually shorthand for `^0.8.3`, which means any version that is at least `0.8.3` but below `0.9.0`. Any version `0.9.0` or greater is not guaranteed to have the same API as what the following examples use.

- `Crates.io` is where people in the Rust ecosystem post their open source Rust projects for others to use.

Cargo has a mechanism that ensures you can rebuild the same artifact every time you or anyone else builds your code: Cargo will use only the versions of the dependencies you specified until you indicate otherwise. For example, what happens if next week version 0.8.4 of the rand crate comes out and contains an important bug fix but also contains a regression that will break your code? The answer to this problem is the Cargo.lock file, which was created the first time you ran cargo build and is now in your guessing_game directory. When you build a project for the first time, Cargo figures out all the versions of the dependencies that fit the criteria and then writes them to the Cargo.lock file. When you build your project in the future, Cargo will see that the Cargo.lock file exists and use the versions specified there rather than doing all the work of figuring out versions again. This lets you have a reproducible build automatically. In other words, your project will remain at 0.8.3 until you explicitly upgrade, thanks to the Cargo.lock file.

`cargo update` will ignore the Cargo.lock file and figure out all the latest versions that fit your specifications in Cargo.toml. If that works, Cargo will write those versions to the Cargo.lock file. But by default, Cargo will only look for versions greater than `0.8.3` and less than `0.9.0`.

Next, we’re adding two lines in the middle. The `rand::thread_rng` function will give us the particular random number generator that we’re going to use: one that is local to the current thread of execution and seeded by the operating system. Then we call the `gen_range` method on the random number generator. This method is defined by the `Rng` trait that we brought into scope with the use `rand::Rng` statement. The `gen_range` method takes a range expression as an argument and generates a random number in the range. The kind of range expression we’re using here takes the form `start..end`. It’s inclusive on the lower bound but exclusive on the upper bound, so we need to specify `1..101` to request a number between 1 and 100. Alternatively, we could pass the range 1..=100, which is equivalent.

- `cargo doc --open` creates document page with current dependencies.

The first new bit here is another use statement, bringing a type called `std::cmp::Ordering` into scope from the standard library. Like `Result`, `Ordering` is another `enum`, but the variants for `Ordering` are `Less`, `Greater`, and `Equal`. These are the three outcomes that are possible when you compare two values.

Then we add five new lines at the bottom that use the `Ordering` type. The `cmp` method compares two values and can be called on anything that can be compared. It takes a reference to whatever you want to compare with: here it’s comparing the `guess` to the `secret_number`. Then it returns a variant of the `Ordering` enum we brought into scope with the use statement. We use a `match` expression to decide what to do next based on which variant of Ordering was returned from the call to `cmp` with the values in `guess` and `secret_number`.

A `match` expression is made up of _arms_. An arm consists of a _pattern_ and the code that should be run if the value given to the beginning of the match expression fits that arm’s pattern. Rust takes the value given to match and looks through each arm’s pattern in turn. 

- Shadowing: We create a variable named guess. But wait, doesn’t the program already have a variable named guess? It does, but Rust allows us to shadow the previous value of guess with a new one. This feature is often used in situations in which you want to convert a value from one type to another type. Shadowing lets us reuse the guess variable name rather than forcing us to create two unique variables, such as guess_str and guess for example. 

- Error handling w/ `match` or `expect`.


## CH 3: Common Concepts

### Variables
- Variables are _immutable_ by default.
- `mut` conveys intent to future readers of the code by indicating that other parts of the code will be changing this variable’s value.
```rust
let x = 13;
let mut y = 13;
```

### Constants

```rust
const MAX_POINTS: u32 = 100_000;
```

- Constants can be declared in any scope, including the global scope, which makes them useful for values that many parts of code need to know about.
- The last difference is that constants may be set only to a constant expression, not the result of a function call or any other value that could only be computed at runtime.

### Shadowing

```rust
let x = 5;

let x = x + 1;

let x = x * 2;
```

- You can declare a new variable with the same name as a previous variable. Rustaceans say that the first variable is shadowed by the second, which means that the second variable’s value is what appears when the variable is used. 
- Shadowing is different from marking a variable as `mut`, because we’ll get a compile-time error if we accidentally try to reassign to this variable without using the `let` keyword. By using `let`, we can perform a few transformations on a value but have the variable be immutable after those transformations have been completed.
- The other difference between `mut` and shadowing is that because we’re effectively creating a new variable when we use the `let` keyword again, we can change the type of the value but reuse the same name.

### Data Types
- We’ll look at two data type subsets: _scalar_ and _compound_.
- The compiler can usually infer what type we want to use based on the value and how we use it. In cases when many types are possible

#### Scalar Types
- _integers, floating-point numbers, Booleans, and characters_. 
- Each *integer* variant can be either signed or unsigned and has an explicit size. Signed and unsigned refer to whether it’s possible for the number to be negative—in other words, whether the number needs to have a sign with it (signed) or whether it will only ever be positive and can therefore be represented without a sign (unsigned). It’s like writing numbers on paper: when the sign matters, a number is shown with a plus sign or a minus sign; however, when it’s safe to assume the number is positive, it’s shown with no sign. Signed numbers are stored using two’s complement representation. _i8, u8, i16, u16, i32, u32, i64, u64, i128, u128, isize, usize_
- So how do you know which type of integer to use? If you’re unsure, Rust’s defaults are generally good choices, and integer types default to i32: this type is generally the fastest, even on 64-bit systems. The primary situation in which you’d use isize or usize is when indexing some sort of collection.


- *floating-point types*: f32, f64
- The default type is f64 because on modern CPUs it’s roughly the same speed as f32 but is capable of more precision.

- *boolean-type*: bool (1 byte)

- *char type*: char (4 bytes), unicode scalar vbalue
- `char` literals are specified with single quotes, as opposed to string literals, which use double quotes.


#### Compound Types
- *tuple type*
```rust
let tup: (i32, f64, u8) = (500, 6.4, 1);
let (x, y, z) = tup;  // destructuring

let five_hundred = x.0;
let six_point_four = x.1;let six_point_four = x.1;
```

- *array type*
- Unlike a tuple, every element of an array must have the same type. Arrays in Rust are different from arrays in some other languages because arrays in Rust have a fixed length, like tuples.
- Arrays are useful when you want your data allocated on the stack rather than the heap or when you want to ensure you always have a fixed number of elements. 
```rust
let a = [1, 2, 3, 4, 5];
let a: [i32; 5] = [1, 2, 3, 4, 5];

let a = [3; 5];  // same as: let a = [3, 3, 3, 3, 3];

let first = a[0];
```

- The program will result in a _runtime error_ if we try to access invalid index in an array. This is the first example of Rust’s safety principles in action. In many low-level languages, this kind of check is not done, and when you provide an incorrect index, invalid memory can be accessed. Rust protects you against this kind of error by immediately exiting instead of allowing the memory access and continuing.


### Functions
- Rust code uses snake case as the conventional style for function and variable names.
- Function definitions in Rust start with `fn` and have a set of parentheses after the function name. The curly brackets tell the compiler where the function body begins and ends.
- Rust doesn’t care where you define your functions, only that they’re defined somewhere.
- In function signatures, you must declare the type of each parameter. This is a deliberate decision in Rust’s design: requiring type annotations in function definitions means the compiler almost never needs you to use them elsewhere in the code to figure out what you mean.
```rust
fn another_function(x: i32, y: bool) {
    println!("The value of x is: {}", x);
    println!("The value of y is: {}", y);
}

fn plus_one(x: i32) -> i32 {
    x + 1
}
```
- `let y = 6;` is a statement. Statement does not return a value
- Expressions evaluate to something and make up most of the rest of the code that you’ll write in Rust. Consider a simple math operation, such as `5 + 6`, which is an expression that evaluates to the value 11.
```rust
let x = 5;

let y = {
    let x = 3;
    x + 1
};

println!("The value of y is: {}", y);  // 4
```
- Expressions do not include ending semicolons. If you add a semicolon to the end of an expression, you turn it into a statement, which will then not return a value. Keep this in mind as you explore function return values and expressions next.

- In Rust, the return value of the function is synonymous with the value of the final expression in the block of the body of a function. You can return early from a function by using the `return` keyword and specifying a value, but most functions return the last expression implicitly.


```rust
fn plus_one(x: i32) -> i32 {
    x + 1;
}
```
- Compiling this code produces an error. The main error message, “mismatched types,” reveals the core issue with this code. The definition of the function plus_one says that it will return an i32, but statements don’t evaluate to a value, which is expressed by (), an empty tuple. Therefore, nothing is returned, which contradicts the function definition and results in an error. In this output, Rust provides a message to possibly help rectify this issue: it suggests removing the semicolon, which would fix the error.


### Control Flow
```rust
fn main() {
    let number = 3;

    if number < 5 {
        println!("condition was true");
    } else {
        println!("condition was false");
    }
}
```

- Blocks of code associated with the conditions in `if` expressions are sometimes called arms, just like the arms in `match` expressions.
- It’s also worth noting that the condition in this code _must_ be a `bool`. If the condition isn’t a `bool`, we’ll get an error. For example, try running the following code:
```rust
let number = 3;

if number {
    println!("number was three");
}
```

- Using too many `else if` expressions can clutter your code, so if you have more than one, you might want to refactor your code. Chapter 6 describes a powerful Rust branching construct called `match` for these cases.

- Because `if` is an expression, we can use it on the right side of a `let` statement.
```rust
let condition = true;
let number = if condition { 5 } else { 6 };         
```
- In the preceding code snippet, both if and else return expressions should return the same type. Otherwise, we will get an error during compile time.

#### Repetition with Loops
```rust
// Infinite loop
loop {
    println!("again!");
}


//  Returning values from loops
let mut counter = 0;
// Result will be 20.
let result = loop {
    counter += 1;

    if counter == 10 {
        break counter * 2;
    }
};


// Conditional Loops with while
let mut number = 3;

while number != 0 {
    println!("{}!", number);

    number -= 1;
}


// Looping Through a Collection with for
let a = [10, 20, 30, 40, 50];
for element in a.iter() {
    println!("the value is: {}", element);
}

for number in (1..4).rev() {
    println!("{}!", number);
}
```
- The safety and conciseness of for loops make them the most commonly used loop construct in Rust.



## CH 4: Ownership
- Ownership is Rust’s most unique feature, and it enables Rust to make memory safety guarantees without needing a garbage collector. Rust’s central feature is _ownership_. 
- All programs have to manage the way they use a computer’s memory while running. Some languages have garbage collection that constantly looks for no longer used memory as the program runs; in other languages, the programmer must explicitly allocate and free the memory. Rust uses a third approach: memory is managed through a system of ownership with a set of rules that the compiler checks at compile time. None of the ownership features slow down your program while it’s running.

> - In many programming languages, you don’t have to think about the stack and the heap very often. But in a systems programming language like Rust, whether a value is on the stack or the heap has more of an effect on how the language behaves and why you have to make certain decisions. 
> - Both the stack and the heap are parts of memory that are available to your code to use at runtime, but they are structured in different ways. The stack stores values in the order it gets them and removes the values in the opposite order.
> - All data stored on the stack must have a known, fixed size. Data with an unknown size at compile time or a size that might change must be stored on the heap instead. The heap is less organized: when you put data on the heap, you request a certain amount of space. The memory allocator finds an empty spot in the heap that is big enough, marks it as being in use, and returns a pointer, which is the address of that location. This process is called allocating on the heap and is sometimes abbreviated as just allocating. Pushing values onto the stack is not considered allocating. Because the pointer is a known, fixed size, you can store the pointer on the stack, but when you want the actual data, you must follow the pointer.
> - Pushing to the stack is faster than allocating on the heap because the allocator never has to search for a place to store new data; that location is always at the top of the stack. Comparatively, allocating space on the heap requires more work, because the allocator must first find a big enough space to hold the data and then perform bookkeeping to prepare for the next allocation.
> - Accessing data in the heap is slower than accessing data on the stack because you have to follow a pointer to get there. Contemporary processors are faster if they jump around less in memory.
> - Keeping track of what parts of code are using what data on the heap, minimizing the amount of duplicate data on the heap, and cleaning up unused data on the heap so you don’t run out of space are all problems that ownership addresses.

- Each value in Rust has a variable that’s called its owner.
- There can only be one owner at a time.
- When the owner goes out of scope, the value will be dropped.

Left-off here: https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html#variable-scope