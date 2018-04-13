---
title: Coding Guide
---

This is yet to be fleshed out.

### Panickers

Panics are a pain; in principle, we write code expecting that they will never occur (or at least, we should!). However, with code churn and different coders working in the same code, it's all too easy to find a panic (often written by someone else) and wonder why the author thought that it could never happen. When such a panic happens at run-time it can be as difficult to track down the reason for failure as for a segmentation fault.

As such, all panickers in our codebase, outside of test code, must by accompanied by formal-ish proof of why they can never happen. These no-panic proofs should be of the form:

```
"<truth> ( [, <truth>] ... ) ( [; so <derivation>] ... ); qed"
```

For example:

```"in `enact_result` once: so `SubState::accrue` called once: so we're tracing: so one item pushed when and only when `SubState::accrue` called: qed."```

When the condition that is being asserted is substituted for the "qed", the resultant proof should be a logical tautology.

These proofs should be used as the string argument for `assert!` and `expect`. They are also required for any panickers including `.first`, `.last`, `unwrap`, `[]` and math ops where underflow/overflow is not obviously impossible; in these cases, use `debug_assert!` prior to the panic site at the earliest point where it is still obviously a perfect guard against the silent panic.

