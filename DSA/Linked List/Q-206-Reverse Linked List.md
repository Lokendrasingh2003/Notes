Link : https://leetcode.com/problems/reverse-linked-list/description/

Pattern:
Linked List + Pointer Manipulation

Approach:
- Keep three pointers: prev, curr, next.
- Save curr.next in next.
- Reverse curr.next to prev.
- Move prev and curr forward.
- Return prev.

Code:

```javascript

var reverseList = function(head) {
    let prev = null 
    let cur = head 
    while(cur){
        let next = cur.next 
        cur.next = prev 
        prev = cur 
        cur = next
    }
    
    return prev
    
};

```

Time: O(n)
Space: O(1)


Similar:
92. Reverse Linked List II