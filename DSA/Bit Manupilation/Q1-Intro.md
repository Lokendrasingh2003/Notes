# Bit Manipulation

## 1. What is Bit Manipulation?

Bit manipulation means performing operations directly on the binary representation of numbers.

Computers store integers using bits:

    0 or 1

Example:

    Decimal 5

    Binary:
    101

Each position represents a power of 2:

    1  0  1
    ↑  ↑  ↑
    4  2  1

So:

    4 + 0 + 1 = 5

Bit manipulation is commonly used in:

- DSA
- Competitive programming
- System programming
- Operating systems
- Cryptography
- Networking
- Permission systems
- Performance-sensitive code

---

# 2. Binary Basics

Each bit position represents a power of 2.

Example:

    8 4 2 1
    1 0 1 1

This represents:

    8 + 0 + 2 + 1 = 11

Therefore:

    11 = 1011

### Common powers of 2

    2^0 = 1
    2^1 = 2
    2^2 = 4
    2^3 = 8
    2^4 = 16
    2^5 = 32
    2^6 = 64
    2^7 = 128

---

# 3. Bitwise Operators

The main bitwise operators are:

    &   AND
    |   OR
    ^   XOR
    ~   NOT
    <<  Left Shift
    >>  Right Shift

JavaScript also provides:

    >>> Unsigned Right Shift

---

# 4. Bitwise AND (&)

AND returns `1` only when both bits are `1`.

Truth table:

    A   B   A & B

    0   0    0
    0   1    0
    1   0    0
    1   1    1

Example:

    5 = 101
    3 = 011

    101
    011
    ---
    001

Therefore:

    5 & 3 = 1

### Important use

AND is commonly used to:

- Check whether a bit is set
- Extract bits
- Create masks
- Check odd/even numbers

---

# 5. Check Odd or Even

The least significant bit tells whether an integer is odd or even.

Even numbers:

    2  = 10
    4  = 100
    6  = 110
    8  = 1000

Their last bit is:

    0

Odd numbers:

    1  = 1
    3  = 11
    5  = 101
    7  = 111

Their last bit is:

    1

Therefore:

    n & 1

If result is:

    0 → Even
    1 → Odd

Example:

    7 & 1

    111
    001
    ---
    001

Result:

    1

Therefore 7 is odd.

---

# 6. Bitwise OR (|)

OR returns `1` if at least one bit is `1`.

Truth table:

    A   B   A | B

    0   0    0
    0   1    1
    1   0    1
    1   1    1

Example:

    5 = 101
    3 = 011

    101
    011
    ---
    111

Therefore:

    5 | 3 = 7

### Common use

OR is useful when you want to:

> Set a specific bit to 1.

---

# 7. Bitwise XOR (^)

XOR returns `1` when the bits are different.

Truth table:

    A   B   A ^ B

    0   0    0
    0   1    1
    1   0    1
    1   1    0

Example:

    5 = 101
    3 = 011

    101
    011
    ---
    110

Therefore:

    5 ^ 3 = 6

---

# 8. Important XOR Properties

These properties are extremely important for DSA.

### Property 1

    x ^ 0 = x

Example:

    5 ^ 0 = 5

### Property 2

    x ^ x = 0

Example:

    5 ^ 5 = 0

### Property 3

XOR is commutative:

    a ^ b = b ^ a

### Property 4

XOR is associative:

    (a ^ b) ^ c = a ^ (b ^ c)

These properties make XOR useful for finding unique elements.

---

# 9. Find the Number Appearing Once

Problem:

Every number appears twice except one number.

Example:

    [2, 3, 4, 2, 4]

Answer:

    3

Use XOR:

    2 ^ 3 ^ 4 ^ 2 ^ 4

Rearrange:

    (2 ^ 2) ^ (4 ^ 4) ^ 3

    0 ^ 0 ^ 3

    = 3

### JavaScript

    function singleNumber(nums) {
      let result = 0;

      for (const num of nums) {
        result ^= num;
      }

      return result;
    }

Time:

    O(n)

Space:

    O(1)

---

# 10. Bitwise NOT (~)

NOT flips every bit.

    0 → 1
    1 → 0

Example conceptually:

    1010
     ↓
    0101

However, JavaScript bitwise operations use signed 32-bit integer representations, so the result can look surprising.

Important identity:

    ~x = -(x + 1)

Example:

    ~5 = -6

---

# 11. Left Shift (<<)

Left shift moves bits to the left.

