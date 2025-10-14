# Pair Sum - Sorted

### Problem

Given an array of integers sorted in ascending order and a target value, return the indexes
of any pair of numbers in the array that sum to the target. The order of the indexes in the
result doesn't matter. If no pair is found, return an empty array.

Example 1:
Input nums = [-5, -2, 3, 4, 6], target = 7
Output [2, 3]
Explanation: nums[2] + nums[3] = 3 + 4 = 7

### Intuition

On first glance, you notice that you have a couple things.

1. A sorted Array. This gives us a predictable dynamic to work with.
2. That we need 2 values from that array to meet a condition.
3. We know that we need to find all possible pairs of numbers until we meet the condition.

So we can use the inward traversal to find these possible pairs.


#### Implementation

1. Initialise Left and Right, where left is the 0th index, and right is the last index.
2. Have a while loop, that continues on the condition that the left and right indexes are strictly larger than one another. Within the while loop
   1. Initialise currentSummedValue = array[left] + array[right]
   2. Check if the currentSummedValue equals to the target, if it does, return answer immediately.
   3. Increment left if the currentSummedValue is too small
   4. Else, decrement right.
3. If the for loop completes, return an empty array/response.


#### Complexity Analysis

Time complexity is O(n). We visit each value at most once, and never need to go back to.

Space Complexity is O(1). This is because we only need the pointers, it doesn't grow by the size of the input.


#### Test Cases


| Test                  | Input            |
| :---------------------- | :----------------- |
| No Values             | [ ]              |
| One Value             | [1]              |
| Normal Values         | [-1,-2,0,1,2,5]  |
| Only Negatives        | [-1,-2,-3,-4,-5] |
| Only Positives        | [1,2,3,5,6]      |
| No Possible Answer    | [-1,0,1,2] t = 5 |
| Both Negative         | [-2,-1] t = -3   |
| Negative and Positive | [-3,0,6] t = 3   |
