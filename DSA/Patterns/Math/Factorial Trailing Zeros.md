# Problem

Given an integer n, return the number of trailing zeroes in n!.

Note that n! = n _ (n - 1) _ (n - 2) _ ... _ 3 _ 2 _ 1.

Example 1:

Input: n = 3
Output: 0
Explanation: 3! = 6, no trailing zero.

### Intuition

We can leverage the use of 5's within an equation to know the number of trailing zeros.

Imagine we have n = 30. We want to find all factors of 5 in 30!.

1. Count all multiples of 5.

- Numbers: 5,10,15,20,25,30: So we can find this with 30/5. Our count at this point is 6.

2. Fix the Mistake (Find the extra Factors)

- Our count is wrong. We know the answer for 25! was 6, so for 30! it must be even higher (it's 7, because of 30).The problem is 25. Our first step only counted it once. But 25 = 5 \* 5, so it should be counted twice.
- So we need to find the numbers with a second factor of 5, which are all multiples of 25.

3. Get the total factors, which are Counts of Multiples of 5 + Counts of Multiples of 25

- This means for 30!, the answer is 6 + 1 = 7.

### Code

```java
    if (n < 0) {
        return 0;
    }

    int zeroCount = 0;
    long powerOf5 = 5; // Use long to avoid overflow for large n

    while (n >= powerOf5) {
        zeroCount += n / powerOf5;
        // Prevent overflow when calculating the next power of 5
        if (powerOf5 > Integer.MAX_VALUE / 5) {
            break;
        }
        powerOf5 *= 5;
    }
    return zeroCount;
```