Example:

    5 = 0101

    5 << 1

    1010

    = 10

So:

    5 << 1 = 10

Generally:

    x << k

approximately corresponds to:

    x × 2^k

for values where signed 32-bit behavior does not cause issues.

Example:

    3 << 2

    0011
    ↓
    1100

    = 12

---

# 12. Right Shift (>>)

Right shift moves bits to the right.

Example:

    8 = 1000

    8 >> 1

    0100

    = 4

Generally:

    x >> k

approximately corresponds to:

    x / 2^k

for non-negative integers.

Example:

    16 >> 2

    = 4

---

# 13. Unsigned Right Shift (>>>)

JavaScript also has:

    >>>

It shifts bits to the right and fills the left side with zeros.

This matters particularly when working with negative 32-bit integers.

Example:

    x >>> 0

is commonly used to interpret a JavaScript bitwise result as an unsigned 32-bit integer.

---

# 14. What is a Bit Mask?

A bit mask is a number whose binary representation is used to select or modify specific bits.

Example:

    00001000

The mask targets one particular bit.

Masks are heavily used in:

- Permission systems
- Flags
- Subsets
- Bit checking
- Competitive programming

---

# 15. Check if a Bit is Set

Suppose we want to check the `k`th bit of `n`.

Use:

    n & (1 << k)

Example:

    n = 10

Binary:

    1010

Check bit position 1:

    1 << 1
    = 0010

Then:

    1010
    0010
    ----
    0010

Result is non-zero.

Therefore bit 1 is set.

### Formula

    (n & (1 << k)) != 0

---

# 16. Set a Bit

To set the kth bit to `1`:

    n | (1 << k)

Example:

    n = 8

    1000

Set bit 1:

    1 << 1
    = 0010

Then:

    1000
    0010
    ----
    1010

Result:

    10

---

# 17. Clear a Bit

To clear the kth bit:

    n & ~(1 << k)

Example:

    n = 10

    1010

Clear bit 1:

    mask:

    0010

    NOT:

    ...1101

Then:

    1010
    1101
    ----
    1000

Result:

    8

---

# 18. Toggle a Bit

Toggle means:

    0 → 1
    1 → 0

Use XOR:

    n ^ (1 << k)

Example:

    n = 10

    1010

Toggle bit 1:

    0010

    1010
    0010
    ----
    1000

Result:

    8

---

# 19. Remove the Lowest Set Bit

This is one of the most important bit manipulation tricks.

Formula:

    n & (n - 1)

Example:

    n = 12

Binary:

    1100

    n - 1:

    1011

Now:

    1100
    1011
    ----
    1000

The lowest set bit was removed.

---

# 20. Count Set Bits

A set bit means a bit containing `1`.

Example:

    13 = 1101

Number of set bits:

    3

### Simple approach

Check every bit.

    function countBits(n) {
      let count = 0;

      while (n > 0) {
        count += n & 1;
        n >>= 1;
      }

      return count;
    }

Time:

    O(log n)

---

# 21. Brian Kernighan's Algorithm

A better bit trick:

    n = n & (n - 1)

Each operation removes one set bit.

Example:

    n = 13

    1101
    ↓
    1100
    ↓
    1000
    ↓
    0000

There were 3 set bits.

### JavaScript

    function countSetBits(n) {
      let count = 0;

      while (n !== 0) {
        n &= n - 1;
        count++;
      }

      return count;
    }

Time:

    O(number of set bits)

This can be faster than checking every bit when the number has relatively few set bits.

---

# 22. Check if Number is a Power of 2

Powers of 2 have exactly one set bit.

Examples:

    1  = 0001
    2  = 0010
    4  = 0100
    8  = 1000
    16 = 10000

Use:

    n > 0 && (n & (n - 1)) === 0

Example:

    8

    1000
    0111
    ----
    0000

Therefore 8 is a power of 2.

But:

    10

    1010
    1001
    ----
    1000

Not zero.

Therefore 10 is not a power of 2.

---

# 23. Find the Lowest Set Bit

Formula:

    n & -n

Example:

    n = 12

    12 = 1100

    -12 uses two's-complement representation.

Result:

    1100
    ↓
    0100

So:

    12 & -12 = 4

This extracts the value of the lowest set bit.

---

# 24. Two's Complement

Computers commonly represent signed integers using two's complement.

To calculate `-x`:

    Invert bits
    +
    Add 1

Conceptually:

    x
    ↓
    ~x
    ↓
    ~x + 1
    ↓
    -x

