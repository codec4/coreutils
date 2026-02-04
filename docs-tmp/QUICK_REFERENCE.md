# Quick Reference Guide: Learning Rust with uutils coreutils

## Getting Started

### Build and Test

```bash
cd coreutils

# Build a specific utility
cargo build -p uu_echo

# Run tests
cargo test -p uu_echo

# Run against GNU test suite
bash util/run-gnu-test.sh tests/misc/echo.sh

# Run the utility
./target/debug/coreutils echo "Hello"
```

### Project Structure

```
src/uu/UTILITY_NAME/
├── src/
│   ├── main.rs      # Entry point (just calls uucore::bin!(module_name))
│   └── UTILITY.rs   # Main implementation
├── Cargo.toml       # Package metadata
└── locales/         # Translations
```

---

## Rust Basics Cheat Sheet

### Variables and Types

```rust
let x = 5;              // Immutable, type inferred as i32
let mut y = 5;          // Mutable
let z: u8 = 255;        // Explicit type
const MAX: u32 = 100;   // Compile-time constant

// String types
let s = "hello";                    // &str (string slice)
let s = String::from("hello");      // String (owned)
let bytes = b"hello";               // &[u8] (byte slice)
```

### Ownership & Borrowing

```rust
// Ownership - moves value
let s1 = String::from("hello");
let s2 = s1;           // s1 is moved, can't use s1 anymore

// Borrowing - doesn't move
let s1 = String::from("hello");
let s2 = &s1;          // Immutable borrow
let s3 = &mut s1;      // Mutable borrow (if s1 is mut)

// Rules:
// - Can have many immutable borrows OR one mutable borrow
// - References must be valid for their lifetime
```

### Pattern Matching

```rust
match value {
    pattern1 => { /* code */ },
    pattern2 => { /* code */ },
    _ => { /* default case */ },
}

// if let - simpler match for single case
if let Some(value) = option_val {
    // code
}

// while let - loop until pattern doesn't match
while let Some(item) = iterator.next() {
    // code
}
```

### Errors with Result

```rust
// Result<T, E> - either Ok(value) or Err(error)
let result: Result<i32, String> = Ok(5);

// Unwrap (panics if Err)
let value = result.unwrap();

// ? operator (returns error if Err)
fn do_something() -> Result<(), Error> {
    let value = some_function()?;  // Returns if Err
    Ok(())
}

// match on Result
match result {
    Ok(val) => println!("{}", val),
    Err(e) => eprintln!("Error: {}", e),
}
```

### Iterators

```rust
let vec = vec![1, 2, 3];

// for loop
for item in &vec {
    println!("{}", item);
}

// Method chain
vec.iter()
   .filter(|&x| x > 1)
   .map(|x| x * 2)
   .collect::<Vec<_>>()

// Common methods
.map(|x| x + 1)        // Transform each item
.filter(|x| x > 0)     // Keep items where condition is true
.collect::<Vec<_>>()   // Collect into Vec (infer type with _)
.sum::<i32>()          // Sum all items
.count()               // Count items
.enumerate()           // Get (index, item) pairs
```

### Structs and Traits

```rust
#[derive(Debug, Clone)]  // Auto-implement Debug, Clone
struct MyStruct {
    field1: String,
    field2: i32,
}

// Implement methods
impl MyStruct {
    fn new(field1: String, field2: i32) -> Self {
        MyStruct { field1, field2 }
    }
}

// Traits - define behavior
trait Drawable {
    fn draw(&self);
}

impl Drawable for MyStruct {
    fn draw(&self) {
        println!("{}", self.field1);
    }
}
```

### Macros

```rust
println!("Hello {}", name);     // Print with newline
eprintln!("Error: {}", msg);    // Print to stderr
format!("Hello {}", name);      // Format to string
assert!(condition);              // Assert is true
assert_eq!(a, b);               // Assert equal
dbg!(variable);                 // Debug print and return value

// Attribute macros
#[test]                         // Mark as test
#[derive(Debug)]                // Auto-implement traits
#[cfg(test)]                    // Compile only if testing
```

