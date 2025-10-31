# Lonely Integer

Given an integer array where each number occurs twice except for one of them, find the unique number

Example:
Input: nums = [1,3,3,2,1]
Output: 2

Constraints:

- nums contains at least one element

### Intuition

Whilst using a hashmap is the usual intuitive approach, we can utilise the not operator to find the lonely integer.

By using ~ on each number, we know were cancelling each out, being leftover with the final value.

Think of it like this

XOR all elements: 1 ^ 3 ^ 3 ^ 2 ^ 1
= (1 ^ 1) ^ (3 ^ 3) ^ 2
= 0 ^ 0 ^ 2
= 2

This allows us to find the lonely integer in linear time without using extra space.

### Code

```python

def lonely_integer(nums: List[int]) -> int:
    res = 0

    # XOR each element of the array so that duplicate values will cancel each other out (x ^ x == 0)
    for num in nums:
        res ^= num

    return res

```

### Complexity

TC: O(n)
SC: O(1)
