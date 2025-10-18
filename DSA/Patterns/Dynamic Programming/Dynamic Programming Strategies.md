Questions

1. How should we look for Dynamic Programming Questions?
2. What is a recurrence relation?
3. How can we identify it?

# Dynamic Programming Strategies

Dynamic programming is an optimization technique for solving complex problems by breaking them down into simpler, overlapping subproblems, solving each of those subproblems just once, and storing their solutions to avoid redundant computations.

### The DP Process And Terminology

1. Optimal Substructure: where the optimal solution to a problem can be found from the optimal solutions of its subproblems.
2. Overlapping Subproblems: If there are subproblems that are solved repeatedly during the problem solving process.
3. Recurrence Relation: A formula that expresses the solution to the problem in terms of its subproblems
4. Base Cases: The simplest instance of the problem, where the solution is already known and cant be decomposed into more subproblems.
5. Memoization: The act of storing solved problems in a data structure to avoid re-calculating it during runtime. This is due to the overlapping subproblems attribute of Dynamic Programming problems.
6. Top-Down DP: When you find the answer to a problem through recursive downward calls. You pass in the value you want and recursively break down the problem until it is complete.
7. Bottom-Up DP: When you find the answer to a problem through computing from zero up to the desired value in DP.

### Different Ways to Split DP Problems

1. 1D Dynamic Programming: Problems like Climbing Stairs, Maximum Coin Combination, Neighbourhood Burglary, and Maximum Subarray Sum.
2. 2D Dynamic Programming: Problems like Matrix Pathways, Longest Palindrome in a String, Longest Common Subsequence, 0/1 Knapsack and Largest Square in an Array.
