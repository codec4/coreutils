# 📚 Rust Learning Index - Quick Navigation

This index helps you find exactly what you need as you learn Rust through uutils coreutils.

## 🎯 Quick Start (First Time Here?)

**Start with this file:** `START_HERE.md` (5-10 min read)

It gives you:

- Overview of what you have
- Learning path for different levels
- Essential commands
- First steps to get running

## 📖 Learning Materials

### For Understanding Concepts

**File:** `RUST_LEARNING_GUIDE.md`

Perfect for learning how Rust actually works through real code.

**What it covers:**

- Complete walkthrough of the `echo` utility
- Line-by-line explanation with Rust concepts
- How to build and test
- Key functions explained:
  - Ownership and references (`is_flag`)
  - Pattern matching (the `match` statement)
  - Error handling (the `?` operator)
  - Iterators (`filter_flags`)
  - Structs and implementations

**When to read:** After you've seen the project structure, before doing exercises

**Time:** 45-60 minutes

---

### For Hands-On Learning

**File:** `PRACTICAL_EXERCISES.md`

Contains 10 progressive exercises from beginner to advanced.

**Exercises:**

1. Understanding flag processing (debug output)
2. Adding a new flag (-c for capitalize)
3. Creating unit tests
4. Exploring the iterator pattern
5. Error handling deep dive
6. Understanding the builder pattern
7. Integration testing
8. Comparing with GNU implementations
9. Refactoring for clarity
10. Exploring other utilities

**When to use:** After reading the learning guide, for hands-on practice

**Time:** 30 min to 2 hours per exercise

---

### For Quick Reference

**File:** `QUICK_REFERENCE.md`

Fast lookup for syntax, commands, and patterns.

**What it includes:**

- Essential Rust syntax cheat sheet
- Common uutils patterns
- Testing patterns
- Debugging techniques
- Common error messages and fixes
- File I/O snippets
- One-liner commands

**When to use:** While coding, when you need to remember syntax

**Time:** 5-10 minutes per lookup

---

## 🗂️ Learning Sequence

### Week 1: Foundations

```
1. Read START_HERE.md (understand what you have)
2. Verify setup (cargo build -p uu_echo)
3. Read RUST_LEARNING_GUIDE.md (understand echo)
4. Do PRACTICAL_EXERCISES 1-3
5. Read tests to understand expectations
```

### Week 2: Expansion

```
1. Study cat utility similarly to echo
2. Complete PRACTICAL_EXERCISES 4-6
3. Study other simple utilities (yes, head, tail)
4. Try modifying a simple utility
```

### Week 3+: Mastery

```
1. Study complex utilities (sort, grep, find)
2. Complete remaining exercises
3. Make your first code contribution
4. Help others learn!
```

## 🔍 Finding Specific Topics

### I want to learn about...

**Ownership and References:**

- → RUST_LEARNING_GUIDE.md "The Entry Point" section
- → QUICK_REFERENCE.md "Ownership & Borrowing"

**Pattern Matching:**

- → RUST_LEARNING_GUIDE.md "Key Function: is_flag()"
- → QUICK_REFERENCE.md "Pattern Matching"
- → PRACTICAL_EXERCISES.md Exercise 1

**Error Handling:**

- → RUST_LEARNING_GUIDE.md "Key Function: uumain()"
- → QUICK_REFERENCE.md "Errors with Result"
- → PRACTICAL_EXERCISES.md Exercise 5

**Iterators:**

- → RUST_LEARNING_GUIDE.md "Iterator Processing"
- → QUICK_REFERENCE.md "Iterators"
- → PRACTICAL_EXERCISES.md Exercise 4

**Testing:**

- → PRACTICAL_EXERCISES.md Exercise 3 (Unit Tests)
- → PRACTICAL_EXERCISES.md Exercise 7 (Integration Tests)
- → QUICK_REFERENCE.md "Testing Patterns"

**File I/O:**

- → Study cat utility: `cat src/uu/cat/src/cat.rs`
- → QUICK_REFERENCE.md "Working with Files"
- → PRACTICAL_EXERCISES.md Exercise 6 onwards

**Command-line Arguments:**

- → RUST_LEARNING_GUIDE.md "Argument Parsing with clap"
- → QUICK_REFERENCE.md "Argument Parsing"
- → PRACTICAL_EXERCISES.md Exercise 2, 4

**Debugging:**

- → QUICK_REFERENCE.md "Debugging Techniques"
- → PRACTICAL_EXERCISES.md "Debugging Tips"
- → START_HERE.md "Common Workflows"

## 📁 File Organization

