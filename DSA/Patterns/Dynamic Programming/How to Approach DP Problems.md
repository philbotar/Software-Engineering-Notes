# How to Approach Dynamic Programming Questions

## Step 1: Look For Clues

Look for the 2 primary parts of a DP problem

1. Optimal Substructure
2. Overlapping Subproblems

### Optimal Substructures

An optimal Substructure is where the answers to the problem is based on the optimal solutions of the previous problems/values. Its where you would ask yourself: "If I knew the best answer for all smaller versions of this problem, could I easily combine them to get the best answer for the current problem?"

So for example, the optimal substructure in Longest Palindrome in a String is where

1. The inner substring is a palindrome, $S[i+1..j-1]$
2. The two end characters $S[i]$ and $S[j]$ are equal.

To go on for this, lets go with the tabular representation:

![](assets/20251025_070503_Longest-Palindromic-substring.png)

We know that in order to find the answer, we need to find the optimal values of the previous/subproblems within it.

### Overlapping Subproblems

This means that in the subproblems can be reused multiple times. If theres instances where the recursive implementation would repeat the same computation multiple times, most likely it is a DP problem.

An example of this is the fibonacci problem. In order to calculate the nth fibonacci number, you need fib(n-1) and fib(n-2). Youll know that as you go on, therell be multiple double ups, which is exactly where we'd need DP.

## Step 2: Recognise Common Problem Types and Phrases

| **Problem Type**           | **Common Phrasing/Goal**                                                            | **Example DP Technique**                                                                             |
| -------------------------- | ----------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| **Optimization**           | "Find the**maximum/minimum** value..."                                              | Minimum path sum, maximum profit, minimum number of steps.                                           |
| **Counting**               | "Find the**total number of ways** to do X..."                                       | Counting unique paths, ways to make change, ways to decode a string.                                 |
| **Existence**              | "Can we make a**certain value** ?"                                                  | Subset sum problem, partition problem.                                                               |
| **Sequential/Range-based** | Problems involving**substrings, subsequences, or continuous ranges** in a sequence. | Longest Common Subsequence (LCS), Longest Increasing Subsequence (LIS), Matrix Chain Multiplication. |
| **Grid/Path Finding**      | Problems involving**moving through a grid** (up, down, left, right).                | Unique Paths, Minimum Path Sum in a grid.                                                            |

## Step 3: Identify Red Flags for Recursion / Memoization

If youre initially thinking of a recursive soluition, check the warning signs

### High Time Complexity

If a purely recursive solution has an exponential time complexity, and the input size is small (n <= 50), thats a hint that memoization or tabulation is needed to stop redundant work and bring the complexity down to polynomial time.

### A Greedy Choice Fails

If a greedy approach fails, then DP is often the next correct step. DP explores ALL optimal subproblem solutions, ensuring the globally optimal solution is found.

## Summary: The Identification Flowchart

1. **Start with Recursion:** Can you express the problem's solution in terms of the solution to smaller versions of the same problem? (This establishes **Optimal Substructure** ).
2. **Test for Overlap:** If you were to draw the recursive call tree, are the same subproblems calculated repeatedly? If **YES** , you have **Overlapping Subproblems** .
3. **Conclusion:** If both properties are present, the problem is a strong candidate for **Dynamic Programming** .

### Transitioning to a DP Solution

Once identified, DP can be implemented in two ways:

1. **Top-Down (Memoization):** Implement the recursive function but add a data structure (like a hash map or array) to store the results of subproblems as they're solved.
2. **Bottom-Up (Tabulation):** Start by solving the smallest subproblems first and store their results in a table (usually an array or 2D matrix), then use these results to solve larger subproblems until the final solution is reached.
