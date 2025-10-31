# Reverse Bits

## Problem Description

Given a 32-bit unsigned integer, reverse the bits and return the result.

## Approach

The solution uses bit manipulation to reverse the bits of a 32-bit integer.

### Algorithm

1. Initialize `result = 0` to store the reversed bits
2. Iterate 32 times (for each bit position)
3. For each iteration:
   - Left shift `result` by 1 to make room for the next bit
   - Extract the least significant bit of `n` using `n & 1`
   - Add this bit to `result` using OR operation
   - Right shift `n` by 1 to process the next bit

### Code

```java
class Solution {
    public int reverseBits(int n) {
        int result = 0;

        for(int i = 0; i < 32; i++){
            result = (result << 1) | (n & 1);
            n >>= 1;
        }
        return result;
    }
}
```

### Time Complexity

O(1) - Always processes exactly 32 bits

### Space Complexity

O(1) - Uses constant extra space

### Key Concepts

- **Bit shifting**: `<<` (left shift), `>>` (right shift)
- **Bitwise AND**: `n & 1` extracts the least significant bit
- **Bitwise OR**: `|` combines bits
