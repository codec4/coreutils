# Practical Exercises for Learning Rust with uutils Coreutils

## Overview

This guide provides hands-on exercises to help you learn Rust by modifying and extending actual utilities in the uutils coreutils project. Each exercise builds on previous knowledge and introduces new Rust concepts.

## Prerequisites

- Rust installed and working
- coreutils repository cloned
- GNU coreutils built (for testing)
- Ability to run `cargo build` and `cargo test`

## Exercise 0: Setup and Verification

### Objective

Verify your environment is set up correctly and understand the build process.

### Steps

1. **Build the default coreutils binary:**

```bash
cd coreutils
cargo build --release
```

2. **Run echo to verify it works:**

```bash
./target/release/coreutils echo "Hello, Rust!"
```

3. **Run the test suite for echo:**

```bash
cargo test -p uu_echo
```

4. **Run GNU test suite for echo:**

```bash
bash util/run-gnu-test.sh tests/misc/echo.sh
```

### Success Criteria

- All tests pass
- Echo command prints "Hello, Rust!"
- No compiler warnings

---

## Exercise 1: Understand Flag Processing

### Objective

Learn how echo processes flags by adding debug output.

### What You'll Learn

- How to read and understand existing code
- How `match` statements work
- How mutable references work
- Using `eprintln!()` for debugging

### Steps

1. **Open the echo source:**

```bash
cat src/uu/echo/src/echo.rs
```

2. **Add debug output to `is_flag()` function:**
   - Find the `is_flag()` function (around line 59)
   - Add this line at the start of the function:

```rust
eprintln!("DEBUG is_flag: arg={:?}", arg);
```

3. **Build and test:**

```bash
cargo build -p uu_echo
./target/debug/coreutils echo -n "test"
```

You should see debug output showing each argument being processed.

4. **Add more debug output:**
   - Add debug output inside the `match` statement
   - Show which flag character was matched
   - Show the resulting options

5. **Understand the output:**
   - Which arguments are flags?
   - Which are not?
   - How does the function stop processing flags?

### Follow-up Questions

1. What happens if you run: `echo -n -e test`?
2. What happens if you run: `echo -ne test`?
3. Why does the function return early in the `_ => return false` case?

### Cleanup

Remove the debug output before proceeding.

---

## Exercise 2: Add a New Flag

### Objective

Add functionality to echo by implementing a new flag.

### What You'll Learn

- How to modify existing code
- How to extend enums/structs
- How to handle new behavior
- Testing your changes

### Task: Add a `-c` (capitalize) flag

The `-c` flag should capitalize the first letter of each argument.

### Steps

1. **Modify the Options struct** (line ~22):

```rust
#[derive(Debug, Clone, Copy)]
struct Options {
    pub trailing_newline: bool,
    pub escape: bool,
    pub capitalize: bool,  // ADD THIS LINE
}
```

2. **Update Default implementation** (line ~28):

```rust
impl Default for Options {
    fn default() -> Self {
        Self {
            trailing_newline: true,
            escape: false,
            capitalize: false,  // ADD THIS LINE
        }
    }
}
```

3. **Update is_flag() function** (inside the match statement, ~line 74):

```rust
match c {
    b'e' => options_.escape = true,
    b'E' => options_.escape = false,
    b'n' => options_.trailing_newline = false,
    b'c' => options_.capitalize = true,  // ADD THIS LINE
    _ => return false,
}
```

4. **Modify execute() function** to handle capitalization:
   - Find where the output is written
   - Before writing bytes, check if `options.capitalize` is true
   - If true, capitalize the first byte of each argument

Hint: You can capitalize like this:

```rust
if options.capitalize && !bytes.is_empty() {
    // Create a new Vec with the first byte capitalized
    let mut capitalized = bytes.to_vec();
    capitalized[0] = capitalized[0].to_ascii_uppercase();
    stdout.write_all(&capitalized)?;
} else {
    stdout.write_all(bytes)?;
}
```

5. **Test your changes:**

```bash
cargo build -p uu_echo
./target/debug/coreutils echo -c "hello world"
./target/debug/coreutils echo -c hello world
./target/debug/coreutils echo "hello"  # Should not capitalize
```

6. **Expected output:**

```
Hello world
Hello world
hello
```

### Bonus Challenge

- Make `-c` work with escape sequences: `echo -ce "hello\nworld"`
- Add a `-C` flag to disable capitalization (like `-E` for escape)
- Add capitalization of all letters: `echo -C HELLO` -> should print "HELLO"