---

## Common uutils Patterns

### Entry Point

```rust
// main.rs - always just this:
uucore::bin!(uu_module_name);

// This calls uumain() function from module_name.rs
```

### Main Function

```rust
#[uucore::main]
pub fn uumain(args: impl uucore::Args) -> UResult<()> {
    let args: Vec<OsString> = args.skip(1).collect();

    // Process arguments
    let options = parse_args(&args)?;

    // Execute
    execute(options)?;

    Ok(())  // Success
}
```

### Argument Parsing

```rust
use clap::{Arg, Command};

pub fn uu_app() -> Command {
    Command::new("utility_name")
        .about("Description")
        .version(uucore::crate_version!())
        .arg(
            Arg::new("flag_name")
                .short('f')
                .long("flag-name")
                .help("Help text")
                .action(ArgAction::SetTrue)
        )
        .arg(
            Arg::new("value")
                .value_parser(ValueParser::os_string())
        )
}
```

### Options Struct

```rust
#[derive(Debug, Clone, Copy)]
struct Options {
    pub flag1: bool,
    pub flag2: bool,
}

impl Default for Options {
    fn default() -> Self {
        Self {
            flag1: false,
            flag2: false,
        }
    }
}
```

### Output/I/O

```rust
use std::io::Write;

// Write to stdout
stdout.write_all(b"text")?;
stdout.write_all("text".as_bytes())?;
println!("text");

// Write to stderr
eprintln!("error");

// Lock stdout for multiple writes
let mut stdout = io::stdout().lock();
stdout.write_all(b"data")?;
```

### Working with Files

```rust
use std::fs::File;
use std::io::BufRead;

// Read file
let file = File::open("path")?;
let reader = BufReader::new(file);
for line in reader.lines() {
    let line = line?;
    // process line
}

// Write file
let mut file = File::create("path")?;
file.write_all(b"content")?;
```

### Working with OS Strings

```rust
use std::ffi::{OsStr, OsString};

let os_str: &OsStr = OsStr::new("hello");
let bytes = os_str_as_bytes(&os_str)?;  // uucore function
let string = String::from_utf8_lossy(bytes);
```

---

## Testing Patterns

### Unit Tests

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_something() {
        let result = some_function();
        assert_eq!(result, expected);
        assert!(result > 0);
    }
}

// Run: cargo test -p uu_module
```

### Integration Tests

```rust
// In tests/by-util/test_module.rs
#[test]
fn test_command() {
    new_ucmd!()
        .arg("--flag")
        .arg("value")
        .succeeds()
        .stdout_only("expected output\n");
}

// Run: cargo test -p uu_module
```

---

## Common Rust File Operations

### Derive Traits

```rust
#[derive(Debug)]       // Enables {:?} in println
#[derive(Clone)]       // Can .clone() the value
#[derive(Copy)]        // Copied automatically (small types only)
#[derive(Default)]     // Can use Type::default()
#[derive(PartialEq)]   // Can use ==
#[derive(Eq)]          // Equality is reflexive
```

### Common Methods

**String/&str:**

```rust
"hello".len()              // 5
"hello".chars()            // Iterator over chars
"hello".bytes()            // Iterator over bytes
"hello".split(' ')         // Split by delimiter
"hello".trim()             // Remove whitespace
"hello".to_uppercase()     // Convert case
"hello".contains("ll")     // Check contains
```

**Vec:**

```rust
vec![1,2,3].len()          // 3
vec![1,2,3].push(4)        // Add to end (mut)
vec![1,2,3].pop()          // Remove from end (mut)
vec![1,2,3].iter()         // Iterator
vec![1,2,3].into_iter()    // Consuming iterator
vec![1,2,3][0]             // Index access
```

**Option/Result:**

```rust
Some(5).is_some()          // true
None.is_none()             // true
Some(5).unwrap()           // 5 (panics if None)
Some(5).unwrap_or(0)       // 5 (0 if None)
Ok(5).is_ok()              // true
Err(e).is_err()            // true
```

---

## Compilation Flags

### Build Variants

```bash
cargo build              # Debug build (slow, easy to debug)
cargo build --release   # Release build (fast, optimized)
cargo check            # Check without compiling
cargo clippy           # Lint suggestions
cargo fmt              # Format code
```

### Feature Flags

```bash
cargo build -p uu_cat --all-features
cargo build -p uu_cat --features unix
cargo test --features unix
```

---

## Debugging Techniques

### Print Debugging

```rust
eprintln!("Variable: {}", var);           // Print with label
eprintln!("Debug: {:?}", var);            // Print with debug
eprintln!("Pretty: {:#?}", var);          // Pretty print
dbg!(var);                                // Print and return value
```

### Testing

```bash
# Run specific test
cargo test test_name

