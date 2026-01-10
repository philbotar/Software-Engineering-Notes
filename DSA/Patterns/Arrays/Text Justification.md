# Text Justification

Given an array of strings words and a width maxWidth, format the text such that each line has exactly maxWidth characters and is fully (left and right) justified.

You should pack your words in a greedy approach; that is, pack as many words as you can in each line. Pad extra spaces ' ' when necessary so that each line has exactly maxWidth characters.

Extra spaces between words should be distributed as evenly as possible. If the number of spaces on a line does not divide evenly between words, the empty slots on the left will be assigned more spaces than the slots on the right.

For the last line of text, it should be left-justified, and no extra space is inserted between words.

Note:

A word is defined as a character sequence consisting of non-space characters only.
Each word's length is guaranteed to be greater than 0 and not exceed maxWidth.
The input array words contains at least one word.

Example 1:

Input: words = ["This", "is", "an", "example", "of", "text", "justification."], maxWidth = 16
Output:
[
"This is an",
"example of text",
"justification. "
]

### Intuition

There isnt a real trick to this question, but rather its a test for your ability to write clean code. You need to be able to identify different problems within the problem to then go on. So for this one, you need to be able to differentiate the paths and how they interact with one another.

A good thing to note is how to add the extra spaces and when to do it. By doing normal integer division you get the minimum required for each space between, but for the remaining values you can use modulo and remove from that modulo'd value to go on.

### Code

```java
class Solution {
    public List<String> fullJustify(String[] words, int maxWidth) {
        // 1. Pack words in greedy approach.
        // So we grab as many words as we can plus the num of words in the array - 1 (only need between)
        // 2. Once we know what words we will put in there, we need the number of spaces remaining.
        // 3. Once we have the spaces remaining, we can fill it in.
        ArrayList<String> wordsForLine  = new ArrayList<>();
        ArrayList<String> returnVal = new ArrayList<>();

        int curStringLength = 0;

        for(String word : words){

            // If the word is the entire line, we need to ensure its fine
            // LEts say [hey, there] witth length 10
            // We need 1 space at least, so we do maxWidth - size + 1

            // So we have the string Length, now we need to convert into actual line
            if(curStringLength + word.length() < maxWidth - wordsForLine.size() + 1){
                curStringLength += word.length();
                wordsForLine.add(word);
            } else {
                returnVal.add(getFormattedString(wordsForLine, maxWidth, curStringLength, false));
                wordsForLine = new ArrayList<>();
                wordsForLine.add(word);
                curStringLength = word.length();
            }
        }

        returnVal.add(getFormattedString(wordsForLine, maxWidth, curStringLength, true));

        return returnVal;
    }


    public String getFormattedString(ArrayList<String> words, int maxWidth, int curStringLength, boolean isLastLine) {
        StringBuilder str = new StringBuilder();

        // 1. Handle Single Word Case (applies to all lines)
        if (words.size() == 1) {
            str.append(words.get(0));
            str.append(" ".repeat(maxWidth - curStringLength));
            return str.toString();
        }

        int totalSpaces = maxWidth - curStringLength;
        int numGaps = words.size() - 1;

        // 2. Handle Last Line (Left Justified)
        if (isLastLine) {
            // Words separated by single space, remaining space padded at the end
            for (int i = 0; i < words.size(); i++) {
                str.append(words.get(i));
                if (i < numGaps) { // Add one space between all words
                    str.append(" ");
                }
            }
            // Pad the rest of the line
            str.append(" ".repeat(maxWidth - str.length()));
            return str.toString();
        }

        // 3. Handle Regular Lines (Fully Justified)

        int baseSpace = totalSpaces / numGaps;
        int extraSpaces = totalSpaces % numGaps;

        // Append the first word
        str.append(words.get(0));

        // Append the remaining words and spaces
        for (int i = 1; i < words.size(); i++) {
            int spacesToAdd = baseSpace;

            // Distribute the 'extra' spaces one by one to the left gaps
            if (i - 1 < extraSpaces) {
                spacesToAdd++;
            }

            str.append(" ".repeat(spacesToAdd));
            str.append(words.get(i));
        }

        return str.toString();
    }
}
```
