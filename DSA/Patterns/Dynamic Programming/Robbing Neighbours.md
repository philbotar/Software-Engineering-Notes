# Robbing Neighbours

You plan to rob houses in a street where each house stores a certain amount of money. The neighborhood has a security system that sets off an alarm when two adjacent houses are robbed. Return the maximum amount of cash that can be stolen without triggering the alarms.

Example:
Input: houses = [200, 300, 200, 50]
Output: 400

Explanation: Stealing from the houses at indexes O and 2 yields 200 + 200 = 4OO dollars.

### Intuition

So when you think of the optimal substructure of this problem, you realise in order to make the best decision about which house to rob, you need to know if the sum of 2 houses before current plus current or the house immediately before.

This makes it so your recurrence relation is:

dp[i] = max(dp[i - 2] + house[i], dp[i - 1])

and your base cases is the first house dp value being itself, and house 2 being max between house 1 and house 2. You then go on from house 3 onwards.

### Implementation

```python
    from typing import List

    def neighborhood_burglary(houses: List[int]) -> int:
        # Handle the cases when the array is less than the size of 2 to
        # avoid out-of-bounds errors when assigning the base case values.
        if not houses:
            return 0
        if len(houses) == 1:
            return houses[0]

        dp = [0] * len(houses)

        # Base case: when there's only one house, rob that house.
        dp[0] = houses[0]
        # Base case: when there are two houses, rob the one with the most
        # money.
        dp[1] = max(houses[0], houses[1])

        # Fill in the rest of the DP array.
        for i in range(2, len(houses)):
            # dp[i] = max(profit if we skip house 'i', profit if we rob
            # house 'i').
            dp[i] = max(dp[i - 1], houses[i] + dp[i - 2])

        return dp[len(houses) - 1]
```

### Complexity

TC: O(n) as we decide per house onces.
SC: O(n) as we maintain the array of each value.

### Optimization

From the DP array formula dp[i] = max(dp[i - 1], houses[i] + dp[i - 2]), an important observation is that we only need to access the previous two values of the DP array, index i - 1 and index i - 2, to calculate the current value at index i. This means we don't need to store the entire DP array.

Instead, we can use two variables to keep track of the previous two values:
• prev_max_profit: stores the value of dp[i - 1].
• prev_prev_max_profit: stores the value of dp[i - 2].
