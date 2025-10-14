# What is Backtracking?

Backtracking is a pattern that is used when a path or series of steps needs to be reverted up to a certain point, to later on be used. For example, if i wanted to get every permutation of path possible from the root of a binary tree to each leaf, i would use backtracking to go back up the tree to then take a different path, without needing to revisit each node multiple times.

## What types of Questions will need Backtracking?

- Combinations of a Phone Number, Combinations, Permutations
- Cobination Sum
- Generate Parentheses - Another permutation question.
- Word Search

## How do we implement Backtracking?

Generally speaking, Backtracking is implemented by utilising recursion and the call stack. The idea behind this is that, because we know that the call exists on the stack and provided you do it in a pre-order way, you know that you will return to that node.

This then means when you get back to that node, you can start again and take a new path, without needing to restart from the beginning, as you "backtracked" to a position where a new permutation can begin.

So as steps

1. Start from Root, recursively travel down the tree/graph to next viable locations.
2. Once the base case is fulfilled, add permutation or value to the target.
3. As you return, each call on the stack will wait to continue execution, where execution could be a new path.
4. Continue till all possible paths are exhausted.

NOTE: if following paths in memory, you will need to clone and/or remove the values added as you went down the tree. This is because changes arent reverted when execution completes for each call.

## As Code

```
def backtrack(result, current_path, ...other_params):
    # 1. Check for the base case (a valid solution)
    if is_a_solution(current_path):
        result.add(copy(current_path)) # Add a copy, not the reference!
        return

    # 2. Iterate through all possible choices
    for choice in get_possible_choices():
        # 3. Choose
        current_path.add(choice)

        # 4. Explore
        backtrack(result, current_path, ...other_params)

        # 5. Un-choose (This is the backtrack!)
        current_path.remove(last_choice)
```
