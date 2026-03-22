# 🦀 LeetCode-Rust_solutions

A collection of LeetCode self solutions written in Rust, focusing on clean code, performance, and detailed explanations of Rust-specific concepts used in each solution.

---

## 📊 Progress Overview

| # | Problem | Difficulty | Time Complexity | Space Complexity | Solution |
|---|---------|------------|-----------------|------------------|----------|
| 1 | [Two Sum](#1-two-sum) | 🟢 Easy | O(n²) | O(1) | [View](#1-two-sum) |
| 13 | [Roman to Integer](#13-roman-to-integer) | 🟢 Easy | O(n) | O(1) | [View](#13-roman-to-integer) |
| 21 | [Merge Two Sorted Lists](#21-merge-two-sorted-lists) | 🟢 Easy | O(n + m) | O(1) | [View](#21-merge-two-sorted-lists) |

---

## 🗂️ Structure

```
leetcode-rust/
├── README.md
├── two_sum.rs
├── roman_to_integer.rs
└── merge_two_lists.rs
```

---

## Solutions

### 1. Two Sum

**Problem:** Given an array of integers `nums` and an integer `target`, return indices of the two numbers that add up to `target`.

```rust
impl Solution {
    pub fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {
        for i in 0..nums.len() {
            for j in (i + 1)..nums.len() {
                if nums[i] + nums[j] == target {
                    return vec![i as i32, j as i32];
                }
            }
        }
        vec![]
    }
}
```

**Key Rust Concepts:**
- `Vec<i32>` — used instead of a fixed array because the input size is unknown at compile time
- `0..nums.len()` — Rust range syntax, equivalent to `i < nums.len()` in other languages
- `j` starts from `i + 1` to avoid comparing an element with itself
- `as i32` — explicit type cast required because loop indices are `usize` in Rust, but the return type is `Vec<i32>`

**Complexity:**
- ⏱ Time: `O(n²)` — nested loops, each pair is checked once
- 💾 Space: `O(1)` — no extra data structures used

---

### 13. Roman to Integer

**Problem:** Convert a Roman numeral string to an integer.

```rust
impl Solution {
    pub fn roman_to_int(s: String) -> i32 {
        let mut result = 0;
        let mut chars = s.chars().peekable();

        while let Some(curr_char) = chars.next() {
            let curr = Self::roman_char_to_int(curr_char);
            let next_val = chars.peek().map_or(0, |&c| Self::roman_char_to_int(c));

            if curr < next_val {
                result -= curr;
            } else {
                result += curr;
            }
        }

        result
    }

    fn roman_char_to_int(c: char) -> i32 {
        match c {
            'I' => 1,
            'V' => 5,
            'X' => 10,
            'L' => 50,
            'C' => 100,
            'D' => 500,
            'M' => 1000,
            _ => 0,
        }
    }
}
```

**Key Rust Concepts:**
- `.peekable()` — an Iterator Adaptor that allows looking at the next element without consuming it
- `.peek()` — returns `Option<&&char>`, used here to check the next character ahead
- `.map_or(0, |&c| ...)` — handles the `Option` returned by `peek()`: returns `0` if `None` (end of string), or applies the closure if `Some`
- `|&c|` — pattern destructures the reference since `peek()` yields a reference to the next item
- `match` — Rust's powerful pattern matching, used here as a lookup table for Roman numeral values
- **Logic:** If the current value is less than the next (e.g., `IV`), subtract it. Otherwise, add it (e.g., `VI`)

**Complexity:**
- ⏱ Time: `O(n)` — single pass through the string
- 💾 Space: `O(1)` — only a few integer variables used

---

### 21. Merge Two Sorted Lists

**Problem:** Merge two sorted linked lists and return the merged list sorted.

```rust
impl Solution {
    pub fn merge_two_lists(
        mut list1: Option<Box<ListNode>>,
        mut list2: Option<Box<ListNode>>,
    ) -> Option<Box<ListNode>> {
        let mut dummy = ListNode::new(0);
        let mut current = &mut dummy;

        while list1.is_some() && list2.is_some() {
            if list1.as_ref().unwrap().val < list2.as_ref().unwrap().val {
                let next = list1.as_mut().unwrap().next.take();
                current.next = list1;
                list1 = next;
            } else {
                let next = list2.as_mut().unwrap().next.take();
                current.next = list2;
                list2 = next;
            }
            current = current.next.as_mut().unwrap();
        }

        current.next = if list1.is_some() { list1 } else { list2 };
        dummy.next
    }
}
```

**Key Rust Concepts:**
- `Option<Box<ListNode>>` — linked list nodes in Rust are heap-allocated (`Box`) and nullable (`Option`)
- `mut` on parameters and variables — Rust requires explicit `mut` to allow mutation
- `.is_some()` — safely checks if the `Option` contains a value before unwrapping
- `.as_ref()` — borrows the inner value without taking ownership, required before calling `.unwrap()`
- `.as_mut()` — mutable borrow of the inner value
- `.take()` — moves the value out of the `Option`, replacing it with `None` (avoids double-move errors)
- `current = current.next.as_mut().unwrap()` — advances the pointer while keeping ownership of previous nodes intact
- `dummy.next` without `;` — implicit return of the final merged list

**Complexity:**
- ⏱ Time: `O(n + m)` — each node from both lists is visited once
- 💾 Space: `O(1)` — no new nodes are created, only pointers are rearranged

---

## 🧠 Why Rust for LeetCode?

Rust is an unusual but powerful choice for competitive problem solving:

- ⚡ **Performance** — comparable to C/C++ with zero-cost abstractions
- 🔒 **Memory Safety** — the borrow checker prevents bugs at compile time
- 🦾 **Strong Type System** — forces you to think clearly about data and ownership
- 📚 **Great for Learning** — solving LeetCode in Rust deepens your understanding of systems programming concepts

---

## 🚀 How to Run

```bash
# Clone the repo
git clone https://github.com/ahmedhawash321/leetcode-rust.git
cd leetcode-rust

# Run a specific solution
rustc two_sum.rs && ./two_sum
```

---

*Solutions are added regularly. Feel free to open an issue or PR if you spot improvements!*