This is why:

    n & -n

can isolate the lowest set bit.

---

# 25. Swap Two Numbers Using XOR

Classic bit manipulation trick:

    a = a ^ b
    b = a ^ b
    a = a ^ b

Example:

    a = 5
    b = 3

After the operations:

    a = 3
    b = 5

### Important

In modern JavaScript, this technique is mostly educational.

A clearer approach is:

    [a, b] = [b, a]

Use XOR swapping mainly to understand XOR properties and interview questions.

---

# 26. XOR and Missing Number

Problem:

Given numbers from `0` to `n`, one number is missing.

Example:

    [3, 0, 1]

Expected:

    0, 1, 2, 3

Missing:

    2

XOR all indexes and values:

    0 ^ 1 ^ 2 ^ 3
    ^
    3 ^ 0 ^ 1

Everything cancels except:

    2

### JavaScript

    function missingNumber(nums) {
      let result = nums.length;

      for (let i = 0; i < nums.length; i++) {
        result ^= i;
        result ^= nums[i];
      }

      return result;
    }

Time:

    O(n)

Space:

    O(1)

---

# 27. XOR for Two Unique Numbers

Suppose every number appears twice except two numbers.

Example:

    [1, 2, 1, 3, 2, 5]

Unique numbers:

    3 and 5

First XOR everything:

    1 ^ 2 ^ 1 ^ 3 ^ 2 ^ 5

Duplicates cancel:

    3 ^ 5

Now:

    result = 3 ^ 5

Find one set bit:

    diff = result & -result

This bit differs between 3 and 5.

Use that bit to divide numbers into two groups.

Then XOR each group separately.

This gives:

    3
    5

This is a common advanced XOR interview problem.

---

# 28. Bit Manipulation for Permissions

Bit manipulation is useful for representing multiple boolean permissions inside one integer.

Suppose:

    READ    = 1 << 0
    WRITE   = 1 << 1
    DELETE  = 1 << 2
    ADMIN   = 1 << 3

Then:

    READ
    = 0001

    WRITE
    = 0010

    DELETE
    = 0100

    ADMIN
    = 1000

User with:

    READ + WRITE

can be represented as:

    0001
    0010
    ----
    0011

Check permission:

    permissions & WRITE

If non-zero:

    WRITE permission exists.

---

# 29. Bit Flags

Bit flags are useful when multiple independent boolean values need to be stored compactly.

Example:

    IS_ACTIVE = 1 << 0
    IS_ADMIN  = 1 << 1
    IS_VERIFIED = 1 << 2

Combined:

    flags = IS_ACTIVE | IS_VERIFIED

Check:

    if (flags & IS_VERIFIED) {
      // verified
    }

Set:

    flags |= IS_ADMIN

Clear:

    flags &= ~IS_ADMIN

Toggle:

    flags ^= IS_ADMIN

---

# 30. Subsets Using Bitmasking

Bit manipulation can represent subsets.

Suppose:

    [A, B, C]

There are:

    2^3 = 8

possible subsets.

Represent each subset using 3 bits.

Example:

    000 → {}
    001 → {A}
    010 → {B}
    011 → {A, B}
    100 → {C}
    101 → {A, C}
    110 → {B, C}
    111 → {A, B, C}

Each bit tells whether an element is included.

    0 → not included
    1 → included

---

# 31. Generate All Subsets Using Bitmasking

For an array of size `n`, iterate:

    0 → 2^n - 1

For every number, inspect each bit.

Example:

    nums = [1, 2, 3]

For:

    mask = 5

Binary:

    101

Bit positions:

    bit 0 → include 1
    bit 1 → don't include 2
    bit 2 → include 3

Subset:

    [1, 3]

### Complexity

Number of subsets:

    2^n

Time:

    O(n × 2^n)

---

# 32. Important Bit Tricks

### Check odd

    n & 1

### Check kth bit

    n & (1 << k)

### Set kth bit

    n | (1 << k)

### Clear kth bit

    n & ~(1 << k)

### Toggle kth bit

    n ^ (1 << k)

### Remove lowest set bit

    n & (n - 1)

### Get lowest set bit

    n & -n

### Check power of 2

    n > 0 && (n & (n - 1)) === 0

### XOR with itself

    n ^ n = 0

### XOR with zero

    n ^ 0 = n

---

# 33. JavaScript Bitwise Operations

Important JavaScript-specific point:

