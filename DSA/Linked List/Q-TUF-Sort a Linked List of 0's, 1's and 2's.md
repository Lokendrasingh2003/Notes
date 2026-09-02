# Sort a Linked List of 0's, 1's and 2's

# Link : https://takeuforward.org/plus/dsa/problems/sort-a-ll-of-0's-1's-and-2's?source=strivers-a2z-dsa-track

## 1. Problem

Given a linked list containing only `0`, `1`, and `2`, sort it in ascending order.

The sorting must be done **in-place** by changing links between existing nodes. We should **not create new data nodes**.

**Example:**

```text
Input:  1 → 0 → 2 → 0 → 1
Output: 0 → 0 → 1 → 1 → 2
```

---

## 2. Pattern

**Linked List Segregation / Three Buckets**

We maintain three separate linked lists:

```text
0-list → all 0s
1-list → all 1s
2-list → all 2s
```

Then connect them:

```text
0-list → 1-list → 2-list
```

This avoids actually swapping node values.

---

## 3. Key Idea

Use three dummy nodes:

```text
dummyZero
dummyOne
dummyTwo
```

And three pointers:

```text
zero
one
two
```

Each pointer represents the **last node** in its respective list.

For every node:

* If value is `0` → attach to zero list
* If value is `1` → attach to one list
* If value is `2` → attach to two list

Finally:

```text
zero.next = dummyOne.next
one.next = dummyTwo.next
```

Return:

```text
dummyZero.next
```

---

## 4. Approach

### Step 1: Create three dummy nodes

```js
let dummyZero = new ListNode(-1)
let dummyOne = new ListNode(-1)
let dummyTwo = new ListNode(-1)
```

These dummy nodes make insertion easier because every list has a starting point.

### Step 2: Create three tail pointers

```js
let zero = dummyZero
let one = dummyOne
let two = dummyTwo
```

These pointers always point to the last node of their respective list.

### Step 3: Traverse the original list

Before moving the current node into another list, save its next node:

```js
let nextNode = temp.next
```

Then disconnect it:

```js
temp.next = null
```

This is important because we are rearranging the existing links.

### Step 4: Put the node into the correct list

```js
if(temp.data == 0){
    zero.next = temp
    zero = zero.next
}
else if(temp.data == 1){
    one.next = temp
    one = one.next
}
else{
    two.next = temp
    two = two.next
}
```

### Step 5: Connect the three lists

```text
0-list → 1-list → 2-list
```

Code:

```js
zero.next = dummyOne.next
one.next = dummyTwo.next
```

### Step 6: Return the beginning

```js
return dummyZero.next
```

---

## 5. Code

```js
class Solution {
    sortList(head) {
        let dummyZero = new ListNode(-1)
        let dummyOne = new ListNode(-1)
        let dummyTwo = new ListNode(-1)

        let zero = dummyZero
        let one = dummyOne
        let two = dummyTwo

        let temp = head

        while (temp) {
            let nextNode = temp.next
            temp.next = null

            if (temp.data == 0) {
                zero.next = temp
                zero = zero.next
            }
            else if (temp.data == 1) {
                one.next = temp
                one = one.next
            }
            else {
                two.next = temp
                two = two.next
            }

            temp = nextNode
        }

        zero.next = dummyOne.next
        one.next = dummyTwo.next

        return dummyZero.next
    }
}
```

> **Note:** If the platform's node property is `val` instead of `data`, use `temp.val`.

---

## 6. Dry Run

For:

```text
2 → 0 → 1 → 2 → 0
```

After traversal:

```text
0-list: 0 → 0
1-list: 1
2-list: 2 → 2
```

After connecting:

```text
0 → 0 → 1 → 2 → 2
```

So the result is:

```text
[0, 0, 1, 2, 2]
```

---

## 7. Why `temp.next = null`?

Suppose:

```text
temp = 0
temp.next = 1
```

If we directly attach `temp` to the zero list without disconnecting it, the old connection may remain:

```text
0 → 1 → ...
```

We are rearranging nodes into different lists, so we first break the old connection:

```js
temp.next = null
```

Then attach the node safely.

---

## 8. Why Dummy Nodes?

Without dummy nodes, we would need special handling for the first `0`, first `1`, and first `2`.

Dummy nodes simplify everything:

```text
dummyZero → 0 → 0
dummyOne  → 1 → 1
dummyTwo  → 2
```

The actual result starts from:

```js
dummyZero.next
```

The dummy nodes are helper nodes, not replacements for the original data nodes.

---

## 9. Complexity

### Time

```text
O(n)
```

We traverse the linked list only once.

### Space

```text
O(1)
```

Only a fixed number of pointers/dummy nodes are used.

No new nodes containing the list values are created.

---

## 10. Interview Point

If the interviewer asks:

**"Why not just count the number of 0s, 1s and 2s?"**

You can say:

> We could count the frequencies and then overwrite node values, but the problem specifically asks us to sort by changing links. Therefore, I segregate the existing nodes into three lists and connect those lists at the end.

---

## 11. Common Mistakes

### Mistake 1: Forgetting to save `next`

Wrong:

```js
temp.next = null
temp = temp.next
```

After disconnecting, `temp.next` is `null`.

Correct:

```js
let nextNode = temp.next
temp.next = null
...
temp = nextNode
```

### Mistake 2: Returning the wrong head

Correct:

```js
return dummyZero.next
```

Not:

```js
return dummyZero
```

### Mistake 3: Connecting using the dummy nodes

Correct:

```js
zero.next = dummyOne.next
one.next = dummyTwo.next
```

We need the **actual first nodes** of the `1` and `2` lists.

### Mistake 4: Confusing `data` and `val`

Depending on the platform:

```js
temp.data
```

or:

```js
temp.val
```

Use whatever property the `ListNode` definition provides.

---

## 12. Memory Trick

**"Separate → Attach → Connect"**

```text
0 → 0-list
1 → 1-list
2 → 2-list

0-list → 1-list → 2-list
```

Think:

> **Three values → Three lists → Join them.**

---

## 13. Related Problems

* **75. Sort Colors** → same `0,1,2` idea but array
* **21. Merge Two Sorted Lists** → useful for linked-list merging
* **328. Odd Even Linked List** → linked-list segregation using pointers
* **148. Sort List** → general linked-list sorting using merge sort
