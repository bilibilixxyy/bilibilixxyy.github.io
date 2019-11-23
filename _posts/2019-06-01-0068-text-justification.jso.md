---
layout: post
title: 68. Text Justification
---
### Question
Given an array of words and a width  _maxWidth_ , format the text such that
each line has exactly _maxWidth_ characters and is fully (left and right)
justified.

You should pack your words in a greedy approach; that is, pack as many words
as you can in each line. Pad extra spaces `' '` when necessary so that each
line has exactly _maxWidth_ characters.

Extra spaces between words should be distributed as evenly as possible. If the
number of spaces on a line do not divide evenly between words, the empty slots
on the left will be assigned more spaces than the slots on the right.

For the last line of text, it should be left justified and no **extra** space
is inserted between words.

 **Note:**

  * A word is defined as a character sequence consisting of non-space characters only.
  * Each word's length is guaranteed to be greater than 0 and not exceed _maxWidth_.
  * The input array `words` contains at least one word.

 **Example 1:**

    
    
     **Input:**
    words = ["This", "is", "an", "example", "of", "text", "justification."]
    maxWidth = 16
    **Output:**
    [
        "This    is    an",
       "example  of text",
       "justification.  "
    ]
    

**Example 2:**

    
    
    **Input:**
    words = ["What","must","be","acknowledgment","shall","be"]
    maxWidth = 16
    **Output:**
    [
       "What   must   be",
      "acknowledgment  ",
      "shall be        "
    ]
    **Explanation:** Note that the last line is "shall be    " instead of "shall     be",
                  because the last line must be left-justified instead of fully-justified.
                 Note that the second line is also left-justified becase it contains only one word.
    

**Example 3:**

    
    
    **Input:**
    words = ["Science","is","what","we","understand","well","enough","to","explain",
              "to","a","computer.","Art","is","everything","else","we","do"]
    maxWidth = 20
    **Output:**
    [
       "Science  is  what we",
      "understand      well",
      "enough to explain to",
      "a  computer.  Art is",
      "everything  else  we",
      "do                  "
    ]
    

### Solution 1
    
    
    def fullJustify(self, words, maxWidth):
        res, cur, num_of_letters = [], [], 0
        for w in words:
            if num_of_letters + len(w) + len(cur) > maxWidth:
                for i in range(maxWidth - num_of_letters):
                    cur[i%(len(cur)-1 or 1)] += ' '
                res.append(''.join(cur))
                cur, num_of_letters = [], 0
            cur += [w]
            num_of_letters += len(w)
        return res + [' '.join(cur).ljust(maxWidth)]
    

How does it work? Well in the question statement, the sentence "Extra spaces
between words should be distributed as evenly as possible. If the number of
spaces on a line do not divide evenly between words, the empty slots on the
left will be assigned more spaces than the slots on the right" was just a
really long and awkward way to say _round robin_. The following line
implements the round robin logic:

    
    
     for i in range(maxWidth - num_of_letters):
                    cur[i%(len(cur)-1 or 1)] += ' '
    

What does this line do? Once you determine that there are only k words that
can fit on a given line, you know what the total length of those words is
num_of_letters. Then the rest are spaces, and there are (maxWidth -
num_of_letters) of spaces. The "or 1" part is for dealing with the edge case
len(cur) == 1.

* * *

The following is my older solution for reference, longer and less clear. The
idea is the same, but I did not figure out the nice way to distribute the
space at the time.

    
    
    def fullJustify(self, words, maxWidth):
        res, cur, num_of_letters = [], [], 0
        for w in words:
            if num_of_letters + len(w) + len(cur) > maxWidth:
                if len(cur) == 1:
                    res.append( cur[0] + ' '*(maxWidth - num_of_letters) )
                else:
                    num_spaces = maxWidth - num_of_letters
                    space_between_words, num_extra_spaces = divmod( num_spaces, len(cur)-1)
                    for i in range(num_extra_spaces):
                        cur[i] += ' '
                    res.append( (' '*space_between_words).join(cur) )
                cur, num_of_letters = [], 0
            cur += [w]
            num_of_letters += len(w)
        res.append( ' '.join(cur) + ' '*(maxWidth - num_of_letters - len(cur) + 1) )
        return res


### Solution 2
    
    
    vector<string> fullJustify(vector<string> &words, int L) {
        vector<string> res;
        for(int i = 0, k, l; i < words.size(); i += k) {
            for(k = l = 0; i + k < words.size() and l + words[i+k].size() <= L - k; k++) {
                l += words[i+k].size();
            }
            string tmp = words[i];
            for(int j = 0; j < k - 1; j++) {
                if(i + k >= words.size()) tmp += " ";
                else tmp += string((L - l) / (k - 1) + (j < (L - l) % (k - 1)), ' ');
                tmp += words[i+j+1];
            }
            tmp += string(L - tmp.size(), ' ');
            res.push_back(tmp);
        }
        return res;
    }
    

For each line, I first figure out which words can fit in. According to the
code, these words are words[i] through words[i+k-1]. Then spaces are added
between the words. The trick here is to use mod operation to manage the spaces
that can't be evenly distrubuted: the first (L-l) % (k-1) gaps acquire an
additional space.


### Solution 3
    
    
    public class Solution {
        public List<String> fullJustify(String[] words, int L) {
            List<String> lines = new ArrayList<String>();
            
            int index = 0;
            while (index < words.length) {
                int count = words[index].length();
                int last = index + 1;
                while (last < words.length) {
                    if (words[last].length() + count + 1 > L) break;
                    count += words[last].length() + 1;
                    last++;
                }
                
                StringBuilder builder = new StringBuilder();
                int diff = last - index - 1;
                // if last line or number of words in the line is 1, left-justified
                if (last == words.length || diff == 0) {
                    for (int i = index; i < last; i++) {
                        builder.append(words[i] + " ");
                    }
                    builder.deleteCharAt(builder.length() - 1);
                    for (int i = builder.length(); i < L; i++) {
                        builder.append(" ");
                    }
                } else {
                    // middle justified
                    int spaces = (L - count) / diff;
                    int r = (L - count) % diff;
                    for (int i = index; i < last; i++) {
                        builder.append(words[i]);
                        if (i < last - 1) {
                            for (int j = 0; j <= (spaces + ((i - index) < r ? 1 : 0)); j++) {
                                builder.append(" ");
                            }
                        }
                    }
                }
                lines.add(builder.toString());
                index = last;
            }
            
            
            return lines;
        }
    }