### Testing Considerations

- Does it work with no arguments?
- Does it work with multiple arguments?
- Does it interact correctly with `-n` and `-e`?
- Does it work with special characters?

---

## Exercise 3: Create Unit Tests

### Objective

Write proper Rust unit tests for the functionality you created.

### What You'll Learn

- How to write `#[test]` functions
- Test organization and naming
- Using `assert!` and `assert_eq!`
- How to test private functions

### Steps

1. **Add a test module to echo.rs** (at the end of the file):

```rust
#[cfg(test)]
mod tests {
    use super::*;
    use std::ffi::OsStr;

    #[test]
    fn test_is_flag_recognizes_n() {
        let mut opts = Options::default();
        assert!(is_flag(OsStr::new("-n"), &mut opts));
        assert!(!opts.trailing_newline);
    }

    #[test]
    fn test_is_flag_recognizes_e() {
        let mut opts = Options::default();
        assert!(is_flag(OsStr::new("-e"), &mut opts));
        assert!(opts.escape);
    }

    #[test]
    fn test_is_flag_rejects_non_flag() {
        let mut opts = Options::default();
        assert!(!is_flag(OsStr::new("hello"), &mut opts));
    }

    #[test]
    fn test_is_flag_recognizes_combined_flags() {
        let mut opts = Options::default();
        assert!(is_flag(OsStr::new("-ne"), &mut opts));
        assert!(!opts.trailing_newline);
        assert!(opts.escape);
    }

    #[test]
    fn test_capitalize_flag() {
        let mut opts = Options::default();
        assert!(is_flag(OsStr::new("-c"), &mut opts));
        assert!(opts.capitalize);
    }

    #[test]
    fn test_default_options() {
        let opts = Options::default();
        assert!(opts.trailing_newline);
        assert!(!opts.escape);
        assert!(!opts.capitalize);
    }
}
```

2. **Run the tests:**

```bash
cargo test -p uu_echo
```

3. **Add more tests:**
   - Test `filter_flags()` function
   - Test combinations of flags
   - Test that invalid flags are rejected

### Test Naming Convention

Tests should clearly describe what they're testing:

- `test_is_flag_recognizes_n` - ✅ Clear
- `test_flag_n` - ❌ Too vague
- `test_is_flag_with_combined_flags_ne` - ✅ Clear

### Bonus Challenge

Write integration tests in `tests/by-util/test_echo.rs` that:

- Run the compiled binary
- Capture stdout
- Verify output matches expected

---

## Exercise 4: Explore the Iterator Pattern

### Objective

Understand Rust's iterator pattern and functional programming style.

### What You'll Learn

- Iterators and the Iterator trait
- `.map()`, `.filter()`, `.collect()`
- Functional programming patterns
- Lazy evaluation

### Task: Refactor filter_flags using iterators

1. **Current implementation** (lines 98-115):

```rust
fn filter_flags(mut args: impl Iterator<Item = OsString>) -> (Vec<OsString>, Options) {
    let mut arguments = Vec::with_capacity(args.size_hint().0);
    let mut options = Options::default();

    for arg in &mut args {
        if !is_flag(&arg, &mut options) {
            arguments.push(arg);
            break;
        }
    }

    arguments.extend(args);
    (arguments, options)
}
```

2. **Study the current code:**
   - Why does it use a `for` loop instead of `.collect()`?
   - Why does it break early?
   - What does `.extend()` do?

3. **Write an explanation:**
   - Document why this function can't be easily converted to pure iterator chains
   - Hint: It needs to stop processing flags after the first non-flag
   - This is why loops are sometimes better than iterator chains

4. **Create a new version** that processes only flags:

```rust
fn extract_flag_args(args: impl Iterator<Item = OsString>)
    -> Vec<(OsString, bool)> {
    // Return a Vec of (arg, is_flag) tuples
    // Use .map() to transform each argument
}
```

5. **Bonus:**
   - Use the new function to reimplement `filter_flags()`
   - Compare readability of both versions
   - Learn when to use iterators vs loops

---

## Exercise 5: Error Handling Deep Dive

### Objective

Understand Rust's error handling with `Result<T, E>` and the `?` operator.

### What You'll Learn

- `Result<T, E>` enum
- The `?` operator for error propagation
- Custom error types
- Error messages

### Steps

1. **Look at the execute() function:**
   - Find all uses of `?` operator
   - For each, understand what error it's handling

2. **Add explicit error handling:**
   - Replace one `?` with an explicit `match` statement
   - See how verbose it becomes
   - Understand why `?` is preferred

