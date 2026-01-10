- Drawing out the algorithm is the most important thing in DP.
- O(n / 2) normalises to O(n) because we dont take into account multiplicatives.
- When we identify a DP problem, always draw the tree to identify the RR.
- When we are determining the space complexity of a recursive function, we should remember that when adding to the call stack, we dont add every single call, but rather only n calls until we have to pop one from the stack. 
- Memoization, its like a memo, a reminder in real life. 
- Always try to start with a really small and simple input when trying to solve DP problems.
- Many dynamic Programming questions are just spin offs of fibonacci.
- First Solve, then make efficient. Dont try to make it memoized off the bat. 

Alvins Memoization Recipe. STAY METHODICAL.  
1. Make it Work
	- Visualise the problem as a tree
	- Implement the tree using recursion 
	- Test it, (Our brute force solution)
2. Make it efficient
	- Add a memo object
	- add a base case to return memo values
	- store return values into the memo

- Stop trying to make it binary choice each time, there can be more choices. 
- Always just try make the tree of decisions, do NOT worry about it being 1 or 2 calls, it can be a bunch.