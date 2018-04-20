---
title: kmp算法
date: 2017-03-09 13:27:12
tags: [经典算法]
categories: [Algorithm]
---
Text: aabaaabaaac
Pattern: aabaaac

substrings of pattern
a
aa
aab
aaba
aabaa
aabaaa
aabaaac

1. build the next array
indx: 0 1 2 3 4 5 6
text: a a b a a a c
next: 0 1 0 1 2 2 0

idea: is find the common prefix and suffix for all the substring, then if two char are miss matched, we can quickly know where is a good index to continue comparing without unnecessary works.
use j to scan the word, and i to find the common predix and suffix
steps:  
1. while char i and j is not same, set(move) i to next[i - 1] (the i has to be greater than 0)
2. if they are the same, set next[j] to i + 1 and increment i by 1. 
for example, when i = 0, j = 3, we know we found a common prefix and suffix, then we can increment both i and j to check if the next chars could make a longer common prefix and suffix.

more importantly, when they start different, like when i = 2 and j = 5, if simply set i to 0, will be wrong, bacause when i2 and j5 is different, there sill may be some common prefix and suffix in front, the right way is moving i to next[i - 1].

2. compare

as the next array gives us the right index to continue comparing, then the only thing we need to do is when found a miss matching, we just move j to next[j - 1] until they start matching.

```java
/*idea is using kmp algorithm, two important part, 1. build the next array. 2. compare
1. first is build the next array: 
idea: is find the common prefix and suffix for all the substring, then if two char are miss matched, we can quickly know where is a good index to continue comparing without unnecessary works.
steps:  1. while char i and j is not same, set(move) i to next[i - 1] (the i has to be greater than 0)
        2. if they are the same, set next[j] to i + 1 and increment i by 1. 
2. compare, idea is when there is a miss match, we can simply set(move) j to next[j - 1] to continue, otherwise, we can just keep moving both i and j and compare.
then if the j goes over the length, means we found a full match.
*/
class Solution {
    public int strStr(String haystack, String needle) {
        if (haystack == null || needle == null) {
            return -1;
        }
        int hlen = haystack.length();
        int nlen = needle.length();
        if (nlen == 0)  {
            return 0;
        }
        if (hlen == 0 || hlen < nlen) {
            return -1;
        }
        int[] next = new int[nlen];
        for (int i = 0, j = 1; j < nlen; ++j) {
            while (i > 0 && needle.charAt(i) != needle.charAt(j)) {
                i = next[i - 1];
            }
            if (needle.charAt(i) == needle.charAt(j)) {
                next[j] = ++i;
            }
        }
        for (int i = 0, j = 0; i < hlen && j < nlen; ++i) {
            while (j > 0 && haystack.charAt(i) != needle.charAt(j)) {
                j = next[j - 1]; 
            }
            if (haystack.charAt(i) == needle.charAt(j)) {
                ++j;
            }
            if (j == nlen) {
                return i - nlen + 1;
            }
        }
        return -1;
    }
}
```