Example:

```rust
// With ?
let bytes = os_str_as_bytes(&arg)?;

// With match
let bytes = match os_str_as_bytes(&arg) {
    Ok(b) => b,
    Err(e) => return Err(e),
};
```

3. **Create a new error case:**
   - What if capitalization should fail for non-ASCII?
   - Add a check: `if options.capitalize && !bytes.is_ascii() { ... }`
   - Return an error using the `?` operator

4. **Understand UResult:**
   - Find where `UResult` is defined
   - Look at `src/uucore/src/error.rs`
   - Understand how it differs from standard `Result`

5. **Write error messages:**
   - When should you use `eprintln!()`?
   - When should you return an error?
   - Look at how other utilities handle errors

---

## Exercise 6: Study the Builder Pattern

### Objective

Understand the builder pattern used in argument parsing.

### What You'll Learn

- Builder pattern basics
- Method chaining
- Fluent APIs
- clap argument parser

### Steps

1. **Look at uu_app() function** (lines 161-218):
   - This creates a `Command` using the builder pattern
   - Each `.method()` call returns `self`
   - This allows chaining

2. **Understand clap:**

```bash
# View clap documentation
cargo doc --open -p clap
```

3. **Modify the help text:**
   - Change `.about(translate!("echo-about"))`
   - Provide a custom string instead
   - Rebuild and run: `./target/debug/coreutils echo --help`

4. **Add your capitalize flag to clap:**
   - Find where `-n` flag is defined
   - Add a similar definition for `-c`
   - Include help text

Example:

```rust
.arg(
    Arg::new("capitalize")
        .short('c')
        .help("Capitalize first letter of each argument")
        .action(ArgAction::SetTrue),
)
```

5. **Bonus Challenge:**
   - Add a `--capitalize` long form
   - Add mutually exclusive flags (like `-e` and `-E`)
   - Create a custom value parser

---

## Exercise 7: Integration Testing

### Objective

Test the echo utility as a complete program, not just individual functions.

### What You'll Learn

- Integration tests in Rust
- Running subprocesses
- Capturing output
- Testing exit codes

### Steps

1. **Look at existing tests:**

```bash
cat tests/by-util/test_echo.rs
```

2. **Understand the test structure:**
   - Tests use `common` module for utilities
   - Tests run the compiled binary
   - Tests check stdout, stderr, and exit code

3. **Add a test for your capitalize flag:**

```rust
#[test]
fn test_echo_capitalize() {
    new_ucmd!()
        .arg("-c")
        .arg("hello")
        .succeeds()
        .stdout_only("Hello\n");
}
```

4. **Run the tests:**

```bash
cargo test -p uu_echo
```

5. **Add more comprehensive tests:**
   - Test combinations of flags
   - Test with special characters
   - Test with empty strings

---

## Exercise 8: Compare with GNU

### Objective

Verify your implementation matches GNU coreutils behavior.

### What You'll Learn

- How to use GNU test suite
- Debugging test failures
- Understanding GNU behavior

### Steps

1. **Run GNU tests:**

```bash
bash util/run-gnu-test.sh tests/misc/echo.sh
```

2. **Test your capitalize flag manually:**

```bash
# With GNU
../gnu/src/echo -c hello  # Should just print "-c hello"

# With your implementation
./target/debug/coreutils echo -c hello  # Should capitalize
```

Note: GNU doesn't have `-c` so this won't match. That's okay - it's your custom extension!

3. **Test standard flags match GNU:**

```bash
# Both should produce identical output
echo -n "test"
../gnu/src/echo -n "test"

echo -e "test\n"
../gnu/src/echo -e "test\n"
```

4. **Find edge cases:**
   - Test POSIXLY_CORRECT mode
   - Test with special bytes
   - Test with very long arguments

---

## Exercise 9: Refactor for Clarity

### Objective

Improve code readability and maintainability.

### What You'll Learn

- Code organization
- Naming conventions
- Separation of concerns
- Documentation

### Steps

1. **Extract the capitalize logic:**
   - Create a new function: `fn capitalize_byte(b: u8) -> u8`
   - Use it in execute()

Example:

```rust
fn capitalize_byte(b: u8) -> u8 {
    b.to_ascii_uppercase()
}
```

2. **Add documentation comments:**
   - Document the `Options` struct
   - Document each field
   - Use `///` for doc comments

Example:

