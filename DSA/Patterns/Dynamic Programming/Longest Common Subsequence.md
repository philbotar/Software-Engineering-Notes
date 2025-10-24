# Longest Common Subsequence

### Problem

Given two strings, find the length of their longest common subsequence. A subsequence is a sequence of characters that can be derived from a string by deleting zero or more elements, without changing the order of the remaining elements.

### Intuition

There are 2 possible cases when comparing strings.

1. Equal Characters, The length of the LCS is 1 for the current match, plus the LCS for the rest of both strings, meaning you move onto the next character in both strings.
2. Unequal Characters, You must skip one of them to find a potential match later, leaving you with 2 choices
   - Skip the character in the first string and find the LCS of the remaining substrings,
   - Skip the character in the second string and find the LCS of the remaining substrings

The LCS will be the maximum length found between these two choices.

### Code

```python
// Create and initialize dp table (all 0s)
int dp[text1.length() + 1][text2.length() + 1]

// Loop from 1 (not 0, since row/col 0 is the empty string base case)
for i from 1 to text1.length():
    for j from 1 to text2.length():

        // Case 1: Characters match
        // Note: we check text1[i-1] and text2[j-1]
        if text1[i-1] == text2[j-1]:
            // 1 + the LCS of the strings *before* this char
            dp[i][j] = 1 + dp[i-1][j-1]

        // Case 2: Characters don't match
        else:
            // Max of (skipping char in text1) or (skipping char in text2)
            dp[i][j] = max(dp[i-1][j], dp[i][j-1])
```

### Optimized Code

The optimization we have here is that instead of storing it in a 2D array, we only bother with the 2 rows.

```python
def longestCommonSubsequence(text1: str, text2: str) -> int:
    """
    Calculates the LCS length using bottom-up DP with O(min(m, n)) space.
    """

    # --- Space Optimization ---
    # Ensure text2 is the shorter string.
    # This makes our arrays (prev_row, curr_row) use space O(min(m, n)).
    if len(text1) < len(text2):
        text1, text2 = text2, text1 # Swap them

    m = len(text1) # Length of the (now) longer string
    n = len(text2) # Length of the (now) shorter string

    # We only need two rows, each of length n+1
    # (for the shorter string + 1 for the empty string case)
    prev_row = [0] * (n + 1)
    curr_row = [0] * (n + 1)

    # Outer loop iterates through the longer string (text1)
    for i in range(1, m + 1):
        # Inner loop iterates through the shorter string (text2)
        for j in range(1, n + 1):

            # Note: We compare text1[i-1] and text2[j-1]

            if text1[i - 1] == text2[j - 1]:
                # --- Case 1: Characters match ---
                # 1 + LCS of strings before this (dp[i-1][j-1])
                # This corresponds to prev_row[j-1]
                curr_row[j] = 1 + prev_row[j-1]
            else:
                # --- Case 2: Characters do not match ---
                # max( LSC_skipping_text1_char, LCS_skipping_text2_char )
                # max( dp[i-1][j], dp[i][j-1] )
                # This corresponds to max( prev_row[j], curr_row[j-1] )
                curr_row[j] = max(prev_row[j], curr_row[j-1])

        # --- Update rows for the next iteration ---
        # The row we just calculated (curr_row) now becomes the
        # "previous" row for the *next* i-iteration.
        # We can copy the values.
        prev_row = curr_row.copy()
        # curr_row will be overwritten in the next inner loop,
        # so we don't need to reset it to zeros.

    # After all loops, the final answer is the last element
    # of the last row we computed (which is now stored in prev_row).
    return prev_row[n]

# --- Example Usage ---
s1 = "abcde"
s2 = "ace"
print(f"LCS of '{s1}' and '{s2}': {longestCommonSubsequence(s1, s2)}")
# Output: LCS of 'abcde' and 'ace': 3

s3 = "AGGTAB"
s4 = "GXTXAYB"
print(f"LCS of '{s3}' and '{s4}': {longestCommonSubsequence(s3, s4)}")
# Output: LCS of 'AGGTAB' and 'GXTXAYB': 4
```

### Complexity

The time complexity on both is O(m _ n), with m being the first string, and n being the second.
The space complexity without the optimization is O(m _ n), whereas with the optimization its o(n).
