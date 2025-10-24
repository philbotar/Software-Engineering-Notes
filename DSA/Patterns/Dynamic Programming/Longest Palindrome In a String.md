# Longest Palindrome in a String

Return the longest palindromic substring within a given string

### Intuition

In order to get the longest palindrome, you have to "build outwards" by first finding all the small palindromes of length 1 and 2.

For length one, it's simple: just assign True diagonally down the 2D dp array (dp[i][i]). Then, you do it for all palindromes of length 2 by checking if s[i] is equal to s[i+1]. We set these base cases because they cover the two types of "middles" a palindrome can have: a singular middle (like in "aca") and a double middle (like in "acca").

The 2D dp array we store isn't for numbers, it's just a boolean (True or False) that answers the question, "Is the substring from index i to index j a palindrome?".

Finally, you check for all lengths from 3 up to n. This is the "building outwards" part. To decide if dp[i][j] is True, you only need to check two things:

Are the outer characters the same? (s[i] == s[j])

Is the entire string inside them already a palindrome? (You check this by looking up the pre-solved answer: dp[i+1][j-1])

If both of those are True, then dp[i][j] is also True. As you go, you just keep track of the start_index and max_len of the longest one you've found.

### Code

```java
class Solution {
    /**
     * Finds the longest palindromic substring using dynamic programming.
     * @param s The input string.
     * @return The longest palindromic substring.
     */
    public String longestPalindrome(String s) {
        int n = s.length();

        // Handle empty or single-character strings
        if (n < 2) {
            return s;
        }

        // dp[i][j] will be true if the substring s[i...j] (inclusive) is a palindrome
        boolean[][] dp = new boolean[n][n];

        int start_index = 0;
        int max_len = 1; // A single character is always a palindrome

        // --- Base case: All substrings of length 1 are palindromes ---
        for (int i = 0; i < n; i++) {
            dp[i][i] = true;
        }

        // --- Base case: Check substrings of length 2 ---
        for (int i = 0; i < n - 1; i++) {
            if (s.charAt(i) == s.charAt(i + 1)) {
                dp[i][i + 1] = true;
                start_index = i; // Update to the first found length-2 palindrome
                max_len = 2;
            }
        }

        // --- Populate for lengths 3 to n (The "Building Out" part) ---
        // L is the length of the substring we are checking
        for (int L = 3; L <= n; L++) {
            // i is the starting index
            // We loop from i=0 up to the last possible start for a string of length L
            for (int i = 0; i < n - L + 1; i++) {

                // j is the ending index of the substring
                int j = i + L - 1;

                // Check the two conditions:
                // 1. Its first and last characters are the same (s[i] == s[j])
                // 2. The inner substring is also a palindrome (dp[i + 1][j - 1])
                if (s.charAt(i) == s.charAt(j) && dp[i + 1][j - 1]) {
                    dp[i][j] = true;

                    // If we found a new, *longer* palindrome
                    if (L > max_len) {
                        max_len = L;
                        start_index = i;
                    }
                }
            }
        }

        // Return the longest palindromic substring
        // s.substring(beginIndex, endIndex)
        // beginIndex is inclusive, endIndex is exclusive.
        return s.substring(start_index, start_index + max_len);
    }
}

// --- Example Usage ---
// public class Main {
//     public static void main(String[] args) {
//         Solution sol = new Solution();
//
//         String s1 = "babad";
//         System.out.println("Input: '" + s1 + "'");
//         System.out.println("Output: '" + sol.longestPalindrome(s1) + "'"); // Output: 'bab' or 'aba'
//
//         String s2 = "cbbd";
//         System.out.println("Input: '" + s2 + "'");
//         System.out.println("Output: '" + sol.longestPalindrome(s2) + "'"); // Output: 'bb'
//
//         String s3 = "abccbaba";
//         System.out.println("Input: '" + s3 + "'");
//         System.out.println("Output: '" + sol.longestPalindrome(s3) + "'"); // Output: 'bccb'
//     }
// }

```


