---
layout: post
title: 336. Palindrome Pairs
---
### Question
Given a list of **unique** words, find all pairs of **_distinct_** indices
`(i, j)` in the given list, so that the concatenation of the two words, i.e.
`words[i] + words[j]` is a palindrome.

 **Example 1:**

    
    
     **Input:** ["abcd","dcba","lls","s","sssll"]
    **Output:** [[0,1],[1,0],[3,2],[2,4]] 
    **E** **xplanation : **The palindromes are ["dcbaabcd","abcddcba","slls","llssssll"]
    

**Example 2:**

    
    
    **Input:** ["bat","tab","cat"]
    **Output:** [[0,1],[1,0]] 
    **E** **xplanation : **The palindromes are ["battab","tabbat"]
    

### Solution 1
Apparently there is an `O(n^2 * k)` naive solution for this problem, with `n`
the total number of words in the `words` array and `k` the average length of
each word: **for each word, we simply go through the`words` array and check
whether the concatenated string is a palindrome or not.**

Of course this will result in `TLE`, as expected. To improve the algorithm, we
want to reduce the number of words that need to be checked for each word,
instead of iterating through the whole array. This prompted me to think if I
can extract any useful information out of the process checking whether the
concatenated string is a palindrome, so that it can help eliminate as many
words as possible for the rest of the `words` array.

To begin, here is the technique I employed to check for palindromes: maintain
two pointers `i` and `j`, with `i` pointing to the start of the string and `j`
to the end of the string. Characters pointed by `i` and `j` are compared. If
at any time the characters pointed by them are not the same, we conclude the
string is not a palindrome. Otherwise we move the two pointers towards each
other until they meet in the middle and the string is a palindrome.

By examining the process above, I did find something that we may take
advantage of to get rid of words that need to be checked otherwise. For
example, let's say we want to append words to `w0`, which starts with
character `'a'`. Then we only need to consider words ending with character
`'a'`, i.e., this will single out all words ending with character `'a'`. If
the second character of `w0` is `'b'`, for instance, we can further reduce our
candidate set to words ending with string `"ba"`, etc. Our naive solution
throws away all these useful pieces of information and repeats the comparison,
which leads to the undesired `O(n^2 * k)` time complexity.

