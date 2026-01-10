# Bitwise AND on a Range

Given two integers left and right that represent the range [left, right], return the bitwise AND of all numbers in this range, inclusive.

Example 1:

Input: left = 5, right = 7
Output: 4
Example 2:

Input: left = 0, right = 0
Output: 0
Example 3:

Input: left = 1, right = 2147483647
Output: 0

### Intuition

This is a trick question. because you know that each value thats below the shared prefix will be either zero or one, you run off the fact that there'll only be shared on the larger numbers that have the same prefix.

So when you code this, youre just right shifting till both values equal one another, and then left shift the bits you shifted by to get the value.

### Code

```java
public int rangeBitwiseAnd(int m, int n) {
    int shift = 0;
    // find the common 1-bits
    while (m < n) {
        m >>= 1;
        n >>= 1;
        ++shift;
    }
    return m << shift;
}
```


### Example

4 = 100 
7 = 111

If we shift both
4 -> 2
7 -> 3

Since 2 < 3, we shift it again.

2 -> 1
3 -> 1

Now that they equal, we know to shift that 1 by the number of values we shifted right to.

1 -> 100
1 -> 100

100 is 4, so we return 4. 