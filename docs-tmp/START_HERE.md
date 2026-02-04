# 🦀 Start Here: Learning Rust with uutils Coreutils

Welcome! This guide will help you get started learning Rust through the uutils coreutils project. You've got everything set up, so let's begin your Rust journey!

## What You Have

✅ **uutils coreutils** - A Rust reimplementation of GNU coreutils
✅ **GNU coreutils** - The original C implementation (for testing)
✅ **GNU test suite** - Comprehensive tests to verify compatibility
✅ **All build tools** - autoconf, automake, m4, bison, texinfo, autopoint, gperf

## Your Learning Path

### Phase 1: Understand the Project (1-2 hours)

Start by understanding what you're working with:

1. **Read the README:**

   ```bash
   cat README.md | head -100
   ```

2. **Explore the project structure:**

   ```bash
   ls -la src/uu/
   ```

3. **Look at the simplest utility:**

   ```bash
   cat src/uu/true/src/true.rs
   cat src/uu/false/src/false.rs
   ```

4. **Build something:**
   ```bash
   cargo build -p uu_echo
   ./target/debug/coreutils echo "Hello, Rust!"
   ```

### Phase 2: Deep Dive into Echo (3-5 hours)

The `echo` command is perfect for learning because it's simple yet demonstrates important Rust concepts.

1. **Read the learning guide:**

   ```bash
   less RUST_LEARNING_GUIDE.md
   ```

2. **Study the code:**

   ```bash
   cat src/uu/echo/src/echo.rs
   ```

3. **Trace through execution:**
   - Run: `./target/debug/coreutils echo -n "hello"`
   - Add debug output: `eprintln!("DEBUG: {:?}", options);`
   - Rebuild and observe
   - Understand the flow

4. **Run the tests:**
   ```bash
   cargo test -p uu_echo
   bash util/run-gnu-test.sh tests/misc/echo.sh
   ```

### Phase 3: Complete Practical Exercises (2-3 hours per exercise)

Work through the exercises to apply what you've learned:

1. **Read the exercises:**

   ```bash
   less PRACTICAL_EXERCISES.md
   ```

2. **Start with Exercise 1:**
   - Add debug output to understand flag processing
   - Build, test, understand

3. **Progress through exercises:**
   - Exercise 2: Add a new flag
   - Exercise 3: Write unit tests
   - Exercise 4: Explore iterators
   - And more!

### Phase 4: Study Other Utilities (Ongoing)

Once you understand echo, expand your knowledge:

1. **Simple utilities** (1-2 hours each):
   - `src/uu/cat/src/cat.rs` - File I/O
   - `src/uu/yes/src/yes.rs` - Infinite loops
   - `src/uu/head/src/head.rs` - Line processing

2. **Medium utilities** (3-5 hours each):
   - `src/uu/cut/src/cut.rs` - String manipulation
   - `src/uu/sort/src/sort.rs` - Algorithms
   - `src/uu/uniq/src/uniq.rs` - Deduplication

3. **Complex utilities** (5+ hours each):
   - `src/uu/grep/src/grep.rs` - Regular expressions
   - `src/uu/find/src/find.rs` - Recursion
   - `src/uu/ls/src/ls.rs` - Complex output formatting

## Essential Commands Reference

Keep these commands handy (full reference in `QUICK_REFERENCE.md`):

```bash
# Build a utility
cargo build -p uu_echo

# Test a utility
cargo test -p uu_echo

# Test against GNU
bash util/run-gnu-test.sh tests/misc/echo.sh

# Run it
./target/debug/coreutils echo "test"

# Check code quality
cargo clippy -p uu_echo

# Format code
cargo fmt

# View docs
cargo doc --open
```

## Key Concepts You'll Learn

### In Echo:

- ✅ Ownership and references
- ✅ Pattern matching with `match`
- ✅ The `?` operator for error handling
- ✅ Iterators and functional programming
- ✅ Structs and implementations
- ✅ String types (String, &str, OsString, OsStr)
- ✅ I/O operations (stdout)
- ✅ Command-line argument parsing with clap

