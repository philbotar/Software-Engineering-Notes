# Adapting Kadane to Circular Arrays
So, what do we do when the array is circular? Take a look at this picture from the official answer
image.png

By minimizing the middle subarray, we necessarily maximize the subarray surrounding that subarray, i.e., the circular one. The smaller the middle subarray we get, the bigger the circular array around it will be because the circular array sum is just array sum - middle sum.

So if the minimum subarray is just the entire array, no circular array is better. If there were any better circular subarray, then the minimum middle array would not contain all elements because the subarray's sum could be decreased by removing those elements. Therefore if the minimum is simply the entire array, we can throw out all circular arrays and just evaluate this normally. So we return max so far, which is the answer if the array weren't circular.

If the minimum subarray isn't the entire array, the greatest subarray may be circular. Note that I said may be: all we know is that there is a circular subarray greater than the minimum. So we simply return the max of our normal subarray and the max circular subarray, which, as explained earlier, is array - middle (minimum) sum

Here's the final answer:

```python
    class Solution:
        def maxSubarraySumCircular(self, nums: List[int]) -> int:
            if len(nums) == 1:
                return nums[0]

            #minimum = minimum middle array
            #maximum = maximum array if the problem weren't circular
            minimum = maximum = total = cur_max = cur_min = nums[0]
            for n in nums[1:]:
                # keep track of total array sum
                total += n

                # keep track of current minimum subarray and maximum
                # this is just kadane from the previous problem adjusted a bit because now we have negative numbers
                cur_min = min(cur_min+n, n)
                cur_max = max(cur_max+n, n)

                # update the global min and max
                minimum = min(cur_min, minimum)
                maximum = max(cur_max, maximum)

            # The entire array is the minimum sum, so a better circular cannot exist.
            # Simply return the best subarray if the problem weren't circular
            if minimum == total:
                return maximum

            # The greatest subarray may be circular, or it may not me
            # Return the max of the best non-circular and circular subarray
            return max(maximum, total-minimum)
```

Remember, total-minimum is the best circular array sum, strictly excluding any non-circular answers
