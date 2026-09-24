# 📚 Bit Manipulation & Bitwise Tricks

A quick reference for commonly used **Bit Manipulation** techniques in DSA and coding interviews using JavaScript.

---

## 📌 1. Swap Two Numbers Without Using a Third Variable

We can swap two numbers using the **XOR (`^`) operator**.

### Code

    let a = 5;
    let b = 10;

    a = a ^ b;
    b = a ^ b;
    a = a ^ b;

    console.log(a); // 10
    console.log(b); // 5

### How it works

Important XOR properties:

    x ^ x = 0
    x ^ 0 = x

For example:

    a = 5  → 0101
    b = 10 → 1010

Then:

    a = a ^ b
    b = a ^ b
    a = a ^ b

The values are swapped without using a third variable.

### ⚠️ JavaScript Note

JavaScript bitwise operators work on **32-bit signed integers**, so this technique should be used with that limitation in mind.

---

## 📌 2. Add Two Numbers Without Using `+`

We can add two numbers using:

- `XOR (^)` → calculates the sum without carry
- `AND (&)` → finds the carry
- `Left Shift (<<)` → moves the carry to the correct position

### Code

    function add(a, b) {
        while (b !== 0) {
            let carry = a & b;

            a = a ^ b;

            b = carry << 1;
        }

        return a;
    }

    console.log(add(5, 7)); // 12

### How it works

Suppose:

    a = 5  → 0101
    b = 7  → 0111

### Step 1: XOR

    0101
    0111
    ----
    0010

XOR gives the addition **without carry**.

    0010 = 2

### Step 2: AND

    0101
    0111
    ----
    0101

AND finds the carry.

    0101 = 5

### Step 3: Shift the carry

    0101 << 1
    = 1010

The process continues until there is no carry left.

### Key Idea

    XOR → Sum without carry
    AND → Find carry
    LEFT SHIFT → Move carry

---

## 📌 3. Check if the `i`th Bit Is Set

A bit is **set** when its value is `1`.

### Code

    function isBitSet(N, i) {
        return ((N >> i) & 1) === 1;
    }

### Example

Suppose:

    N = 10

Binary:

    10 = 1010

Bit positions:

    Position:  3 2 1 0
    Bit:       1 0 1 0

Check bit `1`:

    1010 >> 1
    = 0101

Now:

    0101 & 0001
    = 0001

Result is `1`, so bit `1` is set.

### Formula

    (N >> i) & 1

### Important

Bit positions start from the **rightmost bit at index `0`**.

---

## 📌 4. Set the `i`th Bit

Setting a bit means changing it to `1`.

### Formula

    N | (1 << i)

### Example

Suppose:

    N = 8
    8 = 1000

Set bit `1`:

    1 << 1
    = 0010

Now:

    1000
    0010
    ----
    1010

Result:

    1010 = 10

### Function

    function setBit(N, i) {
        return N | (1 << i);
    }

---

## 📌 5. Clear the `i`th Bit

Clearing a bit means changing it to `0`.

### Formula

    N & ~(1 << i)

### Example

Suppose:

    N = 10
    10 = 1010

Clear bit `1`:

    1 << 1
    = 0010

NOT:

    ~0010

Then:

    1010
    1101
    ----
    1000

Result:

    1000 = 8

### Function

    function clearBit(N, i) {
        return N & ~(1 << i);
    }

---

## 📌 6. Toggle the `i`th Bit

Toggling means:

    0 → 1
    1 → 0

We use XOR (`^`) for this.

### Formula

    N ^ (1 << i)

### Example

Suppose:

    N = 10
    10 = 1010

Toggle bit `1`:

    1 << 1
    = 0010

Then:

    1010
    0010
    ----
    1000

Result:

    1000 = 8

### Function

    function toggleBit(N, i) {
        return N ^ (1 << i);
    }

### ⚠️ Important Correction

Do NOT use:

    N ^ (i << i)

The correct formula is:

    N ^ (1 << i)

---

## 📌 7. Remove the Last Set Bit

The last set bit means the **rightmost `1` bit**.

### Formula

    N & (N - 1)

### Example

Suppose:

    N = 12
    12 = 1100

Now:

    N - 1 = 11
    11 = 1011

Perform AND:

    1100
    1011
    ----
    1000

Result:

    1000 = 8

The rightmost `1` has been removed.

### Code

    N = N & (N - 1);

### Why is this useful?

It is commonly used for:

- Counting set bits
- Checking powers of 2
- Bit manipulation problems

---

## 📌 8. Check if a Number Is a Power of 2

