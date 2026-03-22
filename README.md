# 🦀 LeetCode-Rust_solutions

A collection of LeetCode self solutions written in Rust, focusing on clean code, performance, and detailed explanations of Rust-specific concepts used in each solution.

---

## 📊 Progress Overview

| # | Problem | Difficulty | Time Complexity | Space Complexity | Solution |
|---|---------|------------|-----------------|------------------|----------|
| 1 | [Two Sum](#1-two-sum) | 🟢 Easy | O(n²) | O(1) | [View](#1-two-sum) |
| 13 | [Roman to Integer](#13-roman-to-integer) | 🟢 Easy | O(n) | O(1) | [View](#13-roman-to-integer) |
| 20 | [Valid Parentheses](#20-valid-parentheses) | 🟢 Easy | O(n) | O(n) | [View](#20-valid-parentheses) |
| 21 | [Merge Two Sorted Lists](#21-merge-two-sorted-lists) | 🟢 Easy | O(n + m) | O(1) | [View](#21-merge-two-sorted-lists) |
| 26 | [Remove Duplicates from Sorted Array](#26-remove-duplicates-from-sorted-array) | 🟢 Easy | O(n) | O(1) | [View](#26-remove-duplicates-from-sorted-array) |

---

## 🗂️ Structure

```
leetcode-rust/
├── README.md
├── two_sum.rs
├── roman_to_integer.rs
├── valid_parentheses.rs
├── merge_two_lists.rs
└── remove_duplicates.rs
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

### 20. Valid Parentheses

**Problem:** Given a string `s` containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid. Open brackets must be closed by the same type of brackets and in the correct order.

```rust
impl Solution {
    pub fn is_valid(s: String) -> bool {
        let mut stack: Vec<char> = Vec::new();

        for c in s.chars() {
            match c {
                '(' | '[' | '{' => stack.push(c),
                ')' => if stack.pop() != Some('(') { return false; },
                ']' => if stack.pop() != Some('[') { return false; },
                '}' => if stack.pop() != Some('{') { return false; },
                _ => {}
            }
        }

        stack.is_empty()
    }
}
```

**Key Rust Concepts:**
- `Vec<char>` used as a **Stack** — Rust's `Vec` has built-in `push` and `pop` making it a perfect stack
- `stack.push(c)` — adds an opening bracket to the top of the stack
- `stack.pop()` — removes and returns the last element as `Option<char>`, returns `None` if empty
- `Some('(')` — `pop()` returns an `Option`, so we compare with `Some(value)` not the value directly
- `'(' | '[' | '{'` — Rust's `match` supports multiple patterns with `|` (OR)
- `stack.is_empty()` — at the end, a valid string leaves the stack completely empty
- **Logic:** Push every opening bracket. When a closing bracket appears, pop the stack and verify it matches. Any mismatch means invalid.

**Complexity:**
- ⏱ Time: `O(n)` — single pass through the string
- 💾 Space: `O(n)` — in the worst case all characters are opening brackets and get pushed to the stack

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

### 26. Remove Duplicates from Sorted Array

**Problem:** Given an integer array `nums` sorted in non-decreasing order, remove the duplicates in-place such that each unique element appears only once. Return the number of unique elements `k`.

```rust
impl Solution {
    pub fn remove_duplicates(nums: &mut Vec<i32>) -> i32 {
        let mut k = 1;

        for i in 1..nums.len() {
            if nums[i] != nums[i - 1] {
                nums[k] = nums[i];
                k += 1;
            }
        }

        k as i32
    }
}
```

**Key Rust Concepts:**
- `&mut Vec<i32>` — mutable reference to the vector, required to modify it in-place without taking ownership
- `let mut k = 1` — the write pointer, starts at 1 because the first element is always unique
- `nums[i] != nums[i - 1]` — since the array is sorted, duplicates are always adjacent, so we only need to compare with the previous element
- `nums[k] = nums[i]` — overwrites the duplicate position with the new unique value directly in the same array
- `k as i32` — casts `usize` to `i32` for the return type
- **No extra array needed** — the in-place Two Pointers approach satisfies the `O(1)` space requirement

**Complexity:**
- ⏱ Time: `O(n)` — single pass through the array
- 💾 Space: `O(1)` — in-place, no extra memory used

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
git clone https://github.com/AhmedHawash321/leetcode-rust.git
cd leetcode-rust

# Run a specific solution
rustc two_sum.rs && ./two_sum
```

---

*Solutions are added regularly. Feel free to open an issue or PR if you spot improvements!*
