# 🦀 LeetCode-Rust_solutions
A collection of LeetCode self solutions written in Rust, focusing on clean code, performance, and detailed explanations of Rust-specific concepts used in each solution.

## 📊 Progress Overview
| # | Problem | Difficulty | Time Complexity | Space Complexity | Solution |
|---|---------|------------|-----------------|------------------|----------|
| 1 | Two Sum | 🟢 Easy | O(n²) | O(1) | [View](#1-two-sum) |
| 13 | Roman to Integer | 🟢 Easy | O(n) | O(1) | [View](#13-roman-to-integer) |
| 14 | Longest Common Prefix | 🟢 Easy | O(n·m) | O(1) | [View](#14-longest-common-prefix) |
| 20 | Valid Parentheses | 🟢 Easy | O(n) | O(n) | [View](#20-valid-parentheses) |
| 21 | Merge Two Sorted Lists | 🟢 Easy | O(n + m) | O(1) | [View](#21-merge-two-sorted-lists) |
| 26 | Remove Duplicates from Sorted Array | 🟢 Easy | O(n) | O(1) | [View](#26-remove-duplicates-from-sorted-array) |
| 27 | Remove Element | 🟢 Easy | O(n) | O(1) | [View](#27-remove-element) |
| 28 | Find the Index of the First Occurrence in a String | 🟢 Easy | O(n·m) | O(1) | [View](#28-find-the-index-of-the-first-occurrence-in-a-string) |
| 35 | Search Insert Position | 🟢 Easy | O(log n) | O(1) | [View](#35-search-insert-position) |
| 58 | Length of Last Word | 🟢 Easy | O(n) | O(1) | [View](#58-length-of-last-word) |
| 66 | Plus One | 🟢 Easy | O(n) | O(1) | [View](#66-plus-one) |
| 70 | Climbing Stairs | 🟢 Easy | O(n) | O(1) | [View](#70-climbing-stairs) |
| 83 | Remove Duplicates from Sorted List | 🟢 Easy | O(n) | O(1) | [View](#83-remove-duplicates-from-sorted-list) |
| 94 | Binary Tree Inorder Traversal | 🟢 Easy | O(n) | O(h) | [View](#94-binary-tree-inorder-traversal) |
| 100 | Same Tree | 🟢 Easy | O(n) | O(h) | [View](#100-same-tree) |
| 136 | Single Number | 🟢 Easy | O(n) | O(1) | [View](#136-single-number) |
| 168 | Excel Sheet Column Title | 🟢 Easy | O(log n) | O(log n) | [View](#168-excel-sheet-column-title) |

## 🗂️ Structure
```
leetcode-rust/
├── README.md
├── two_sum.rs
├── roman_to_integer.rs
├── longest_common_prefix.rs
├── valid_parentheses.rs
├── merge_two_lists.rs
├── remove_duplicates.rs
├── remove_element.rs
├── str_str.rs
├── search_insert.rs
├── length_of_last_word.rs
├── plus_one.rs
├── climb_stairs.rs
├── delete_duplicates.rs
├── inorder_traversal.rs
├── same_tree.rs
├── single_number.rs
└── excel_column_title.rs
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

### 14. Longest Common Prefix
**Problem:** Write a function to find the longest common prefix string amongst an array of strings. If there is no common prefix, return an empty string `""`.

```rust
impl Solution {
    pub fn longest_common_prefix(strs: Vec<String>) -> String {
        if strs.is_empty() {
            return String::new();
        }

        let mut prefix = &strs[0][..];

        for string in strs.iter().skip(1) {
            while !string.starts_with(prefix) {
                prefix = &prefix[0..prefix.len() - 1];

                if prefix.is_empty() {
                    return String::new()
                }
            }
        }
        prefix.to_string()
    }
}
```

**Key Rust Concepts:**
- `&strs[0][..]` — takes a string slice (`&str`) of the entire first string; this becomes the starting candidate prefix before being trimmed down
- `strs.iter().skip(1)` — iterates over the remaining strings, skipping the first one since it's already the initial `prefix` candidate
- `.starts_with(prefix)` — checks if the current string begins with the candidate prefix; `&str` makes this a single built-in call instead of manual character comparison
- `&prefix[0..prefix.len() - 1]` — string slicing shrinks the prefix by one character from the right each time there's a mismatch
- `prefix.is_empty()` — early exit guard; once the prefix shrinks to nothing, no common prefix exists across all strings
- `prefix.to_string()` — converts the final `&str` slice into an owned `String` for the return type
- **Shrinking Window pattern** — instead of comparing character by character, the candidate prefix progressively shrinks from the right until every string matches it

**Complexity:**
- ⏱ Time: O(n·m) — `n` strings are checked, and in the worst case the prefix shrinks character by character (`m` = length of the first string)
- 💾 Space: O(1) — only a slice reference is kept; no new strings are allocated until the final `.to_string()`

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

### 28. Find the Index of the First Occurrence in a String
**Problem:** Given two strings `haystack` and `needle`, return the index of the first occurrence of `needle` in `haystack`, or `-1` if `needle` is not part of `haystack`.

```rust
impl Solution {
    pub fn str_str(haystack: String, needle: String) -> i32 {
        let h_bytes = haystack.as_bytes();
        let n_bytes = needle.as_bytes();

        let h_len = h_bytes.len();
        let n_len = n_bytes.len();

        if n_len > h_len {
            return -1;
        }

        for i in 0..=(h_len - n_len) {
            if &h_bytes[i..i + n_len] == n_bytes {
                return i as i32;
            }
        }
        -1
    }
}
```

**Key Rust Concepts:**
- `.as_bytes()` — converts a `&str` to a `&[u8]` byte slice; byte comparison is faster than char-by-char for ASCII strings and avoids UTF-8 multi-byte complexity
- `if n_len > h_len` — early return guard; also prevents the `h_len - n_len` subtraction from underflowing since both are `usize` (unsigned), which would wrap around and panic in debug mode
- `0..=(h_len - n_len)` — inclusive range so the last valid window starting index is included (e.g., for `h_len=5, n_len=3`, we check indices `0, 1, 2`)
- `&h_bytes[i..i + n_len]` — slice the haystack byte array into a window of exactly `needle`'s length at each position
- `== n_bytes` — Rust slices implement `PartialEq`, so this compares the two byte slices element-by-element in one expression
- `i as i32` — cast `usize` index to `i32` for the return type
- **Sliding Window** — a fixed-size window of `needle`'s length slides across the haystack, comparing at each position

**Complexity:**
- ⏱ Time: O(n·m) — for each of the `n - m` positions, up to `m` bytes are compared (`n` = haystack length, `m` = needle length)
- 💾 Space: O(1) — no extra allocations; `.as_bytes()` is a zero-copy view into the existing string data

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

### 70. Climbing Stairs
**Problem:** You are climbing a staircase. It takes `n` steps to reach the top. Each time you can either climb `1` or `2` steps. In how many distinct ways can you climb to the top?

```rust
impl Solution {
    pub fn climb_stairs(n: i32) -> i32 {
        if n <= 2 {
            return n;
        }
        let mut first = 1;
        let mut second = 2;

        for _ in 3..=n {
            let current = first + second;
            first = second;
            second = current;
        }
        second
    }
}
```

**Key Rust Concepts:**
- `if n <= 2 { return n; }` — early return handles the base cases: 1 step → 1 way, 2 steps → 2 ways
- `let mut first = 1` / `let mut second = 2` — rolling variables track the previous two Fibonacci-like values, avoiding an array
- `3..=n` — inclusive range starting from `3`, since the first two cases are already handled
- `for _ in ...` — the loop variable is discarded with `_` because only the iteration count matters, not the index value
- `let current = first + second` — computes the next value before updating, preventing overwrite issues
- **Fibonacci pattern** — the number of ways to reach step `n` equals ways to reach `n-1` plus ways to reach `n-2`, identical to the Fibonacci sequence

**Complexity:**
- ⏱ Time: O(n) — single pass from step 3 to n
- 💾 Space: O(1) — only two variables used regardless of input size

---

### 83. Remove Duplicates from Sorted List
**Problem:** Given the `head` of a sorted linked list, delete all duplicates such that each element appears only once. Return the linked list sorted as well.

```rust
impl Solution {
    pub fn delete_duplicates(mut head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        let mut current = head.as_mut();

        while let Some(node) = current {
            while let Some(next_node) = node.next.as_mut() {
                if node.val == next_node.val {
                    node.next = next_node.next.take();
                } else {
                    break;
                }
            }
            current = node.next.as_mut();
        }
        head
    }
}
```

**Key Rust Concepts:**
- `mut head: Option<Box<ListNode>>` — takes ownership of the list and makes it mutable; no dummy node needed since we return `head` directly
- `head.as_mut()` — borrows a mutable reference to the inner `Box<ListNode>` without consuming the `Option`, so `head` can still be returned at the end
- `while let Some(node) = current` — outer loop walks forward through the list one unique node at a time
- `while let Some(next_node) = node.next.as_mut()` — inner loop keeps peeking at the immediate successor without advancing `current` yet
- `node.next = next_node.next.take()` — `.take()` moves the node after the duplicate out of its `Option` (leaving `None`) and splices it in as the new `next`, effectively unlinking the duplicate in a single safe operation
- `break` — exits the inner loop as soon as a non-duplicate neighbour is found, then the outer loop advances `current`
- **In-place surgery** — no new nodes are allocated; only `next` pointers are rewired, keeping space O(1)

**Complexity:**
- ⏱ Time: O(n) — every node is visited at most twice (once by the outer loop, once by the inner)
- 💾 Space: O(1) — pointer manipulation only, no auxiliary data structures

---

### 94. Binary Tree Inorder Traversal
**Problem:** Given the `root` of a binary tree, return the inorder traversal of its nodes' values.

```rust
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn inorder_traversal(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let mut result = Vec::new();
        Self::traverse(&root, &mut result);
        result
    }

    fn traverse(node: &Option<Rc<RefCell<TreeNode>>>, result: &mut Vec<i32>) {
        if let Some(current) = node {
            let current_borrow = current.borrow();

            // Left first
            Self::traverse(&current_borrow.left, result);

            // Current node
            result.push(current_borrow.val);

            // Right last
            Self::traverse(&current_borrow.right, result);
        }
    }
}
```

**Key Rust Concepts:**
- `Option<Rc<RefCell<TreeNode>>>` — LeetCode's standard tree node type; `Rc` allows shared ownership of nodes, `RefCell` allows borrowing the inner value at runtime
- `&Option<Rc<RefCell<TreeNode>>>` — the helper function takes a reference instead of ownership, so the same node can be inspected without moving it out of the tree structure
- `&mut Vec<i32>` — passes the result vector by mutable reference so every recursive call can push into the same accumulator instead of returning and merging vectors
- `if let Some(current) = node` — pattern matches only the `Some` case; `None` (an empty subtree) falls through and the function simply returns, acting as the base case for recursion
- `.borrow()` — `RefCell`'s method to get a shared read-only reference to the node's fields at runtime
- `result.push(current_borrow.val)` — the position of this line between the two recursive calls is what defines **inorder**: left subtree, then current node, then right subtree
- **Recursion with accumulator** — rather than building and concatenating `Vec`s at every level (which would allocate repeatedly), a single mutable `Vec` is threaded through all recursive calls

**Complexity:**
- ⏱ Time: O(n) — every node in the tree is visited exactly once
- 💾 Space: O(h) — call stack depth equals tree height `h` (excluding the O(n) result vector, which is required output, not auxiliary space). O(log n) for balanced trees, O(n) worst case for skewed trees

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

### 136. Single Number
**Problem:** Given a non-empty array of integers `nums`, every element appears twice except for one. Find that single one. You must implement a solution with linear runtime complexity and use only constant extra space.

```rust
impl Solution {
    pub fn single_number(nums: Vec<i32>) -> i32 {
        nums.iter().fold(0, |acc, &num| acc ^ num)
    }
}
```

**Key Rust Concepts:**
- `.iter()` — borrows each element of the vector, yielding `&i32` references without consuming the vector
- `.fold(0, |acc, &num| acc ^ num)` — a functional reducer: starts with an accumulator of `0`, then applies the closure to every element left-to-right, returning the final accumulated value
- `|acc, &num|` — the closure destructures the `&i32` reference into `num` directly via pattern matching, so no explicit dereference is needed
- `acc ^ num` — XOR operator; the key mathematical insight is that `x ^ x == 0` (any number XORed with itself cancels out) and `x ^ 0 == x` (XOR with zero is identity). Since every duplicate pair cancels, only the unique element survives
- **XOR trick** — effectively: `0 ^ a ^ b ^ b ^ a ^ c == c`. Order doesn't matter because XOR is both commutative and associative

**Complexity:**
- ⏱ Time: O(n) — single pass through the array via `fold`
- 💾 Space: O(1) — only the accumulator variable is used, no extra data structures

---

### 168. Excel Sheet Column Title
**Problem:** Given an integer `column_number`, return its corresponding column title as it appears in an Excel sheet (e.g. 1 → "A", 26 → "Z", 27 → "AA", 28 → "AB").

```rust
impl Solution {
    pub fn convert_to_title(mut column_number: i32) -> String {
        let mut result = String::new();

        while column_number > 0 {
            column_number -= 1;

            let reminder = (column_number % 26) as u8;

            let c = (b'A' + reminder) as char;

            result.push(c);

            column_number /= 26;
        }
        result.chars().rev().collect()
    }
}
```

**Key Rust Concepts:**
- `mut column_number: i32` — takes ownership and marks the parameter as mutable so it can be modified in-place inside the loop without a separate variable
- `column_number -= 1` — the critical adjustment that converts 1-based Excel indexing to 0-based; without this, 26 would map to nothing instead of `'Z'` and 27 would give `'A'` instead of `'AA'`
- `(column_number % 26) as u8` — computes the current digit (0–25) in base-26 and casts it to `u8` so it can be used in byte arithmetic
- `b'A'` — a **byte literal** in Rust, equivalent to `65u8`; adding the remainder to it gives the ASCII code of the target letter
- `(b'A' + reminder) as char` — casts the computed `u8` byte value back to a `char`; this is safe here because the result is always a valid ASCII uppercase letter
- `result.push(c)` — builds the string in reverse order (least significant digit first), because the remainder at each step is the rightmost letter
- `result.chars().rev().collect()` — reverses the accumulated characters and collects them into a new `String`; `.collect::<String>()` is inferred from the return type
- **Base-26 with offset** — unlike standard base conversion, Excel columns have no zero digit; the `column_number -= 1` shift at each iteration compensates for this, making the mapping bijective

**Complexity:**
- ⏱ Time: O(log n) — the number of iterations equals the number of digits in the base-26 representation of `column_number`
- 💾 Space: O(log n) — the result string holds one character per base-26 digit

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