A positive number is a power of 2 if it contains **exactly one set bit**.

Examples:

    1  = 0001
    2  = 0010
    4  = 0100
    8  = 1000
    16 = 10000

### Formula

    N > 0 && (N & (N - 1)) === 0

### Function

    function isPowerOfTwo(N) {
        return N > 0 && (N & (N - 1)) === 0;
    }

### Example

For:

    N = 8

Binary:

    8     = 1000
    N - 1 = 0111

Now:

    1000
    0111
    ----
    0000

Therefore:

    N & (N - 1) = 0

So `8` is a power of 2.

### ⚠️ Important JavaScript Syntax

Do not write:

    if (N & (N - 1) === 0)

Instead write:

    if ((N & (N - 1)) === 0)

Also check:

    N > 0

because `0` also satisfies the bitwise condition, but `0` is not a power of 2.

---

## 📌 9. Count the Number of Set Bits

A **set bit** is a bit whose value is `1`.

Example:

    N = 13
    13 = 1101

There are three `1`s.

Therefore:

    Number of set bits = 3

---

## 📌 9.1 Count Set Bits by Checking Every Bit

We can check the last bit using:

    N & 1

Then right-shift the number.

### Code

    function countSetBits(n) {
        let count = 0;

        while (n !== 0) {
            count += n & 1;
            n = n >> 1;
        }

        return count;
    }

### How it works

For:

    13 = 1101

Check the last bit:

    1101 & 0001
    = 0001

Count:

    1

Right shift:

    1101 >> 1
    = 0110

Again:

    0110 & 0001
    = 0000

Continue until the number becomes `0`.

### Important

    n >> 1

means right shift by one position.

For positive numbers, this is approximately equivalent to:

    n / 2

---

## 📌 9.2 Count Set Bits Using `N & (N - 1)`

A more efficient bit manipulation technique is:

    N & (N - 1)

Every time we perform this operation, the **rightmost set bit is removed**.

### Code

    function countSetBits(n) {
        let count = 0;

        while (n !== 0) {
            n = n & (n - 1);
            count++;
        }

        return count;
    }

### Example

Suppose:

    N = 13

Binary:

    13 = 1101

First iteration:

    1101
    1100
    ----
    1100

Count:

    1

Second iteration:

    1100
    1011
    ----
    1000

Count:

    2

Third iteration:

    1000
    0111
    ----
    0000

Count:

    3

Therefore:

    13 has 3 set bits.

### Complexity

If `k` is the number of set bits:

    Time:  O(k)
    Space: O(1)

This is useful because the loop runs once for every `1` bit.

---

# 📌 Important Bitwise Operators

| Operator | Name | Example |
|---|---|---|
| `&` | AND | `a & b` |
| `|` | OR | `a | b` |
| `^` | XOR | `a ^ b` |
| `~` | NOT | `~a` |
| `<<` | Left Shift | `a << 1` |
| `>>` | Right Shift | `a >> 1` |

---

# 📌 Quick Reference

| Operation | Formula |
|---|---|
| Swap two numbers | `a = a ^ b; b = a ^ b; a = a ^ b;` |
| Add without `+` | XOR + AND + Left Shift |
| Check `i`th bit | `(N >> i) & 1` |
| Set `i`th bit | `N | (1 << i)` |
| Clear `i`th bit | `N & ~(1 << i)` |
| Toggle `i`th bit | `N ^ (1 << i)` |
| Remove last set bit | `N & (N - 1)` |
| Check power of 2 | `N > 0 && (N & (N - 1)) === 0` |
| Check last bit | `N & 1` |
| Right shift | `N >> 1` |
| Left shift | `N << 1` |
| Count set bits | `N & (N - 1)` repeatedly |

---

# 🎯 Interview Patterns to Remember

When you see questions involving:

- Binary representation
- Set bits (`1`s)
- Even / odd numbers
- Powers of 2
- XOR
- Swapping numbers
- Missing numbers
- Single number
- Turning bits on/off
- Counting `1`s

Think about **Bit Manipulation** before using a more complicated approach.

---

# 🧠 Most Important Formulas

### Check if `i`th bit is set

    (N >> i) & 1

### Set `i`th bit

    N | (1 << i)

### Clear `i`th bit

    N & ~(1 << i)

### Toggle `i`th bit

    N ^ (1 << i)

### Remove the last set bit

    N & (N - 1)

### Check if number is power of 2

    N > 0 && (N & (N - 1)) === 0

### Count set bits

    while (N !== 0) {
        N = N & (N - 1);
        count++;
    }