### In Cat:

- ✅ File I/O operations
- ✅ Error handling for file operations
- ✅ Buffered reading and writing
- ✅ Working with multiple files

### In Sort:

- ✅ Algorithms and comparisons
- ✅ Collections (Vec, HashMap)
- ✅ Custom sorting logic
- ✅ Memory efficiency

### In Grep:

- ✅ Regular expressions
- ✅ Pattern matching in files
- ✅ Complex control flow
- ✅ Performance optimization

## Learning Tips

### 1. **Read Tests to Understand Expected Behavior**

```bash
# See what a utility should do
cat tests/by-util/test_echo.rs

# See what GNU expects
cat ../gnu/tests/misc/echo.sh
```

### 2. **Use Debug Output Liberally**

```rust
eprintln!("DEBUG: variable = {:?}", variable);
```

### 3. **Compare with GNU Implementations**

```bash
# What does GNU do?
../gnu/src/echo -n "test"

# What does uutils do?
./target/debug/coreutils echo -n "test"

# Are they the same?
diff <(../gnu/src/echo -n "test") <(./target/debug/coreutils echo -n "test")
```

### 4. **Build Incrementally**

- Don't try to understand the whole file at once
- Focus on one function at a time
- Build and test frequently
- Use `cargo check` for quick feedback

### 5. **Read Error Messages Carefully**

Rust compiler errors are very helpful and descriptive. They usually tell you exactly what's wrong and how to fix it.

### 6. **Use `cargo clippy` for Idioms**

```bash
cargo clippy -p uu_echo
```

This tool suggests more idiomatic Rust patterns.

## Common Workflows

### Workflow 1: Learning a New Utility

```bash
# 1. Build it
cargo build -p uu_utility

# 2. Read the source
cat src/uu/utility/src/utility.rs | less

# 3. Test it manually
./target/debug/coreutils utility --help
./target/debug/coreutils utility some args

# 4. Run the tests
cargo test -p uu_utility

# 5. Run GNU tests
bash util/run-gnu-test.sh tests/misc/utility.sh

# 6. Compare with GNU
../gnu/src/utility args
./target/debug/coreutils utility args
```

### Workflow 2: Making a Change

```bash
# 1. Edit the source
vim src/uu/utility/src/utility.rs

# 2. Check for errors
cargo check -p uu_utility

# 3. Build
cargo build -p uu_utility

# 4. Test locally
./target/debug/coreutils utility test args

# 5. Run tests
cargo test -p uu_utility

# 6. Verify with GNU tests
bash util/run-gnu-test.sh tests/misc/utility.sh
```

### Workflow 3: Debugging an Issue

```bash
# 1. Add debug output
eprintln!("DEBUG: {:?}", variable);

# 2. Rebuild
cargo build -p uu_utility

# 3. Run to see debug output
./target/debug/coreutils utility args 2>&1 | grep DEBUG

# 4. Understand the issue
# (adjust code as needed)

# 5. Remove debug output
# (clean up before committing)

# 6. Verify fix
cargo test -p uu_utility
bash util/run-gnu-test.sh tests/misc/utility.sh
```

## Project Organization

```
coreutils/
├── START_HERE.md                    ← You are here
├── RUST_LEARNING_GUIDE.md           ← Deep dive into echo
├── PRACTICAL_EXERCISES.md           ← Hands-on exercises
├── QUICK_REFERENCE.md               ← Command reference
├── README.md                        ← Project info
├── DEVELOPMENT.md                   ← Contributing guide
├── CONTRIBUTING.md                  ← Code of conduct
│
├── src/
│   ├── uu/                          # Individual utilities
│   │   ├── echo/
│   │   │   ├── src/
│   │   │   │   ├── main.rs
│   │   │   │   └── echo.rs
│   │   │   └── Cargo.toml
│   │   ├── cat/
│   │   ├── ls/
│   │   └── ... (50+ more utilities)
│   │
│   ├── uucore/                      # Shared library
│   │   └── src/
│   │       ├── lib.rs
│   │       ├── error.rs
│   │       └── ... (utilities)
│   │
│   └── bin/                         # Multicall binary
│
├── tests/
│   └── by-util/                     # Integration tests
│       └── test_echo.rs
│
├── Cargo.toml                       # Workspace manifest
└── util/
    ├── run-gnu-test.sh              # Test runner
    └── build-gnu.sh                 # Build GNU
```

