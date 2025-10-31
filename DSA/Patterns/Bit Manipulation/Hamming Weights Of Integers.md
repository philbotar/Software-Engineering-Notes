# Hamming Weights of Integers

The hamming weight of a number is the number of set bits (1 bits) in its binary representations. Given a positive integer n, return an array where the ith element is the Hamming Weight of integer i for all integers from 0 to n.

Input: n = 7
Output: [0, 1, 1, 2, 1, 2, 2, 3]

| Number | Binary Representation | Number of Set Bits |
| ------ | --------------------- | ------------------ |
| 0      | 0                     | 0                  |
| 1      | 1                     | 1                  |
| 2      | 10                    | 1                  |
| 3      | 11                    | 2                  |
| 4      | 100                   | 1                  |
| 5      | 101                   | 2                  |
| 6      | 110                   | 2                  |
| 7      | 111                   | 3                  |

### Intuition

As a simple way to figure out how to get the hamming weights, we know that if we do an AND on each 1 with another 1, we'll know for certain that its a 1.

Knowing this, this means for each number we can do a right shift with an & on 1 to find out if that value is a 1 or not. The good thing about doing a right shift is that it means we can go to the next value until its 0.

So we:

1. Iterate from 1 to n
2. while the value is > 0, compare its current rightmost bit to 1.
3. If theyre both 1, add to count.

This is the simple way of doing it, but we know that theres an optimal substructure that we can use.

### Intuition: Dynamic Programming

What we can do is that we can make an optimal substrcutre by treating the results from integers 0 to x - 1 as potential subproblems of x.

Let dp[x] represent the number of set bits in integer x

In order to access a subproblem of dp[x] is to right shift x by 1, removing its Least Significant Bit. This is the subproblem dp[x >> 1], and the only difference between this an dp[x] is the LSB which was just removed.

So, now that we know that we check the previous value, we just need to know if the current bit were at is 1 and add it to the number of 1s at dp[x >> 1].

- If the LSB of x is 0, there is no difference between the number of bits between x and x >> 1
  x = 1 1 0 0 0, dp[x] = 2
  x >> 1 = 0 1 1 0 0 0, dp[x >> 1] = 2

- If the LSB of x is 1, the difference in the number of bits between x and x >> 1 is 1
  x = 1 1 0 0 1 (LSB being the last bit), dp[x] = 3
  x >> 1 = 0 1 1 0 0, dp[x >> 1] = 2

Therefore we can get dp[x] from dp[x >> 1] and adding the LSB to it.

dp[x] = dp[x >> 1] + (x & 1)

### Base Case

The simplest case is when there is no set bits, so the number of set bits is 0. We can apply this base case by setting dp[0] to 0.

After the base case is set, we set the rest of the DP array by applying our formula from dp[1] to dp[n]. The problem is then just the values in the DP array, containing the number of set bits for each number from 0 to n.

### Complexity: DP

TC: O(n) since we populate each element of the DP array once.
SC: The space complexity is O(1) because no extra space is used, aside from the space taken up by the output??

### Code: Dynamic Programming

```python
    def hamming_weights_of_integers_dp(n: int) -> List[int]:
        # Base Case: the number of set bits in 0 is just 0. We set dp[0] to 0 by initialising the entire DP array to 0.
        dp = [0] * (n + 1)
        for x in range(1, n + 1):
            # 'dp[x]' is obtained using the result of 'dp[x >> 1]', plus the LSB of 'x'
            dp[x] = dp[x >> 1] + (x & 1)

        return dp
```