In order to exploit the information gathered so far, we obviously need to
restructure all the words in the `words` array. If you are familiar with
**Trie** structure (I believe you are, since LeetCode has problems for it. In
case you are not, see [Trie](https://en.wikipedia.org/wiki/Trie)), it will
come to mind as we need to deal with words with common suffixes. The next step
is to design the structure for each Trie node. There are at least two fields
that should be covered for each TrieNode: a TrieNode array denoting the next
layer of nodes and a boolean (or integer) to signify the end of a word. So our
tentative TrieNode will look like this:

    
    
     class TrieNode {
        TrieNode[] next;
        boolean isWord;
    }
    

One point here is that we assume all the words contain lowercase letters only.
This is not specified in the problem statement so you probably need to confirm
with the interviewer (here I assume it is the case).

Now we will rearrange each word into this Trie structure: for each word,
simply starting from its last character and identify the node at the next
layer by indexing into root's `next` array with index given by the difference
between the ending character and character `'a'`. If the indexed node is null,
create a new node. Continue to the next layer and towards the beginning of the
word in this manner until we are done with the word, at which point we will
label the `isWord` field of the final node as true.

After building up the Trie structure, we can proceed to search for pairs of
palindromes for each word in the `words` array. I will use the following
example to explain how it works and make possible modifications of the
TrieNode we proposed above.

Let's say we have these words: `["ba", "a", "aaa"]`, the Trie structure will
be as follows:

    
    
            root (f)
               | 'a'
              n1 (t)
         ------------
     'b' |          | 'a'
        n2 (t)    n3 (f)
                    | 'a'
                  n4 (t)
    

The letter in parentheses indicates the value of `isWord` for each node: `f
==> false` and `t ==> true`. The letter beside each vertical line denotes the
index into the `next` array of the corresponding node. For example, for the
first vertical line, `'a'` means `root.next[0]` is not null. Similarly `'b'`
means `n1.next[1]` is not null, and so on.

Here is the searching process:

  1. For word `"ba"`, starting from the first character `'b'`, index into the root.next array with index given by `'b' - 'a' = 1`. The corresponding node is null, then we know there are no words ending at this character, so the searching process is terminated;
  2. For word `"a"`, again indexing into array root.next at index given by `'a' - 'a' = 0` will yield node `n1`, which is not null. We then check the value of `n1.isWord`. If it is true, then it is possible to obtain a palindrome by appending this word to the one currently being examined (a.k.a word `"a"`). Also note that the two words should be distinct from each other, but the `n1.isWord` field provides no information about the word itself, which makes it impossible to distinguish the two words. So it is necessary to modify the structure of the TrieNode so that we can identify the word it represents. One easy way is to have an integer field to remember the index of the word in the `words` array. For non-word nodes, this integer will take negative values (`-1` for example) while for those representing a word, it will be non-negative values. Suppose we have made this modification, then the two words will be identified to be the same, so we discard this pair combination. Since the word `"a"` has only one letter, it seems we are done with it. Or do we? Not really. What if there are words with suffix `"a"` (`"aaa"` in this case)? We need to continue to check the rest part of these words (such as `"aa"` for the word `"aaa"`) and see if the rest forms a palindrome. If it is, then appending this word (`"aaa"` in this case) to the original word (`"a"`) will also form a palindrome (`"aaaa"`). Here I take another strategy: add an integer list to each TrieNode; the list will record the indices of all words satisfying the following two conditions: _1\. each word has a suffix represented by the current TrieNode; 2. the rest of the word forms a palindrome._

Before I get to the third word `"aaa"`, let me spell out the new TrieNode and
the corresponding Trie structure for the above array.

`TrieNode:`

    
    
    class TrieNode {
        TrieNode[] next;
        int index;
        List<Integer> list;
                
        TrieNode() {
            next = new TrieNode[26];
            index = -1;
            list = new ArrayList<>();
        }
    }
    

`Trie`:

    
    
              root (-1,[1,2])
                | 'a'
              n1 (1,[0,1,2])
        ---------------------
    'b' |                 | 'a'
      n2 (0,[0])    n3 (-1,[2])
                          | 'a'
                     n4 (2,[2])
    

The first integer in the parentheses is the index of the word in the `words"`
array (defaulted to `-1`). The integers in the square bracket are the indices
of words satisfying the two conditions mentioned above.

Let's continue with the third word `"aaa"` with this new structure. Indexing
into array `root.next` at index given by `'a' - 'a' = 0` will yield node `n1`
and `n1.index = 1 >= 0`, which means we have a valid word now. The index of
this word (which is `1`) is also different from the index of the word
currently being visited, a.k.a `"aaa"` (which is `2`). So pair `(2,1)` is a
possible concatenation to form a palindrome. But still we need to check the
rest of `"aaa"` (excluding the substring represented by current node `n1`
which is `"a"` from the beginning of `"aaa"`) to see if it is a palindrome. If
so, `(2,1)` will be a valid combination. We continue in this fashion until we
reach the end of `"aaa"`. Lastly we will check `n4.list` to see if there are
any words satisfying the two conditions specified in `step 2` which are
different from current word, and add the corresponding valid pairs.

Both building and searching the `Trie` structure take `O(n * k^2)`, which sets
the total time complexity of the solution. Here is the complete Java program:

    
    
    private static class TrieNode {
        TrieNode[] next;
        int index;
        List<Integer> list;
        	
        TrieNode() {
        	next = new TrieNode[26];
        	index = -1;
        	list = new ArrayList<>();
        }
    }
        
    public List<List<Integer>> palindromePairs(String[] words) {
        List<List<Integer>> res = new ArrayList<>();
    
        TrieNode root = new TrieNode();
    		
        for (int i = 0; i < words.length; i++) {
            addWord(root, words[i], i);
        }
    		
        for (int i = 0; i < words.length; i++) {
            search(words, i, root, res);
        }
        
        return res;
    }
        
    private void addWord(TrieNode root, String word, int index) {
        for (int i = word.length() - 1; i >= 0; i--) {
            int j = word.charAt(i) - 'a';
    				
            if (root.next[j] == null) {
                root.next[j] = new TrieNode();
            }
    				
            if (isPalindrome(word, 0, i)) {
                root.list.add(index);
            }
    				
            root = root.next[j];
        }
        	
        root.list.add(index);
        root.index = index;
    }
        
    private void search(String[] words, int i, TrieNode root, List<List<Integer>> res) {
        for (int j = 0; j < words[i].length(); j++) {	
        	if (root.index >= 0 && root.index != i && isPalindrome(words[i], j, words[i].length() - 1)) {
        	    res.add(Arrays.asList(i, root.index));
        	}
        		
        	root = root.next[words[i].charAt(j) - 'a'];
          	if (root == null) return;
        }
        	
        for (int j : root.list) {
        	if (i == j) continue;
        	res.add(Arrays.asList(i, j));
        }
    }
        
    private boolean isPalindrome(String word, int i, int j) {
        while (i < j) {
        	if (word.charAt(i++) != word.charAt(j--)) return false;
        }
        	
        return true;
    }
    

We have the TrieNode structure at the top. In the `palindromePairs` function,
we build up the Trie by adding each word, then search for valid pairs for each
word and record the results in the `res` list. The last `isPalindrome`
function checks if the substring `[i, j]` (both inclusive) of the given word
is a palindrome.

Hope this helps and happy coding!


### Solution 2
    
    
    public List<List<Integer>> palindromePairs(String[] words) {
        List<List<Integer>> ret = new ArrayList<>(); 
        if (words == null || words.length < 2) return ret;
        Map<String, Integer> map = new HashMap<String, Integer>();
        for (int i=0; i<words.length; i++) map.put(words[i], i);
        for (int i=0; i<words.length; i++) {
            // System.out.println(words[i]);
            for (int j=0; j<=words[i].length(); j++) { // notice it should be "j <= words[i].length()"
                String str1 = words[i].substring(0, j);
                String str2 = words[i].substring(j);
                if (isPalindrome(str1)) {
                    String str2rvs = new StringBuilder(str2).reverse().toString();
                    if (map.containsKey(str2rvs) && map.get(str2rvs) != i) {
                        List<Integer> list = new ArrayList<Integer>();
                        list.add(map.get(str2rvs));
                        list.add(i);
                        ret.add(list);
                        // System.out.printf("isPal(str1): %s
    ", list.toString());
                    }
                }
                if (isPalindrome(str2)) {
                    String str1rvs = new StringBuilder(str1).reverse().toString();
                    // check "str.length() != 0" to avoid duplicates
                    if (map.containsKey(str1rvs) && map.get(str1rvs) != i && str2.length()!=0) { 
                        List<Integer> list = new ArrayList<Integer>();
                        list.add(i);
                        list.add(map.get(str1rvs));
                        ret.add(list);
                        // System.out.printf("isPal(str2): %s
    ", list.toString());
                    }
                }
            }
        }
        return ret;
    }
    
    private boolean isPalindrome(String str) {
        int left = 0;
        int right = str.length() - 1;
        while (left <= right) {
            if (str.charAt(left++) !=  str.charAt(right--)) return false;
        }
        return true;
    }
    

  1. The `<=` in `for (int j=0; j<=words[i].length(); j++)` is aimed to handle empty string in the input. Consider the test case of ["a", ""];

  2. Since we now use `<=` in `for (int j=0; j<=words[i].length(); j++)` instead of `<`. There may be duplicates in the output (consider test case ["abcd", "dcba"]). Therefore I put a `str2.length()!=0` to avoid duplicates.

Another way to avoid duplicates is to use `Set<List<Integer>> ret = new
HashSet<>();` and return `new ArrayList<>(ret);`


### Solution 3
There are several cases to be considered that isPalindrome(s1 + s2):

Case1: If s1 is a blank string, then for any string that is palindrome s2,
s1+s2 and s2+s1 are palindrome.

Case 2: If s2 is the reversing string of s1, then s1+s2 and s2+s1 are
palindrome.

Case 3: If s1[0:cut] is palindrome and there exists s2 is the reversing string
of s1[cut+1:] , then s2+s1 is palindrome.

Case 4: Similiar to case3. If s1[cut+1: ] is palindrome and there exists s2 is
the reversing string of s1[0:cut] , then s1+s2 is palindrome.

To make the search faster, build a HashMap to store the String-idx pairs.

My code:

    
    
    public class Solution {
    public List<List<Integer>> palindromePairs(String[] words) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        if(words == null || words.length == 0){
            return res;
        }
        //build the map save the key-val pairs: String - idx
        HashMap<String, Integer> map = new HashMap<>();
        for(int i = 0; i < words.length; i++){
            map.put(words[i], i);
        }
        
        //special cases: "" can be combine with any palindrome string
        if(map.containsKey("")){
            int blankIdx = map.get("");
            for(int i = 0; i < words.length; i++){
                if(isPalindrome(words[i])){
                    if(i == blankIdx) continue;
                    res.add(Arrays.asList(blankIdx, i));
                    res.add(Arrays.asList(i, blankIdx));
                }
            }
        }
        
        //find all string and reverse string pairs
        for(int i = 0; i < words.length; i++){
            String cur_r = reverseStr(words[i]);
            if(map.containsKey(cur_r)){
                int found = map.get(cur_r);
                if(found == i) continue;
                res.add(Arrays.asList(i, found));
            }
        }
        
        //find the pair s1, s2 that 
        //case1 : s1[0:cut] is palindrome and s1[cut+1:] = reverse(s2) => (s2, s1)
        //case2 : s1[cut+1:] is palindrome and s1[0:cut] = reverse(s2) => (s1, s2)
        for(int i = 0; i < words.length; i++){
            String cur = words[i];
            for(int cut = 1; cut < cur.length(); cut++){
                if(isPalindrome(cur.substring(0, cut))){
                    String cut_r = reverseStr(cur.substring(cut));
                    if(map.containsKey(cut_r)){
                        int found = map.get(cut_r);
                        if(found == i) continue;
                        res.add(Arrays.asList(found, i));
                    }
                }
                if(isPalindrome(cur.substring(cut))){
                    String cut_r = reverseStr(cur.substring(0, cut));
                    if(map.containsKey(cut_r)){
                        int found = map.get(cut_r);
                        if(found == i) continue;
                        res.add(Arrays.asList(i, found));
                    }
                }
            }
        }
        
        return res;
    }
    
    public String reverseStr(String str){
        StringBuilder sb= new StringBuilder(str);
        return sb.reverse().toString();
    }
    
    public boolean isPalindrome(String s){
        int i = 0;
        int j = s.length() - 1;
        while(i <= j){
            if(s.charAt(i) != s.charAt(j)){
                return false;
            }
            i++;
            j--;
        }
        return true;
    }
    

}



