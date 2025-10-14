# Triplet Sum

### Problem

Given an array of integers, return all triplets (a, b, c] such that a + b + c = 0. The
solution must not contain duplicate triplets (e.g., [1, 2, 3] and [2, 3, 1] are considered
duplicate triplets), If no such triplets are found, return an empty array.

Each triplet can be arranged in any order, and the output can be returned in any order.

Example:
Input nums = [0, -1, 2, -3, 1]
Output [[-3, 1, 2], [-1, 0, 1]]

### Intuition

From what we see in the problem statement, we know we need to

1. Get all possible combinations of 3 values that equal to zero
2. We need to ensure no duplicates are returned.
3. There can be multiple of the same value
4. That we need to sort the array to have predictable dynamics.

We know aswell from the Pair Sum Problem, that we can actually leverage the two sum solution with a pivot point. We can set a pivot, and perform a pair sum on the rest of the array, and check the condition if they all add up to 0.

Another point, if we reach a point where all 3 values are positive and not all zero, we know that we can exit early, as its not possible to sum up to 0 when there are no negatives and not all zero.

Aswell, we need to ensure that we are not using the same duplicated value twice as an anchor. The previous iteration has already gotten/checked possible combinations of triplets.

### Implementation

1. Intialise your data structure to store all triplets.
2. Create a for loop (This is for your anchor)
   1. Check that if the index isnt 0 and it equals the previous arrays value, we skip until it doesn't.
   2. Then we initialise left and right indexs, at the index after the for loops index for left, and end of the array for right.
   3. Then we initialise a while loop, while left is less than right
      1. Check that left - 1 is larger than index and equals previous, increment left. (Avoids duplicates from the left check).
      2. Then check if you can have a valid triplet from index, left and right.
      3. If so, add to the data structure.
      4. Check that there is at least one negative value. We did this check AFTER the check in case theyre all zeroes.
      5. Increment left, decrement right.
3. Once the for loop finishes, return the data structure.


### Complexity

Time Complexity is O(n^2).

1. Sort the Array in O(n log(n)) time.
2. For each value in the array, perform the inward traversal, so O(n^2)

So to add them together, it is O(n^2).


Space Complexity is O(n) due to the space taken up by the sorting algorithm. If the space complexity on if it includes the output array, then it is O(n^2) in the worst case as we can add n pairs to the output.


### Tests


| Test                        | Testcase        |
| ----------------------------- | ----------------- |
| No values in Array          | []              |
| Less than 3 values in array | [1,2]           |
| Only negatives              | [-1,-2,-3]      |
| Only Positives              | [1,2,3]         |
| Only Zeroes                 | [0,0,0]         |
| With Duplicates             | [-1,-1,0,0,1,1] |
