# The Rust Programming Language
## https://doc.rust-lang.org/book/title-page.html
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