````rust
/// Configuration for echo command behavior.
///
/// # Examples
///
/// ```
/// let opts = Options::default();
/// assert!(opts.trailing_newline);
/// ```
#[derive(Debug, Clone, Copy)]
struct Options {
    /// Whether to include a newline at the end
    pub trailing_newline: bool,

    /// Whether to interpret backslash escapes
    pub escape: bool,
}
````

3. **Add tests to doc comments:**
   - Doc comments can include `#[test]` examples
   - These are tested with `cargo test --doc`

4. **Organize the code:**
   - Group related functions together
   - Add comments explaining sections
   - Create separate modules if needed

---

## Exercise 10: Explore Another Utility

### Objective

Apply your learning to a different utility.

### What You'll Learn

- Patterns used across utilities
- Different problem domains
- Real-world Rust code

### Choose One to Study:

**`true` / `false` (simplest)**

- Just returns success/failure
- No arguments processing
- Great for understanding exit codes

**`cat` (file I/O)**

- Reads files
- Handles multiple files
- Error handling for file errors

**`yes` (infinite loops)**

- Generates infinite output
- Signal handling (Ctrl+C)
- Buffering strategies

**`head` / `tail` (line processing)**

- Line-based iteration
- Numeric limits
- Different output modes

### Steps

1. **Pick a utility and build it:**

```bash
cargo build -p uu_cat
```

2. **Read the source code:**

```bash
cat src/uu/cat/src/cat.rs | head -100
```

3. **Identify key patterns:**
   - How does it handle arguments?
   - How does it handle errors?
   - What's the main loop/algorithm?

4. **Add a feature:**
   - Something small like a new flag
   - Apply patterns you learned from echo

5. **Test thoroughly:**

```bash
cargo test -p uu_cat
bash util/run-gnu-test.sh tests/misc/cat.sh
```

---

## Exercise Solutions

Solutions are NOT provided here intentionally. This is a learning exercise:

1. **Try the exercise first**
2. **Struggle a bit** - this is where learning happens
3. **Check your compiler errors** - they're usually very helpful
4. **Look at similar code** - other utilities have patterns you can adapt
5. **Read the Rust book** - if you're stuck on a concept
6. **Ask the community** - r/rust, Rust forum, etc.

---

## Debugging Tips

### Use `cargo check` for quick feedback:

```bash
cargo check -p uu_echo  # Just check syntax, don't compile
```

### Use `dbg!()` macro for debugging:

```rust
let bytes = dbg!(os_str_as_bytes(&arg)?);
```

### Use `eprintln!()` for stderr output:

```rust
eprintln!("DEBUG: options = {:?}", options);
```

### Use `cargo expand` to see macros:

```bash
cargo install cargo-expand
cargo expand -p uu_echo  # Shows what macros expand to
```

### Run single test:

```bash
cargo test -p uu_echo test_is_flag_recognizes_n
```

### Run tests with output:

```bash
cargo test -p uu_echo -- --nocapture
```

---

## Common Errors and How to Fix Them

### "cannot borrow as mutable more than once"

- You have conflicting mutable borrows
- Use separate scopes or restructure the code

### "expected type, found enum 'Result'"

- You forgot the `?` operator or pattern match
- Use `?` to unwrap Result, or `match` to handle both cases

### "the trait bound is not satisfied"

- You're using a type that doesn't implement required trait
- Check what traits a type needs
- Implement the trait or use a different type

### "use of moved value"

- You moved ownership of a value, then tried to use it
- Use references `&` or `&mut` to borrow instead
- Or clone the value if needed

---

## Next Steps After These Exercises

1. Contribute to uutils
   - Fix a bug in an existing utility
   - Implement a missing feature
   - Improve documentation

2. Study more complex utilities
   - `sort` - algorithms and comparison
   - `grep` - regular expressions
   - `find` - recursive directory traversal

3. Learn advanced topics
   - Lifetimes and ownership
   - Traits and generics
   - Error types and custom implementations

4. Build your own tools
   - Small command-line utilities
   - Apply Rust patterns you've learned
   - Share on GitHub

---

## Resources

- **Rust Book**: https://doc.rust-lang.org/book/
- **Rust by Example**: https://doc.rust-lang.org/rust-by-example/
- **Rustlings**: https://github.com/rust-lang/rustlings (interactive lessons)
- **Cargo Book**: https://doc.rust-lang.org/cargo/
- **Error Handling**: https://doc.rust-lang.org/book/ch09-00-error-handling.html
- **Testing**: https://doc.rust-lang.org/book/ch11-00-testing.html

---

Good luck, and happy learning! 🦀
