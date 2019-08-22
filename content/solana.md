class: center
name: title
count: false

.TIL[rustc: what's next?]

.me[.grey[*by* **Nicholas Matsakis**]]
.citation[`https://github.com/nikomatsakis/rustconf2019-solana`]

---

# time of transformation

- Technical
- Social
- Learnability

---

# technical

---

# we've made a lot of investments

- MIR
- MIR-based borrow checker (NLL)
- demand driven compilation and incremental

---

# demand driven compilation

- traditional compiler model:
    - parse
    - type-check
    - borrow check
    - generate LLVM IR
    - optimize
    - emit binary
    
---

# demand driven compilation

- demand driven model:
    - start with a goal and work backwards
    
```rust
// a.rs
fn foo() {
    bar();
}

// b.rs
fn bar() {
}

// c.rs
fn baz() {
}
```

???

- instead of parsing all three files at once
- then type-checking all the functions
- then borrow checking all the functions
- then generating code for all the functions
- we might decide to just compile `foo`,
  - which leads us to type-check and parse just what we need

---

# demand driven helps with incremental

```rust
fn foo() {
    bar();
}
```

- compile(`foo`)
    - type-check(`foo`)
        - signature(`bar`)
    - generate-llvm-ir(`foo`)
        - symbol-name(`bar`)
    - optimize

???

- here is how that might look
- we started down this path partly for incremental support
- now if the source for `bar` has changed, 
  - but the result of signature has not,
  - then the type check of `foo` can be re-used

---

# but demand driven can do much more

- parallel compilation
    - type-check multiple functions in parallel
    - Zoxc laid a lot of the groundwork here
    - currently `wg-parallel-rustc` is working on pushing this over the finish line
    - tracking issue: [#48685]
    
[#48685]: https://github.com/rust-lang/rust/issues/48685
    
---

# speaking of parallel compilation

- pipelined compilation
    - should be coming to stable soon
    - for large crate graphs, can permit more parallelism -- sometimes
      as much as a 25% win on overall build time

---

# potential for demand driven

- rustc is still not fully demand driven
- better incremental compilation time
- top notch IDE integration
    - rust-analyzer: forward vanguard to explore IDE applications
- compilation time:
    - multi-crate compilation with selective compilation

---

# leveraging MIR for compilation time wins

- LLVM is a big factor in compilation time
- MIR-based optimization can help
- "MIR only rlibs"

---

# other potential compilation time wins

- cranelift backend (debug builds only)
- "polymorphization" -- can we share code between instantiations?

---

# Social

---

# working groups

- team of folks working towards a common goal
- great way to help integrate newcomers
- also to help us track what we are doing

---

# getting organized

- compiler team web site
    - [rust-lang.github.io/compiler-team](https://rust-lang.github.io/compiler-team/)
- lists active working groups
    - meeting times, goals, and more

---

# design meetings

- an effort to be document our designs better
- still a work in progress =)

---

# a challenge: triage and maintenance

- fighting bugs is hard
- we sometimes have trouble keeping up
- looking for ways to help us stay organized better
- or to keep focus

---

# Learnability

---

- rustc-guide
    - document rustc systematically
    - the learning wg 
    - [read it here!](https://rust-lang.github.io/rustc-guide/)

---

- libraries
    - create subcomponents with strong boundaries
    - examples:
        - miri and const evaluation
        - measureme
        - error reporting
        - polonius, chalk, name resolution

---

# Conclusion

- Lots of exciting stuff happening in rustc
    - All credit goes to the [compiler team members and contributors][c] <3
- Never been a better time to get involved

[c]: https://www.rust-lang.org/governance/teams/compiler
