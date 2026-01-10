# Substring with a Concatenation of All Words

You are given a string s and an array of strings words. All the strings of words are of the same length.

A concatenated string is a string that exactly contains all the strings of any permutation of words concatenated.

For example, if words = ["ab","cd","ef"], then "abcdef", "abefcd", "cdabef", "cdefab", "efabcd", and "efcdab" are all concatenated strings. "acdbef" is not a concatenated string because it is not the concatenation of any permutation of words.
Return an array of the starting indices of all the concatenated substrings in s. You can return the answer in any order.

### Intuition

What you gotta think of with this problem is iterating by the width of the word. Because each word is the same lengt, we can leverage that knowledge to keep moving the window once by x each time, and adding / removing that value to a count map.

We are doing a for loop to set the starting location. We use a wordCountmap and wordsSeen to track what we have gotten as so far.

In the code theres a large if else block, it means the below

1. Word is Found: If currentWord is a target word, the window expands. We add it to wordsSeen and increment wordsUsed.

2. Too Many Words: If the count of currentWord in the window exceeds the required count, the window shrinks (forces validity). We remove the word at the left pointer, decrement its count, and slide left forward until the word counts are back within the required limits.

3. Complete Match: If wordsUsed equals the total number of words (numWords), a full match is found. We record the index and then immediately remove the word at left and slide left forward to search for the next possible overlapping match.

4. Invalid Word: If currentWord is not a required word, the concatenation is broken. We reset wordsSeen and wordsUsed to zero, and jump the left pointer to the position after the invalid word to restart the search.

```java

class Solution {
    public List<Integer> findSubstring(String s, String[] words) {
        if (s == null || words == null || s.length() == 0 || words.length == 0) {
            return new ArrayList<>();
        }

        int wordLength = words[0].length();
        int numWords = words.length;
        int totalWordsLength = wordLength * numWords;
        ArrayList<Integer> result = new ArrayList<>();


        HashMap<String, Integer> wordCountMap = new HashMap<>();
        for (String word : words) {
            wordCountMap.put(word, wordCountMap.getOrDefault(word, 0) + 1);
        }

        for (int i = 0; i < wordLength; i++) {
            // Check from each starting point.
            checkConcatenation(s, wordCountMap, wordLength, numWords, i, result);
        }

        return result;
    }


    private void checkConcatenation(String s, Map<String, Integer> wordCountMap, int wordLength, int numWords, int startIndex, List<Integer> result) {

        HashMap<String, Integer> wordsSeen = new HashMap<>();
        int left = startIndex;
        int wordsUsed = 0;

        // for each possible
        for (int right = startIndex; right <= s.length() - wordLength; right += wordLength) {
            String currentWord = s.substring(right, right + wordLength);

            if (wordCountMap.containsKey(currentWord)) {
                wordsSeen.put(currentWord, wordsSeen.getOrDefault(currentWord, 0) + 1);
                wordsUsed++;

                while (wordsSeen.get(currentWord) > wordCountMap.get(currentWord)) {
                    String leftWord = s.substring(left, left + wordLength);
                    wordsSeen.put(leftWord, wordsSeen.get(leftWord) - 1);
                    wordsUsed--;
                    left += wordLength;
                }

                if (wordsUsed == numWords) {
                    String leftWord = s.substring(left, left + wordLength);
                    wordsSeen.put(leftWord, wordsSeen.get(leftWord) - 1);
                    wordsUsed--;
                    left += wordLength;
                }

            } else {
                wordsSeen.clear();
                wordsUsed = 0;
                left = right + wordLength;
            }
        }
    }
}
```
