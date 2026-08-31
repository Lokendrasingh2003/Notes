# 141. Linked List Cycle

# Link : https://leetcode.com/problems/linked-list-cycle/

### Pattern

**Fast & Slow Pointer (Floyd's Cycle Detection)**

### Problem

Given the head of a linked list, determine whether the linked list contains a cycle.

### Approach

Use two pointers:

* `slow` moves **1 step** at a time.
* `fast` moves **2 steps** at a time.
* If there is a cycle, eventually `slow` and `fast` will meet.
* If there is no cycle, `fast` will reach `null`.

### Code

```javascript
var hasCycle = function(head) {
    let slow = head;
    let fast = head;

    while (fast !== null && fast.next !== null) {
        slow = slow.next;
        fast = fast.next.next;

        if (slow === fast) {
            return true;
        }
    }

    return false;
};
```

### Why does this work?

Imagine a circular track.

The `slow` pointer moves at speed `1`, while the `fast` pointer moves at speed `2`.

If a cycle exists, `fast` will eventually catch up with `slow`.

If there is no cycle, `fast` reaches the end of the linked list.

### Example

```text
1 → 2 → 3 → 4
    ↑       ↓
    ← ← ← ←
```

Here, `3 → 4 → 2` forms a cycle.

Eventually:

```text
slow = 3
fast = 3
```

Therefore:

```javascript
slow === fast
```

and we return `true`.

### Important Condition

```javascript
while (fast !== null && fast.next !== null)
```

We check both because we access:

```javascript
fast.next.next
```

If `fast` or `fast.next` is `null`, we cannot move `fast` two steps.

### Complexity

**Time:** `O(n)`

**Space:** `O(1)`

### Key Interview Point

> When you see a linked-list problem asking whether there is a cycle, think **Fast & Slow Pointer**.

### Common Mistake

Don't compare the **values**:

```javascript
slow.val === fast.val // ❌
```

Compare the **nodes themselves**:

```javascript
slow === fast // ✅
```

Two different nodes can have the same value.

### Related Problems

* 142. Linked List Cycle II
* 876. Middle of the Linked List
* 202. Happy Number
