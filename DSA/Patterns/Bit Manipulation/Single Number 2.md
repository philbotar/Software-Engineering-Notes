# Single Number 2

Given an integer array nums where every element appears three times except for one, which appears exactly once. Find the single element and return it.

You must implement a solution with a linear runtime complexity and use only constant extra space.

Example 1:

Input: nums = [2,2,3,2]
Output: 3

### Inutition

The intuition behind this to first iterate over all bits, and find out for each bit how many values share that bit. Once we get all the times that bit has been shared, if we modulo 3 on it and theres a bit remaining, we know thats from the single number.

So once we have that bit we move it to the correct location by bitShifting and do an OR with the result. This means we OR each location that has the extra bit thats been modulo'd, and end up with the resulting number.

### Code

```java
class Solution {
    public int singleNumber(int[] nums) {
        // Loner
        int loner = 0;

        // Iterate over all bits
        for (int shift = 0; shift < 32; shift++) {
            int bitSum = 0;

            // For this bit, iterate over all integers
            for (int num : nums) {
                // Compute the bit of num, and add it to bitSum
                bitSum += (num >> shift) & 1;
            }

            // Compute the bit of loner and place it
            int lonerBit = bitSum % 3;
            loner = loner | (lonerBit << shift);
        }
        return loner;
    }
}
```