JavaScript `number` values are normally IEEE-754 double-precision floating-point numbers.

However, bitwise operators convert their operands to signed 32-bit integers for the operation.

Therefore:

    & 
    |
    ^
    ~
    <<
    >>
    >>>

operate using 32-bit integer representations.

This matters when working with:

- Large integers
- Negative numbers
- Values above the 32-bit range

For integers larger than JavaScript's safe integer range, consider `BigInt`, but remember that `BigInt` and `Number` cannot be freely mixed.

---

# 34. Common DSA Patterns

Bit manipulation frequently appears in:

### Pattern 1

Find unique element.

Use:

    XOR

### Pattern 2

Find missing number.

Use:

    XOR

### Pattern 3

Check power of 2.

Use:

    n & (n - 1)

### Pattern 4

Count set bits.

Use:

    n &= n - 1

### Pattern 5

Check/set/clear a bit.

Use:

    AND
    OR
    XOR
    NOT

### Pattern 6

Generate subsets.

Use:

    Bitmasking

### Pattern 7

Store multiple boolean states.

Use:

    Bit flags

---

# 35. Common Mistakes

### Mistake 1

Confusing:

    &
    
with:

    &&

`&` is bitwise AND.

`&&` is logical AND.

---

### Mistake 2

Confusing:

    |

with:

    ||

`|` is bitwise OR.

`||` is logical OR.

---

### Mistake 3

Thinking XOR means "OR".

XOR means:

> 1 when the bits are different.

---

### Mistake 4

Forgetting zero in power-of-two checks.

Use:

    n > 0 && (n & (n - 1)) === 0

because:

    0 & -1 = 0

but zero is not a power of 2.

---

### Mistake 5

Ignoring JavaScript's 32-bit bitwise behavior.

Bitwise operations are not arbitrary-precision operations on JavaScript `Number` values.

---

# 36. Interview Questions

## Q1. What is bit manipulation?

Bit manipulation is the process of directly operating on individual bits of a number using operations such as AND, OR, XOR, NOT, and bit shifts.

---

## Q2. How do you check whether a number is odd?

Use:

    n & 1

If the result is `1`, the number is odd.

---

## Q3. How do you check whether a bit is set?

Use:

    n & (1 << k)

If the result is non-zero, the kth bit is set.

---

## Q4. How do you set a bit?

Use:

    n | (1 << k)

---

## Q5. How do you clear a bit?

Use:

    n & ~(1 << k)

---

## Q6. How do you toggle a bit?

Use:

    n ^ (1 << k)

---

## Q7. How do you remove the lowest set bit?

Use:

    n & (n - 1)

---

## Q8. How do you find the lowest set bit?

Use:

    n & -n

---

## Q9. How do you check if a number is a power of 2?

Use:

    n > 0 && (n & (n - 1)) === 0

because powers of 2 contain exactly one set bit.

---

## Q10. Why is XOR useful for finding a unique number?

Because:

    x ^ x = 0

and:

    x ^ 0 = x

Therefore duplicate numbers cancel each other out.

---

## Q11. What is a bitmask?

A bitmask is a number whose binary representation is used to select, check, set, clear, or represent specific bits.

---

## Q12. What is bit shifting?

Bit shifting moves bits left or right.

    x << k

moves bits left.

    x >> k

moves bits right.

For suitable non-negative integer values, shifting left/right by `k` roughly corresponds to multiplying/dividing by `2^k`.

---

# 37. Quick Revision

    AND (&)
    → Both bits must be 1

    OR (|)
    → At least one bit is 1

    XOR (^)
    → Bits must be different

    NOT (~)
    → Flip bits

    Left Shift (<<)
    → Move bits left

    Right Shift (>>)
    → Move bits right

    Odd:
    → n & 1

    Check bit:
    → n & (1 << k)

    Set bit:
    → n | (1 << k)

    Clear bit:
    → n & ~(1 << k)

    Toggle bit:
    → n ^ (1 << k)

    Remove lowest set bit:
    → n & (n - 1)

    Lowest set bit:
    → n & -n

    Power of 2:
    → n > 0 && (n & (n - 1)) === 0

    Unique number:
    → XOR

    Subsets:
    → Bitmasking

---

# 38. One-Line Interview Summary

> Bit manipulation is the technique of operating directly on the binary representation of numbers using AND, OR, XOR, NOT, and shift operations, enabling efficient solutions for problems involving bits, masks, flags, unique elements, powers of two, and subsets.