```
coreutils/
├── START_HERE.md              ← Entry point
├── LEARNING_INDEX.md          ← You are here
├── RUST_LEARNING_GUIDE.md     ← Deep dives
├── PRACTICAL_EXERCISES.md     ← Hands-on work
├── QUICK_REFERENCE.md         ← Syntax lookup
│
├── src/uu/
│   ├── echo/                  ← Start here
│   ├── true/false/            ← Simplest
│   ├── cat/                   ← Next
│   ├── head/tail/yes/         ← Medium
│   ├── cut/sort/uniq/         ← More complex
│   ├── grep/find/             ← Advanced
│   └── ... (50+ more)
│
└── util/
    ├── run-gnu-test.sh        ← Test runner
    └── build-gnu.sh           ← Build GNU
```

## 🚀 Running Commands

### Building and Testing

```bash
cd coreutils

# Build a utility
cargo build -p uu_echo

# Test it
cargo test -p uu_echo

# Run it
./target/debug/coreutils echo "hello"

# Test against GNU
bash util/run-gnu-test.sh tests/misc/echo.sh
```

Full command reference: See `QUICK_REFERENCE.md`

## 📚 External Resources

### Official Rust Learning

- **The Rust Book**: https://doc.rust-lang.org/book/
- **Rust by Example**: https://doc.rust-lang.org/rust-by-example/
- **Rustlings**: https://github.com/rust-lang/rustlings (interactive)

### Getting Help

- **Stack Overflow**: Tag `rust`
- **r/rust**: https://reddit.com/r/rust
- **Rust Forum**: https://users.rust-lang.org
- **Official Rust Discord**: https://discord.gg/rust-lang

### Related Projects

- **GNU Coreutils** (C reference): https://github.com/coreutils/coreutils
- **uutils**: https://github.com/uutils/coreutils

## 💡 Learning Tips

1. **Read in Order**
   - START_HERE → LEARNING_GUIDE → EXERCISES → REFERENCE

2. **Build Frequently**
   - After each section, run `cargo build` and `cargo test`
   - This provides immediate feedback

3. **Compare with GNU**
   - Use GNU implementation as reference
   - Verify your understanding with: `bash util/run-gnu-test.sh`

4. **Keep QUICK_REFERENCE.md Open**
   - While coding, use it for quick syntax lookups
   - Don't memorize - learn to find information efficiently

5. **Don't Rush**
   - Rust has a learning curve
   - Understanding is more important than speed
   - Spend time on each concept

6. **Use Compiler Errors**
   - Rust compiler errors are incredibly helpful
   - Read them carefully - they tell you how to fix issues
   - Search errors online for more explanation

## ✅ Progress Checklist

Track your learning progress:

- [ ] Read START_HERE.md
- [ ] Verify setup (cargo build, cargo test)
- [ ] Read RUST_LEARNING_GUIDE.md
- [ ] Complete PRACTICAL_EXERCISES 1-3
- [ ] Study cat utility
- [ ] Complete PRACTICAL_EXERCISES 4-6
- [ ] Study 3 more utilities
- [ ] Complete remaining exercises
- [ ] Make a code change
- [ ] Run GNU tests on your change
- [ ] Contribute to the project!

## 🆘 Stuck? Here's How to Debug

### If you get a compiler error:

1. Read the error message completely
2. Search the error on Stack Overflow
3. Check `QUICK_REFERENCE.md` for the pattern
4. Look at similar code in the project

### If you don't understand something:

1. Reread the relevant section
2. Look for examples in the codebase
3. Check The Rust Book
4. Add debug output with `eprintln!()`
5. Ask on r/rust or forums

### If tests fail:

1. Run with `--nocapture`: `cargo test -- --nocapture`
2. Add debug output: `eprintln!("{:?}", variable);`
3. Compare with GNU: `bash util/run-gnu-test.sh`
4. Build a minimal test case

## 🎓 What You'll Learn

By following this guide, you'll understand:

✓ Rust ownership and borrowing
✓ Pattern matching and error handling
✓ Iterators and functional programming
✓ Structs, traits, and implementations
✓ File I/O and system programming
✓ Testing strategies
✓ Real-world Rust code patterns
✓ How to read and modify open source

## 📞 Questions?

- **General Rust**: Check `QUICK_REFERENCE.md` first
- **Concepts**: See `RUST_LEARNING_GUIDE.md`
- **Practice**: Use `PRACTICAL_EXERCISES.md`
- **Commands**: See START_HERE.md and QUICK_REFERENCE.md

---

**Ready to start?** → Open `START_HERE.md`

**Want to dive into code?** → Open `RUST_LEARNING_GUIDE.md`

**Ready for exercises?** → Open `PRACTICAL_EXERCISES.md`

Good luck! 🦀