# Run with output
cargo test -- --nocapture

# Run in parallel
cargo test -- --test-threads=4

# Run single-threaded
cargo test -- --test-threads=1
```

### Finding Issues

```bash
cargo check           # Syntax errors only
cargo clippy          # Lint warnings
cargo fmt --check    # Format issues
cargo build --verbose # See what's happening
```

---

## Common Error Messages and Fixes

| Error                              | Cause                    | Fix                                    |
| ---------------------------------- | ------------------------ | -------------------------------------- |
| `cannot move out of`               | Using moved value        | Use references with `&` or `&mut`      |
| `borrow as mutable more than once` | Multiple mutable borrows | Use one mutable borrow or split scopes |
| `expected found enum Result`       | Forgot `?` operator      | Add `?` or use `match`                 |
| `the trait bound is not satisfied` | Missing trait impl       | Implement trait or use different type  |
| `use of moved value`               | Value was moved          | Use `.clone()` or don't move           |
| `cannot find`                      | Unknown symbol           | Import with `use` or check spelling    |

---

## File Organization Tips

### Module Layout

```
src/uu/module_name/
├── src/
│   ├── main.rs           # pub use module_name::uumain;
│   └── module_name.rs    # pub fn uumain() + pub fn uu_app()
├── Cargo.toml
└── locales/
    └── en/
        └── strings.json
```

### Import Conventions

```rust
use clap::{Arg, ArgAction, Command};      // Argument parsing
use std::fs::File;                        // File I/O
use std::io::{self, BufRead};            // I/O utilities
use std::ffi::{OsStr, OsString};         // OS strings
use uucore::error::UResult;               // Error handling
use uucore::{format_usage, help};        // Utilities
```

---

## Quick Utility Progression for Learning

**Start Here (Very Simple):**

1. `true` / `false` - Just exit codes
2. `echo` - Arguments + output

**Then Study (Medium):** 3. `cat` - File I/O 4. `yes` - Loops + signals 5. `head` / `tail` - Line processing

**Advanced (Complex):** 6. `sort` - Algorithms 7. `grep` - Regex 8. `find` - Recursion

---

## Helpful Resources

- **Compiler errors** - Read them carefully, they're usually helpful
- **Rust Book**: https://doc.rust-lang.org/book/
- **Rust by Example**: https://doc.rust-lang.org/rust-by-example/
- **cargo doc --open**: View your project docs
- **YouTube**: Search for "Rust" + concept you're learning

---

## Common One-Liners

```bash
# Check current directory utilities
ls src/uu/ | wc -l

# Find utilities with specific pattern
grep -r "pattern" src/uu/ --include="*.rs"

# Build all tests
cargo test --all

# Run specific test
cargo test -p uu_echo test_name

# Compare outputs
echo "test" | diff - <(../gnu/src/echo "test")

# Benchmark
time cargo build --release

# View documentation
cargo doc -p uu_echo --open
```

---

Good luck learning Rust! Remember: the compiler is your friend. 🦀
