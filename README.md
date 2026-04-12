# 🦀 LeetCode-Rust_solutions
A collection of LeetCode self solutions written in Rust, focusing on clean code, performance, and detailed explanations of Rust-specific concepts used in each solution.

## 📊 Progress Overview
| # | Problem | Difficulty | Time Complexity | Space Complexity | Solution |
|---|---------|------------|-----------------|------------------|----------|
| 1 | Two Sum | 🟢 Easy | O(n²) | O(1) | [View](#1-two-sum) |
| 13 | Roman to Integer | 🟢 Easy | O(n) | O(1) | [View](#13-roman-to-integer) |
| 20 | Valid Parentheses | 🟢 Easy | O(n) | O(n) | [View](#20-valid-parentheses) |
| 21 | Merge Two Sorted Lists | 🟢 Easy | O(n + m) | O(1) | [View](#21-merge-two-sorted-lists) |
| 26 | Remove Duplicates from Sorted Array | 🟢 Easy | O(n) | O(1) | [View](#26-remove-duplicates-from-sorted-array) |
| 27 | Remove Element | 🟢 Easy | O(n) | O(1) | [View](#27-remove-element) |
| 35 | Search Insert Position | 🟢 Easy | O(log n) | O(1) | [View](#35-search-insert-position) |
| 58 | Length of Last Word | 🟢 Easy | O(n) | O(1) | [View](#58-length-of-last-word) |
| 66 | Plus One | 🟢 Easy | O(n) | O(1) | [View](#66-plus-one) |
| 100 | Same Tree | 🟢 Easy | O(n) | O(h) | [View](#100-same-tree) |

## 🗂️ Structure
```
leetcode-rust/
├── README.md
├── two_sum.rs
├── roman_to_integer.rs
├── valid_parentheses.rs
├── merge_two_lists.rs
├── remove_duplicates.rs
├── remove_element.rs
├── search_insert.rs
├── length_of_last_word.rs
├── plus_one.rs
└── same_tree.rs
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
- ⏱ Time: O(n²) — nested loops, each pair is checked once
- 💾 Space: O(1) — no extra data structures used

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
- `.map_or(0, |&c| ...)` — handles the `Option` returned by `peek()`: returns `0` if `None`, or applies the closure if `Some`
- `|&c|` — pattern destructures the reference since `peek()` yields a reference to the next item
- `match` — Rust's powerful pattern matching, used here as a lookup table for Roman numeral values
- **Logic:** If the current value is less than the next (e.g., IV), subtract it. Otherwise, add it (e.g., VI)

**Complexity:**
- ⏱ Time: O(n) — single pass through the string
- 💾 Space: O(1) — only a few integer variables used

---

### 20. Valid Parentheses
**Problem:** Given a string `s` containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

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
- `Vec<char>` used as a Stack — Rust's `Vec` has built-in `push` and `pop` making it a perfect stack
- `stack.push(c)` — adds an opening bracket to the top of the stack
- `stack.pop()` — removes and returns the last element as `Option<char>`, returns `None` if empty
- `Some('(')` — `pop()` returns an `Option`, so we compare with `Some(value)` not the value directly
- `'(' | '[' | '{'` — Rust's `match` supports multiple patterns with `|` (OR)
- `stack.is_empty()` — at the end, a valid string leaves the stack completely empty

**Complexity:**
- ⏱ Time: O(n) — single pass through the string
- 💾 Space: O(n) — in the worst case all characters are opening brackets

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
- `mut` on parameters — Rust requires explicit `mut` to allow mutation
- `.is_some()` — safely checks if the `Option` contains a value before unwrapping
- `.as_ref()` — borrows the inner value without taking ownership
- `.take()` — moves the value out of the `Option`, replacing it with `None` (avoids double-move errors)
- `dummy.next` without `;` — implicit return of the final merged list

**Complexity:**
- ⏱ Time: O(n + m) — each node from both lists is visited once
- 💾 Space: O(1) — no new nodes are created, only pointers are rearranged

---

### 26. Remove Duplicates from Sorted Array
**Problem:** Given a sorted array, remove duplicates in-place so each unique element appears only once. Return the count of unique elements.

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
- `&mut Vec<i32>` — mutable reference, required to modify in-place without taking ownership
- `let mut k = 1` — the write pointer, starts at 1 because the first element is always unique
- `nums[i] != nums[i - 1]` — since the array is sorted, duplicates are always adjacent
- `nums[k] = nums[i]` — overwrites the duplicate position with the new unique value
- `k as i32` — casts `usize` to `i32` for the return type

**Complexity:**
- ⏱ Time: O(n) — single pass through the array
- 💾 Space: O(1) — in-place, no extra memory used

---

### 27. Remove Element
**Problem:** Given an array `nums` and a value `val`, remove all occurrences of `val` in-place. Return the number of elements that are not equal to `val`.

```rust
impl Solution {
    pub fn remove_element(nums: &mut Vec<i32>, val: i32) -> i32 {
        let mut k = 0;
        for i in 0..nums.len() {
            if nums[i] != val {
                nums[k] = nums[i];
                k += 1;
            }
        }
        k as i32
    }
}
```

**Key Rust Concepts:**
- `&mut Vec<i32>` — mutable reference to modify the array in-place without taking ownership
- `let mut k = 0` — the write pointer, starts at `0` because no element is guaranteed to be valid upfront
- `nums[i] != val` — only copy elements that are NOT equal to the target value
- `nums[k] = nums[i]` — overwrites the current write position with the valid element, shifting elements left
- `k as i32` — explicit cast from `usize` to `i32` required by the return type
- **Two Pointers pattern** — `i` scans forward, `k` tracks the next valid write position

**Complexity:**
- ⏱ Time: O(n) — single pass through the array
- 💾 Space: O(1) — in-place modification, no extra memory used

---

### 35. Search Insert Position
**Problem:** Given a sorted array of distinct integers and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order. Must run in `O(log n)` time.

```rust
impl Solution {
    pub fn search_insert(nums: Vec<i32>, target: i32) -> i32 {
        let mut left = 0;
        let mut right = nums.len() as i32 - 1;

        while left <= right {
            let mid = left + (right - left) / 2;
            if nums[mid as usize] == target {
                return mid;
            } else if nums[mid as usize] < target {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        left
    }
}
```

**Key Rust Concepts:**
- `nums.len() as i32 - 1` — `len()` returns `usize`, cast to `i32` so we can safely go negative when the array is empty
- `left + (right - left) / 2` — safer way to calculate mid, avoids integer overflow compared to `(left + right) / 2`
- `mid as usize` — array indexing in Rust requires `usize`, so we cast back when accessing elements
- `while left <= right` — standard Binary Search loop condition
- **Binary Search logic:** Found target → return `mid`. Target bigger → `left = mid + 1`. Target smaller → `right = mid - 1`
- `left` at the end — when the target is not found, `left` naturally lands on the correct insert position

**Complexity:**
- ⏱ Time: O(log n) — search space halves with every iteration
- 💾 Space: O(1) — only a few pointer variables used

---

### 58. Length of Last Word
**Problem:** Given a string `s` consisting of words and spaces, return the length of the last word in the string.

```rust
impl Solution {
    pub fn length_of_last_word(s: String) -> i32 {
        s.trim_end()
            .split_whitespace()
            .last()
            .map(|w| w.len())
            .unwrap_or(0) as i32
    }
}
```

**Key Rust Concepts:**
- `.trim_end()` — removes trailing whitespace so a trailing space doesn't produce an empty last word
- `.split_whitespace()` — splits by any whitespace and returns an iterator, automatically ignoring multiple spaces
- `.last()` — consumes the iterator and returns the last element as `Option<&str>`
- `.map(|w| w.len())` — if `Some(word)` exists, applies the closure to get its length
- `.unwrap_or(0)` — safely unwraps the `Option`, returning `0` if no word was found instead of panicking
- `as i32` — casts `usize` (returned by `.len()`) to `i32` for the return type

**Complexity:**
- ⏱ Time: O(n) — the string is traversed once by the iterator chain
- 💾 Space: O(1) — no extra allocations, iterator is lazy

---

### 66. Plus One
**Problem:** You are given a large integer represented as an array `digits`. Increment the large integer by one and return the resulting array of digits.

```rust
impl Solution {
    pub fn plus_one(mut digits: Vec<i32>) -> Vec<i32> {
        let n = digits.len();
        for i in (0..n).rev() {
            if digits[i] < 9 {
                digits[i] += 1;
                return digits;
            }
            digits[i] = 0;
        }
        digits.insert(0, 1);
        digits
    }
}
```

**Key Rust Concepts:**
- `mut digits: Vec<i32>` — takes ownership of the vector and makes it mutable directly in the parameter, no need for `&mut`
- `(0..n).rev()` — iterates from the last index to `0`, since we always start adding from the rightmost digit
- `digits[i] < 9` — if the digit is not `9`, simply increment and return immediately, no carry needed
- `digits[i] = 0` — if the digit is `9`, it becomes `0` and the carry propagates to the left
- `digits.insert(0, 1)` — only reached when all digits were `9` (e.g., `[9,9,9]` → `[1,0,0,0]`), inserts `1` at the front
- **Logic:** Walk from right to left. If no carry, return early. If all digits carry over, prepend `1`

**Complexity:**
- ⏱ Time: O(n) — at most one full pass through the array
- 💾 Space: O(1) — in-place for most cases, O(n) only when all digits are `9`

---

### 100. Same Tree
**Problem:** Given the roots of two binary trees `p` and `q`, write a function to check if they are the same or not. Two binary trees are considered the same if they are structurally identical, and the nodes have the same value.

```rust
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn is_same_tree(
        p: Option<Rc<RefCell<TreeNode>>>,
        q: Option<Rc<RefCell<TreeNode>>>,
    ) -> bool {
        match (p, q) {
            (None, None) => true,
            (Some(node1), Some(node2)) => {
                let n1 = node1.borrow();
                let n2 = node2.borrow();

                n1.val == n2.val &&
                Self::is_same_tree(n1.left.clone(), n2.left.clone()) &&
                Self::is_same_tree(n1.right.clone(), n2.right.clone())
            },
            _ => false,
        }
    }
}
```

**Key Rust Concepts:**
- `Rc<RefCell<TreeNode>>` — LeetCode's standard tree node type in Rust. `Rc` enables shared ownership, `RefCell` enables interior mutability (runtime borrow checking)
- `match (p, q)` — pattern matches on both nodes simultaneously, covering all 3 cases cleanly
- `(None, None) => true` — both nodes are absent, trees match at this position ✅
- `_ => false` — one is `Some` and the other is `None`, structural mismatch ❌
- `.borrow()` — `RefCell`'s method to get a shared read reference to the inner value at runtime
- `.clone()` — required to pass child nodes into recursive calls since `Rc` clone is cheap (just increments reference count)
- **Recursion** — checks current node values, then recursively verifies left and right subtrees

**Complexity:**
- ⏱ Time: O(n) — every node in both trees is visited once
- 💾 Space: O(h) — call stack depth equals tree height `h`. O(log n) for balanced trees, O(n) worst case for skewed trees

---

## 🧠 Why Rust for LeetCode?
Rust is an unusual but powerful choice for competitive problem solving:

- ⚡ **Performance** — comparable to C/C++ with zero-cost abstractions
- 🔒 **Memory Safety** — the borrow checker prevents bugs at compile time
- 🦾 **Strong Type System** — forces you to think clearly about data and ownership
- 📚 **Great for Learning** — solving LeetCode in Rust deepens your understanding of systems programming concepts

## 🚀 How to Run
```bash
# Clone the repo
git clone https://github.com/AhmedHawash321/leetcode-rust.git
cd leetcode-rust

# Run a specific solution
rustc two_sum.rs && ./two_sum
```

*Solutions are added regularly. Feel free to open an issue or PR if you spot improvements!*
