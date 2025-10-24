# Maximum Subarray Sum

Given an array of integers, return the sum of the subarray with the largest sum. 

Input: nums = [3, 1, -6, -2, -1, 4, -9]
Output: 5

Constraints: The array has at least one element.


### Intuition 
What this problem is, is that its just Kadanes Algorithm. Kadanes algorithm if you remember is that in order to find the maximum subarray within an array, you do a single pass, and check if the current subarray value we have is larger or smaller than the current. If its smaller we reset. 

So how is this a dynamic programming solution? 
- Because we are needing the optimal solutions for each value before. When you need to know which is better, the optimal of (...n) or the current, thats our recurrence relation. 
- max_subarray(i) = max(max_subarray(i - 1) + nums[i], nums[i])

### Base Case
The base case for this is the first value being set as max. 

### Complexity
TC: O(n), a single pass
SC: O(1), only need the max value 

### Code

```python
def maximum_subarray_sum_dp_optimized(nums: List[int]) -> int: 
    n = len(nums)
    if n == 0:
        return 0
    current_sum = nums[0]
    max_sum = nums[0]
    for i in range(1, n): 
        current_sum = max(nums[i], current_sum + nums[i])
        max_sum = max(max_sum, current_sum)
    return max_sum 
```

