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