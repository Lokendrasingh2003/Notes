# Delete All Occurrences of a Key in DLL

## Link : https://takeuforward.org/plus/dsa/problems/delete-all-occurrences-of-a-key-in-dll?source=strivers-a2z-dsa-track

## 1. Problem

Given the head of a doubly linked list and a `target`, delete **every node** whose value is equal to `target`.

Example:

```text
1 ⇄ 2 ⇄ 3 ⇄ 1 ⇄ 4
target = 1
```

Output:

```text
2 ⇄ 3 ⇄ 4
```

---

## 2. Pattern

**Doubly Linked List + Pointer Reconnection**

For every node:

* If it is not the target → move forward.
* If it is the target → remove it by reconnecting its `prev` and `next` nodes.

The basic deletion is:

```text
prev ⇄ target ⇄ next
```

becomes:

```text
prev ⇄ next
```

---

## 3. Key Idea

For the current node:

```js
let nextNode = temp.next
let prevNode = temp.prev
```

Save both neighbors before modifying anything.

Then reconnect:

```js
if(nextNode){
    nextNode.prev = prevNode
}

if(prevNode){
    prevNode.next = nextNode
}
```

If the target node is the head:

```js
if(temp == head){
    head = head.next
}
```

Finally, move to the saved next node.

---

## 4. Approach

### Step 1: Traverse the list

```js
let temp = head

while(temp){
    ...
}
```

### Step 2: Check whether the current node should be deleted

```js
if(temp.val == target)
```

### Step 3: Update the head if necessary

If the target is at the head:

```js
if(temp == head){
    head = head.next
}
```

The new head's `prev` will be set to `null` by the general reconnection logic.

### Step 4: Save both neighbors

```js
let nextNode = temp.next
let prevNode = temp.prev
```

This is important because we need both sides of the node being removed.

### Step 5: Connect the next node to the previous node

```js
if(nextNode){
    nextNode.prev = prevNode
}
```

### Step 6: Connect the previous node to the next node

```js
if(prevNode){
    prevNode.next = nextNode
}
```

Now the target node is bypassed.

### Step 7: Continue traversal

```js
temp = temp.next
```

Since `temp.next` is still the original next node, this works in your implementation.

---

## 5. Code

```js
class Solution {

    deleteAllOccurrences(head, target) {

        let temp = head

        while(temp){

            if(temp.val == target){

                if(temp == head){
                    head = head.next
                }

                let nextNode = temp.next
                let prevNode = temp.prev

                if(nextNode){
                    nextNode.prev = prevNode
                }

                if(prevNode){
                    prevNode.next = nextNode
                }

                temp = temp.next
            }
            else{
                temp = temp.next
            }
        }

        return head
    }
}
```

---

## 6. Dry Run

Input:

```text
1 ⇄ 2 ⇄ 1 ⇄ 3 ⇄ 1
target = 1
```

### Delete first `1`

```text
2 ⇄ 1 ⇄ 3 ⇄ 1
```

### Delete middle `1`

```text
2 ⇄ 3 ⇄ 1
```

### Delete last `1`

```text
2 ⇄ 3
```

Final:

```text
2 ⇄ 3
```

---

## 7. Important Pointer Logic

Suppose:

```text
A ⇄ B ⇄ C
```

and `B` needs to be deleted.

We have:

```js
let nextNode = B.next   // C
let prevNode = B.prev   // A
```

Then:

```js
nextNode.prev = prevNode
```

gives:

```text
A ← C
```

and:

```js
prevNode.next = nextNode
```

gives:

```text
A → C
```

So the final structure becomes:

```text
A ⇄ C
```

This is the core operation of the problem.

---

## 8. Important Case: Head Deletion

Suppose:

```text
7 ⇄ 2 ⇄ 3
target = 7
```

Initially:

```text
head → 7
```

After:

```js
head = head.next
```

we get:

```text
head → 2 ⇄ 3
```

The general code then executes:

```js
nextNode.prev = prevNode
```

Since the old head has:

```text
prevNode = null
```

it becomes:

```text
2.prev = null
```

which is exactly what we need.

---

## 9. Important Case: All Nodes Match

Input:

```text
7 ⇄ 7 ⇄ 7 ⇄ 7
target = 7
```

Every node is deleted.

Eventually:

```text
head = null
```

Therefore the correct answer is:

```text
head
```

---

## 10. Why Save `nextNode`?

This is the most important implementation detail.

Before deleting the current node:

```js
let nextNode = temp.next
```

We save where we need to go next.

After reconnecting the surrounding nodes, we can safely continue traversal.

A safer and clearer version is to explicitly use the saved pointer:

```js
temp = nextNode
```

So I would slightly improve your code to:

```js
class Solution {

    deleteAllOccurrences(head, target) {

        let temp = head

        while(temp){

            if(temp.val == target){

                let nextNode = temp.next
                let prevNode = temp.prev

                if(temp == head){
                    head = nextNode
                }

                if(nextNode){
                    nextNode.prev = prevNode
                }

                if(prevNode){
                    prevNode.next = nextNode
                }

                temp = nextNode
            }
            else{
                temp = temp.next
            }
        }

        return head
    }
}
```

This version makes the traversal logic clearer because after deletion we explicitly move to the node saved before modifying the links.

---

## 11. Complexity

### Time

```text
O(n)
```

Every node is visited once.

### Space

```text
O(1)
```

Only a few pointers are used.

---

## 12. Common Mistakes

### Mistake 1: Forgetting `prev`

In a DLL, deleting a node requires updating both directions:

```js
nextNode.prev = prevNode
prevNode.next = nextNode
```

### Mistake 2: Forgetting head update

If the head contains the target:

```js
head = head.next
```

Otherwise the returned head would still point to a deleted node.

### Mistake 3: Accessing `next.prev` without checking

Wrong:

```js
temp.next.prev = temp.prev
```

If `temp` is the last node, `temp.next` is `null`.

Correct:

```js
if(nextNode){
    nextNode.prev = prevNode
}
```

### Mistake 4: Only deleting the first occurrence

The loop must continue after deletion because the target can appear multiple times, including consecutive nodes.

---

## 13. Interview Point

If asked:

**"Why is deletion easier in a doubly linked list?"**

Answer:

> Each node has both `prev` and `next` pointers, so when I find the target node, I can directly connect its previous node to its next node and update the backward link as well.

---

## 14. Memory Trick

### **Save → Bypass → Move**

For every target node:

```text
Save:
prev + next

Bypass:
prev.next = next
next.prev = prev

Move:
temp = next
```

For the head:

```text
head = next
```

### One-line memory:

> **Delete a DLL node = connect its previous and next nodes in both directions.**
