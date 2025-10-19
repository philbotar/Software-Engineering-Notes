# Minimum Coin Combination

### Problem
You are given an array of coin values and a target amount of money. Return the minimum number of coins needed to total the target amount. If this isn't possible, return -1. You may assume there's an unlimited supply of each coin.

Example 1:
Input coins = [1, 2, 3] , target = 5
Output: 2
Explanation: Use one 2-dollar coin and one 3-dollar coin to, make 5 dollars.

Example 2:
Input: Coins = [2, 4], target = 5
Output: -1

### Intuition
When given questions of infinite possible combinations up to N, its usually a safe assumption to think DP. 

#### Top Down Intuition

We know that for each part of the way, we can pick ƒrom the coins to get to a certain point. We know the target, so for each step, we start a check if its possible to get closer to zero. If theres a number we have reached, we store the number of steps it took to get there. This is our memoization. 

1. Look at the problem, notice that for top down, we need to take all possible paths out of the coins we have available. 
2. Create our array to store the minimum steps it took to get to each location, initialising with infinity. 
3. As we go through, starting from target (top down), we check if th value we have gotten to is memo'd, if it is it means its been solved before and we can just go on with that value. 
4. Once all trees / paths have been exhausted, we know that we have checked all paths, and used the minimums where we can. Return the value at 0 in the array, and if its infinity, return -1. 

Complexity: 
TC WITHOUT MEMOIZATION:  O(n^target / m), where n is the number of coins, m is the smallest coin value. Its because we have a branch factor of n (Number of calls for each call), and the depth being target / m, as its the worst case (Think if I had target 11, and coin 1, we'd be 11 deep in all cases). 

TC WITH MEMOIZATION: Each subproblem is only solved once. Since there are at most target subproblems, and we iterate though all n coins for each subproblem, the time complexity is O(target * n)

SC: O(target), because while the max depth is only target / m, the memo array stores up to target key-value pairs. 

#### Bottom Up Intuition

For bottom up, we know we need to create the subproblems from 0. This means that for us to continue, we need an array to refer to the previous stored value, and the current value. 

This code is saying for each value, as long as the coin is smaller or equal to i, we store the min between its current value, and 1 + the value minus the current coin. 

We know that we need the previous value to go off, and since this is bottom up, the min number of steps to get to our current given the previous coins are there. If there is the case though if where we are now is less, we know to use that. 
```java
    for(int i = 1; i < target + 1; i++){
        for(int coin : coins){
            if(coin <= i){
                dp[t] = Math.min(dp[i], 1 + dp[i - coin]);
            }
        }
    }
```


Time Complexity: O(target * n) because we loop through all n coins for each value between 1 and target.

Space Complexity: O(target) due to the space occupied by the DP array, which is of size target + 1. 