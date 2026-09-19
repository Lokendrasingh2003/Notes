# Subsets I — Sum of All Subsets

## Link : https://takeuforward.org/plus/dsa/problems/subsets-i?source=strivers-a2z-dsa-track

## 📌 Problem

Given an array `nums`, return the **sum of every possible subset**.

For `nums = [2, 3]`:

```text
Subsets:
[]       → 0
[2]      → 2
[3]      → 3
[2, 3]   → 5

Output: [0, 2, 3, 5]
```

---

## 🧠 Pattern

**Backtracking / Pick or Not Pick**

For every element, we have exactly **2 choices**:

1. Pick the element → add it to `sum`
2. Don't pick the element → keep `sum` unchanged

---

## 💡 Key Idea

Instead of actually creating every subset, we only maintain its **sum**.

For each `nums[index]`:

```text
                nums[index]
                /          \
             Pick          Not Pick
              /              \
      sum + nums[index]       sum
```

When we reach the end of the array, the current `sum` represents one subset.

So we add it to `result`.

---

## 🔍 Your Approach

```js
class Solution {
    subsetSums(nums) {
        let result = []

        function findSubset(nums, index, sum) {
            if (index === nums.length) {
                result.push(sum)
                return
            }

            // Pick current element
            findSubset(nums, index + 1, sum + nums[index])

            // Don't pick current element
            findSubset(nums, index + 1, sum)
        }

        findSubset(nums, 0, 0)

        return result
    }
}
```

### Step 1: Start

```js
findSubset(nums, 0, 0)
```

* `index = 0`
* `sum = 0`

Initially, no elements have been selected.

### Step 2: Pick

```js
findSubset(nums, index + 1, sum + nums[index])
```

We include the current element in the subset.

### Step 3: Don't Pick

```js
findSubset(nums, index + 1, sum)
```

We skip the current element.

### Step 4: Base Case

```js
if (index === nums.length) {
    result.push(sum)
    return
}
```

Once all elements are processed, we have one complete subset, so its sum is stored.

---

## 🧪 Dry Run

For:

```text
nums = [1, 2]
```

Recursion:

```text
                     sum=0
                    /     \
                 pick 1   skip 1
                   1         0
                 /   \      /   \
             +2      -2   +2    -2
              3       1    2     0
```

So:

```text
result = [3, 1, 2, 0]
```

Order doesn't matter.

---

## ⚡ Important Observation

For `n` elements, there are:

```text
2^n
```

possible subsets.

Why?

Each element has 2 choices:

```text
Pick / Not Pick
```

Therefore:

```text
2 × 2 × ... × 2 = 2^n
```

---

## ⏱️ Complexity

### Time

```text
O(2^n)
```

We generate one result for every subset.

### Auxiliary Space

```text
O(n)
```

because the recursion can go as deep as `n`.

The `result` array itself contains `2^n` sums, so if output space is included:

```text
O(2^n)
```

---

## 🎯 Common Mistakes

### 1. Forgetting the base case

You need:

```js
if (index === nums.length) {
    result.push(sum)
    return
}
```

Otherwise recursion never stops.

### 2. Using `sum + nums[index]` in both calls

Only the **pick** branch should add the element.

```js
// Pick
sum + nums[index]

// Not Pick
sum
```

### 3. Thinking we need to create the actual subset

We don't need:

```js
current.push(nums[index])
```

because the problem only asks for the **sum**.

We can directly carry the sum.

---

## 🗣️ Interview Explanation

> "I use pick-or-not-pick recursion. For every element, I have two choices: either include it in the subset and add its value to the current sum, or exclude it and keep the sum unchanged. When I reach the end of the array, I add the current sum to the result. Since there are `2^n` subsets, the time complexity is `O(2^n)` and the recursion stack uses `O(n)` space."

---

## 🧠 Memory Trick

**Pick → Add → Skip → Same Sum → End → Store**

```text
Pick     → sum + nums[index]
Not Pick → sum
End      → result.push(sum)
```

### One-line formula

```text
Every element = Pick OR Not Pick
```

This is the same **Pick / Not Pick** pattern you used in Combination Sum, but here we don't need to track combinations—only the subset sum.
