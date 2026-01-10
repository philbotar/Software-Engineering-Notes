# Find Median of Two Sorted Arrays

Given two sorted arrays nums1 and nums2 of size m and n respectively, return the median of the two sorted arrays.
The overall run time complexity should be O(log (m+n)).

Example 1:

Input: nums1 = [1,3], nums2 = [2]
Output: 2.00000
Explanation: merged array = [1,2,3] and median is 2.
Example 2:

Input: nums1 = [1,2], nums2 = [3,4]
Output: 2.50000
Explanation: merged array = [1,2,3,4] and median is (2 + 3) / 2 = 2.5.

### Intuition

For finding the median of two arrays we have a couple ways to do it.

First is Merge Sort. Since both arrays are sorted already for us, all we need to do is make a decision at each possible insert. Its a simple check on whether the nums1 value is smaller or the nums2. If 1 is smaller, put that in and advance, otherwise put in the nums2 value and advance. The only caveat is ensuring that you add the last parts of the array. This is NOT O(log (m + n)), this is O(m+n) and is not correct.

Secondly is using two priority queues and adding values to a min and max queue, this is good for finding medians that are being streamed in, but again is O(m + n).

So the actual way is to use Partitioning and Binary Search.
Clues to using Binary Search was the O(log (m + n)),.

### Implentation Steps

Step by Step Algorithm

1. Ensure nums1 is the smaller array:

```python
if len(nums1) > len(nums2):
    return self.findMedianSortedArrays(nums2, nums1)
```

We always perform binary search on the shorter array to minimize the search space.

2. Initialize variables:

```python
len1, len2 = len(nums1), len(nums2)
left, right = 0, len1
```

3. Binary Search Loop on nums1:

```python
while left <= right:
```

At each iteration:

a. Partition both arrays:

```python
part1 = (left + right) // 2
part2 = (len1 + len2 + 1) // 2 - part1
```

We divide the combined array into two halves such that the total number of elements in the left half is (len1 + len2 + 1) // 2.
part1 is the partition index in nums1, and part2 is in nums2.

b. Get max of left parts and min of right parts:

```python
max_left1 = float('-inf') if part1 == 0 else nums1[part1 - 1]
min_right1 = float('inf') if part1 == len1 else nums1[part1]

max_left2 = float('-inf') if part2 == 0 else nums2[part2 - 1]
min_right2 = float('inf') if part2 == len2 else nums2[part2]
```

These values define the current virtual "cut" in the combined array:

```python
[ ... max_left1 | min_right1 ... ]
[ ... max_left2 | min_right2 ... ]
```

c. Check if the partition is correct:

```python
if max_left1 <= min_right2 and max_left2 <= min_right1:
```

If so, you've found the correct partition.

- If the total number of elements is even:

```python
return (max(max_left1, max_left2) + min(min_right1, min_right2)) / 2
```

- If odd:

```python
return max(max_left1, max_left2)
```

d. Adjust binary search range:
If the partition is not correct, move the binary search range:

```python
elif max_left1 > min_right2:
    right = part1 - 1
else:
    left = part1 + 1
```
