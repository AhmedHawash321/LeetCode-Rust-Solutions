# LeetCode-Rust-Solutions
my solutions for leet code problems using rust :

1 - Roman to Integer

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

Key Features Used:

- useing peekable() here is Iterator Adaptor we use it to know the next value .
- map_or(0, |&c|) i used it here to deal with optin instead of handling match starting with value 0 in default value if option is none , and closure |&c| since i used peek so it will give me refrence to the char
- then in if statement in case of substraction we add lower value before the bigger one the same we will do in case of addition we will add the current value to the next on right like this (if result is 4 it will be IV .. else if its 6 it will be VI )
- finally we return result .

- **Time Complexity:** O(n) — Single pass through the string.
- **Space Complexity:** O(1) — Only a few variables for tracking values.

- --------------------------------------------------------------------------------------

2 - merge two lists : 

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

Key Features Used:

- first we create dummy node with value 0 to start our arrangment , notice we used mut in every single variable because rust dont allow to change without mut and &mut to borrow value .
- while list1.is_some() && list2.is_some() : it means in case if lists have elements .
- list1.as_ref().unwrap().val < list2... : we used as_ref to make sure that rust will borrow as reference without taking ownership , unwrap() to get value inside the Box thats why we used is_some before it not to stop the program if its empty , then we comparing the smallest value in list1 and list2 .
-  in if statement we moved forward along list1 and list2 (current.next = list1 or list2)
- current = current.next.as_mut().unwrap() : this line will make sure our previuse nodes will not disappear after we add the new one .
- current.next = if list1.is_some() { list1 } else { list2 } : here we are saying any nodes else in any of either lists remains will arrange directly .
- finally we used dummy.next without ;  so we can return final result .
