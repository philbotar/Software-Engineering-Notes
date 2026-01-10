## Introduction

- Heavy Lifting of DP is creating the diagram of what the algorithm will be.
- Then converting to code is the easy part

## Fibonacci Sequence

- the xth number is the sum of the previous and the value before that.
- Issue with the naive solution is that it takes way too long. (DFS with no memoization)
- We should track our stack trace to evaluate our Space Complexity.
- For Fib, it comes out to O(n) SC and TC, with DP.
- Without DP, we have O(2^N) TC, and O(n) SC -> fib(50) ~ 2^50 steps.
- First thing we should do, is check if its been calculated before.
- ![[assets/Screenshot 2025-12-01 at 3.01.53 pm.png]]

## Grid Traveler Memoization

- Every step you take, youre shrinking the problem size that you have access to.
- Always try to structure it as a tree.
- ![[assets/Screenshot 2025-12-01 at 3.05.35 pm.png]]
- We try to split it out from our inputs, which you can assume its either down or right, so each step becomes a tree of potential steps.
- ![[assets/Screenshot 2025-12-01 at 3.10.19 pm.png]]
- Pre memoization, with the space complexity being the depth of the tree![[assets/Screenshot 2025-12-01 at 3.14.19 pm.png]]

## CanSum Problem

- canSum(7, [5,3,4,7]) -> true
  - use 3+4
  - use 7
  - We can generate 7 from the array.
- canSum(7, [2,4]) -> False
  - Cannot sum 2 and 4 in any way to make 7.
- You have to try all possibilities of a number, so you use a for loop to go through all possible.
- Dont forget finding the height, and the maximal branching factor.
  - The max height would be the target.
  - The branching factor would be the number of values we can derive from
  - The call stack would be O(n^m) tc.

## HowSum(targetSum, numbers) Problem

- Write a function 'howSum(targetSum, numbers)' that takes in a targetSum and an array of numbers as arguments.
  - The function should return an array containing any combination of elements that add up to exactly the targetSum.
- howSum(8, [2,3,5])
  - 2+2+2+2 -> [2,2,2,2]
  - 3+5 -> [3,5]
-
