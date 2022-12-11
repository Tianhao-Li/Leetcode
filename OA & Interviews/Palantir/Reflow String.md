# Reflow String

## 1. Word wrap

> 给一个word list 和最大的长度，要求把这些word用 - 串联起来，但不能超过最大的长度

- Clarify input & output format



## 2. Word processor : reflow & justify

> Given a String array, and a new maximum width, re-flow the text to fit the new width. Each line should have the exact specified width. If any line is too short, insert '-' (as stand-ins for spaces) between words as equally as possible until it fits.

- ==注意这里代码中填的是空格，要换成 - ！！！==

```java
class Solution {
    public List<String> fullJustify(String[] words, int maxWidth) {
        // Idea: Given the words array, each line go as far as possible, then justify this line
        List<String> result = new ArrayList<>();
        int left = 0;

        // Process one line at a time
        while (left < words.length) {
            // each line go as far as possible
            int right = findRight(words, left, maxWidth);
            // then justify this line
            result.add(justify(words, left, right, maxWidth));
            // move on to next round
            left = right + 1;
        }

        return result;
    }

    // Helper function for finding the rightmost word in this line
    private int findRight(String[] words, int left, int maxWidth) {
        // Start from the word -- words[left]
        int right = left;
        int sumLength = words[right++].length();

        // Expand as far as possible (until invalid)
        // each time "right" is already pointing at the words we have not examined yet
        while (right < words.length && sumLength + 1 + words[right].length() <= maxWidth) { // 1 for space
            sumLength += 1 + words[right].length();
            right++;
        }

        return right - 1; // (Since final invalid right is included)
    }

    // Helper function for justifying this line
    private String justify(String[] words, int left, int right, int maxWidth) {
        // Case 1: only one word included
        if (left == right) {
            return pad(words[left], maxWidth);
        }

        // Case 2: multiple words in this line
        boolean lastLine = (right == words.length - 1); // process differently based on this
        int totalSpace = maxWidth - wordsLength(words, left, right); // the total space to pad
        int numSpace = right - left; // the number of spaces between given words
        // Calculate the space to pad
        String padUnit = lastLine ? " " : " ".repeat(totalSpace / numSpace); // spaces pad at end of each word
        int remainder = lastLine ? 0 : totalSpace % numSpace; // extra space at the end of words on the left
        // Construct the justified version of current line
        StringBuilder sb = new StringBuilder();
        for (int i = left; i <= right; i++) {
            sb.append(words[i]).append(padUnit).append(remainder-- > 0 ? " " : "");
            // last append is for evenly add space to words on the left
        }
        // trim the extra "padUnit" at the end of last word
        String justified = sb.toString().trim();

        return pad(justified, maxWidth); // for the last line with multiple words, still need padding
    }

    // Helper funtion for padding space on the right
    private String pad(String s, int maxWidth) {
        return s + " ".repeat(maxWidth - s.length());
    }

    // Helper function for calculating the total length of give words
    private int wordsLength(String[] words, int left, int right) {
        int totalLength = 0;
        for (int i = left; i <= right; i++) {
            totalLength += words[i].length();
        }

        return totalLength;
    }
}
```