## Getting Help

### When You're Stuck:

1. **Read the error message** - Rust errors are very descriptive
2. **Search Google** - "rust error E0308" or similar
3. **Check the Rust Book** - https://doc.rust-lang.org/book/
4. **Use `cargo doc --open`** - View library documentation
5. **Look at similar code** - Find a utility that does something similar
6. **Ask for help** - r/rust, Stack Overflow, GitHub issues

### Useful Resources:

- **Official Rust Book**: https://doc.rust-lang.org/book/
- **Rust by Example**: https://doc.rust-lang.org/rust-by-example/
- **Rustlings**: Interactive Rust lessons - https://github.com/rust-lang/rustlings
- **Cargo Book**: https://doc.rust-lang.org/cargo/
- **std library docs**: https://doc.rust-lang.org/std/

## Your First Steps (Right Now!)

1. **Read this file completely** (you're doing it!)

2. **Verify your setup works:**

   ```bash
   cargo build -p uu_echo
   ./target/debug/coreutils echo "It works!"
   cargo test -p uu_echo
   bash util/run-gnu-test.sh tests/misc/echo.sh
   ```

3. **Start with the simplest utility:**

   ```bash
   cat src/uu/true/src/true.rs
   # This is only a few lines! Understand this first.
   ```

4. **Then read the echo guide:**

   ```bash
   less RUST_LEARNING_GUIDE.md
   ```

5. **Complete Exercise 1 from the practical exercises:**
   ```bash
   less PRACTICAL_EXERCISES.md
   # Start with Exercise 1: Understanding Echo's Behavior
   ```

## Progress Tracker

Use this to track your learning:

- [ ] Understand the project structure
- [ ] Build and run echo successfully
- [ ] Pass all echo tests
- [ ] Understand true/false utilities
- [ ] Read the echo learning guide
- [ ] Complete Practical Exercise 1
- [ ] Complete Practical Exercise 2
- [ ] Study cat utility
- [ ] Complete remaining exercises
- [ ] Study yes/head/tail utilities
- [ ] Study cut/sort/uniq utilities
- [ ] Study grep utility
- [ ] Make your first code contribution

## What to Expect

### Week 1:

- Learn the project structure
- Understand echo completely
- Get comfortable with Rust syntax
- Build confidence with cargo

### Week 2-3:

- Study multiple utilities
- Write your own code changes
- Debug problems independently
- Understand Rust idioms

### Week 4+:

- Make real contributions
- Help others learn
- Explore advanced topics
- Build your own projects

## Remember

- **Rust is hard, but worth it** - Be patient with the learning curve
- **Compiler errors are friends** - They help you write correct code
- **Build incrementally** - Small steps lead to big understanding
- **Test everything** - The GNU test suite is your friend
- **Have fun!** - You're learning one of the most powerful languages

---

## Next Steps

1. **Right now**: Read this file completely ✓
2. **Next 5 minutes**: Verify setup with `cargo build -p uu_echo`
3. **Next 30 minutes**: Read `RUST_LEARNING_GUIDE.md`
4. **Next 1-2 hours**: Complete `PRACTICAL_EXERCISES.md` Exercise 1-2
5. **Ongoing**: Follow the learning path above

Good luck! You're about to learn a powerful skill. 🦀

---

**Questions?** Check `QUICK_REFERENCE.md` for common commands and concepts.

**Want more detail?** See `RUST_LEARNING_GUIDE.md` for an in-depth walkthrough of echo.

**Ready to practice?** Jump into `PRACTICAL_EXERCISES.md` for hands-on learning.
