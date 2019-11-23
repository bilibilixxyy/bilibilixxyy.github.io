---
layout: post
title: 749. Shortest Completing Word
---
### Question
Find the minimum length word from a given dictionary `words`, which has all
the letters from the string `licensePlate`. Such a word is said to _complete_
the given string `licensePlate`

Here, for letters we ignore case. For example, `"P"` on the `licensePlate`
still matches `"p"` on the word.

It is guaranteed an answer exists. If there are multiple answers, return the
one that occurs first in the array.

The license plate might have the same letter occurring multiple times. For
example, given a `licensePlate` of `"PP"`, the word `"pair"` does not complete
the `licensePlate`, but the word `"supper"` does.

 **Example 1:**  

    
    
     **Input:** licensePlate = "1s3 PSt", words = ["step", "steps", "stripe", "stepple"]
    **Output:** "steps"
    **Explanation:** The smallest length word that contains the letters "S", "P", "S", and "T".
    Note that the answer is not "step", because the letter "s" must occur in the word twice.
    Also note that we ignored case for the purposes of comparing whether a letter exists in the word.
    

**Example 2:**  

    
    
    **Input:** licensePlate = "1s3 456", words = ["looks", "pest", "stew", "show"]
    **Output:** "pest"
    **Explanation:** There are 3 smallest length words that contains the letters "s".
    We return the one that occurred first.
    

**Note:**  

  1. `licensePlate` will be a string with length in range `[1, 7]`.
  2. `licensePlate` will contain digits, spaces, or letters (uppercase or lowercase).
  3. `words` will have a length in the range `[10, 1000]`.
  4. Every `words[i]` will consist of lowercase letters, and have length in range `[1, 15]`.

### Solution 1
Idea: assign each letter a prime number and compute the product for the
license plate. Then, compute the product for each word in wordlist. We know if
the char product for a word is divisible by the license plate char product, it
contains all the characters.

    
    
    class Solution {
        private static final int[] primes = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, 101, 103}; 
        
        public String shortestCompletingWord(String licensePlate, String[] words) {
            long charProduct = getCharProduct(licensePlate.toLowerCase());
            String shortest = "aaaaaaaaaaaaaaaaaaaa"; // 16 a's
            for(String word : words)
                if (word.length() < shortest.length() && getCharProduct(word) % charProduct == 0)
                        shortest = word;
            return shortest;
        }
        
        private long getCharProduct(String plate) {
            long product = 1L;
            for(char c : plate.toCharArray()) {
                int index = c - 'a';
                if (0 <= index && index <= 25) 
                    product *= primes[index];
            }
            return product;
        }
    }
    


### Solution 2
  * w/o Counter:

    
    
    class Solution:
        def shortestCompletingWord(self, lp, words):
            cntr_lp, res = collections.defaultdict(int), None
            for char in lp:
                if char.isalpha(): 
                    cntr_lp[char.lower()] += 1
            for word in words:
                check = dict(cntr_lp)
                for char in word:
                    char = char.lower()
                    if char in check:
                        check[char] -= 1
                        if not check[char]: 
                            del check[char]
                if not check and (not res or len(word) < len(res)): 
                    res = word
            return res
    

  * w/ Counter

    
    
    class Solution:
        def shortestCompletingWord(self, lp, words):
            cntr_lp, res = {k: v for k, v in collections.Counter(lp.lower()).items() if k.isalpha()}, None
            for word in words:
                check = collections.Counter(word.lower())
                if all(k in check and v <= check[k] for k, v in cntr_lp.items()):
                    if not res or len(word) < len(res): 
                        res = word
            return res
    

  * Concise advanced solution w/ collections.Counter "-" operation

    
    
    class Solution:
        def shortestCompletingWord(self, lp, words):
            cnt = collections.Counter("".join(c for c in lp.lower() if c.isalpha()))
            return min([w for w in words if not cnt - collections.Counter(w)], key = len)
    


### Solution 3
    
    
    class Solution {
        public String shortestCompletingWord(String licensePlate, String[] words) {
            String target = licensePlate.toLowerCase();
            int [] charMap = new int[26];
            // Construct the character map
            for(int i = 0 ; i < target.length(); i++){
                if(Character.isLetter(target.charAt(i))) charMap[target.charAt(i) - 'a']++;
            }
            int minLength = Integer.MAX_VALUE;
            String result = null;
            for (int i = 0; i < words.length; i++){
                String word = words[i].toLowerCase();
                if(matches(word, charMap) && word.length() < minLength) {
                    minLength = word.length();
                    result  = words[i];
                }
            }
            return result;
        }
        private boolean matches(String word, int[] charMap){
            int [] targetMap = new int[26];
            for(int i = 0; i < word.length(); i++){
                if(Character.isLetter(word.charAt(i))) targetMap[word.charAt(i) - 'a']++;
            }
            
            for(int i = 0; i < 26; i++){
                if(charMap[i]!=0 && targetMap[i]<charMap[i]) return false;
            }
            return true;
        }
    }
    



