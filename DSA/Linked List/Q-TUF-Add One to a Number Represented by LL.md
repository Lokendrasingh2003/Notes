# Add One to a Number Represented by LL

## Link : https://takeuforward.org/plus/dsa/problems/add-one-to-a-number-represented-by-ll?source=strivers-a2z-dsa-track

## 1. Problem

A linked list represents a number where each node contains one digit.

For example:

```text
1 → 2 → 3
```

represents:

```text
123
```

We need to add `1` and return the updated linked list.

Example:

```text
9 → 9
```

becomes:

```text
1 → 0 → 0
```

---

## 2. Pattern

**Reverse Linked List + Carry**

Since addition starts from the **rightmost digit**, but a singly linked list can only be traversed from left to right, we:

```text
Reverse → Add 1 → Reverse back
```

---

## 3. Key Idea

Consider:

```text
1 → 2 → 9
```

Reverse it:

```text
9 → 2 → 1
```

Now addition becomes easy:

```text
9 + 1 = 10
```

So:

```text
9 → 2 → 1
↓
0 → 3 → 1
```

Reverse again:

```text
1 → 3 → 0
```

Answer:

```text
130
```

If every digit is `9`, the carry remains after processing the entire list.

Example:

```text
9 → 9
```

After addition:

```text
0 → 0
```

Carry is still `1`, so create:

```text
1 → 0 → 0
```

---

## 4. Approach

### Step 1: Reverse the list

```js
let tempHead = reverse(head)
```

Now the least significant digit is at the front.

### Step 2: Start with carry = 1

```js
let carry = 1
```

We are adding exactly one, so initially:

```text
carry = 1
```

### Step 3: Process each digit

If the digit is `9`:

```js
temp.val = 0
carry = 1
```

Because:

```text
9 + 1 = 10
```

If the digit is less than `9`:

```js
temp.val = temp.val + carry
carry = 0
```

After that, no further nodes need to be changed.

### Step 4: Reverse the list again

```js
tempHead = reverse(tempHead)
```

This restores the original left-to-right digit order.

### Step 5: Handle remaining carry

If carry is still `1`, it means the original number contained only `9`s.

For example:

```text
99 → 100
```

So create a new node:

```js
let newHead = new ListNode(carry)
newHead.next = head
```

and return it.

---

## 5. Code

```js
class Solution {
    addOne(head) {
        let tempHead = reverse(head)
        let temp = tempHead
        let carry = 1

        while (temp) {
            if (carry > 0) {
                if (temp.val == 9) {
                    temp.val = 0
                    carry = 1
                }
                else {
                    temp.val = temp.val + carry
                    carry = 0
                }

                temp = temp.next
            }
            else {
                break
            }
        }

        tempHead = reverse(tempHead)

        if (carry > 0) {
            let newHead = new ListNode(carry)
            newHead.next = head
            return newHead
        }

        return tempHead
    }
}

function reverse(head) {
    let prev = null
    let current = head

    while (current) {
        let next = current.next
        current.next = prev
        prev = current
        current = next
    }

    return prev
}
```

---

## 6. Dry Run

For:

```text
9 → 9 → 8
```

### Reverse

```text
8 → 9 → 9
```

### Add 1

First:

```text
8 + 1 = 9
```

Carry becomes `0`.

So:

```text
9 → 9 → 9
```

### Reverse back

```text
9 → 9 → 9
```

Therefore:

```text
998 + 1 = 999
```

---

## 7. Important Case: All 9s

For:

```text
9 → 9
```

After reversing:

```text
9 → 9
```

Process first `9`:

```text
9 + 1 = 10
```

becomes:

```text
0
```

Carry remains `1`.

Process second `9`:

```text
9 + 1 = 10
```

becomes:

```text
0
```

Carry is still `1`.

After reversing:

```text
0 → 0
```

Since carry remains:

```js
if(carry > 0)
```

create a new node:

```text
1 → 0 → 0
```

---

## 8. Why Reverse Is Needed?

Normally addition starts from the rightmost digit:

```text
123
  ↑
start here
```

But a singly linked list gives us:

```text
1 → 2 → 3
↑
start here
```

So we reverse it:

```text
3 → 2 → 1
↑
start here
```

Now we can process the digits in the same order as normal addition.

---

## 9. Complexity

### Time

```text
O(n)
```

We traverse the list for addition and reverse it twice.

Technically:

```text
O(n) + O(n) + O(n) = O(n)
```

### Space

```text
O(1)
```

Only pointers and the carry variable are used.

One additional node may be created when the final carry is `1`.

---

## 10. Common Mistakes

### Mistake 1: Forgetting the carry

For:

```text
9 → 9
```

both digits become `0`, but we still have:

```text
carry = 1
```

That carry becomes the new first node.

### Mistake 2: Not reversing back

After addition, the list is still reversed.

Always do:

```js
tempHead = reverse(tempHead)
```

before returning.

### Mistake 3: Continuing after carry becomes zero

Once:

```js
carry = 0
```

no more digits need to be changed.

Your solution correctly handles this with:

```js
else {
    break
}
```

### Mistake 4: Creating new nodes for every digit

The problem expects an in-place solution. We should modify existing nodes and only create a new node when a final carry requires an extra digit.

---

## 11. Interview Point

If asked:

**"Why don't you traverse from the tail directly?"**

Answer:

> A singly linked list doesn't provide backward traversal, so I reverse the list first. This lets me process the least significant digit first, propagate the carry, and then reverse the list back to its original order.

---

## 12. Memory Trick

### **Reverse → Add Carry → Reverse**

```text
1 → 2 → 9
      ↓
9 → 2 → 1
      ↓
0 → 3 → 1
      ↓
1 → 3 → 0
```

For all `9`s:

```text
9 → 9
↓
0 → 0
↓
1 → 0 → 0
```

**Remember:**

> **Rightmost digit first → Reverse the list.**
