---
layout: post
title: 692. Top K Frequent Words
---
### Question
Given a non-empty list of words, return the _k_ most frequent elements.

Your answer should be sorted by frequency from highest to lowest. If two words
have the same frequency, then the word with the lower alphabetical order comes
first.

 **Example 1:**  

    
    
     **Input:** ["i", "love", "leetcode", "i", "love", "coding"], k = 2
    **Output:** ["i", "love"]
    **Explanation:** "i" and "love" are the two most frequent words.
        Note that "i" comes before "love" due to a lower alphabetical order.
    

**Example 2:**  

    
    
    **Input:** ["the", "day", "is", "sunny", "the", "the", "the", "sunny", "is", "is"], k = 4
    **Output:** ["the", "is", "sunny", "day"]
    **Explanation:** "the", "is", "sunny" and "day" are the four most frequent words,
        with the number of occurrence being 4, 3, 2 and 1 respectively.
    

**Note:**  

  1. You may assume _k_ is always valid, 1 ≤ _k_ ≤ number of unique elements.
  2. Input words contain only lowercase letters.

 **Follow up:**  

  1. Try to solve it in _O_ ( _n_ log _k_ ) time and _O_ ( _n_ ) extra space.

### Solution 1
The idea is to keep a count of each word in a HashMap and then insert in a
Priority Queue.  
While inserting in pq, if the count of two words is same then insert based on
string compare of the keys.

    
    
    class Solution {
        public List<String> topKFrequent(String[] words, int k) {
            
            List<String> result = new LinkedList<>();
            Map<String, Integer> map = new HashMap<>();
            for(int i=0; i<words.length; i++)
            {
                if(map.containsKey(words[i]))
                    map.put(words[i], map.get(words[i])+1);
                else
                    map.put(words[i], 1);
            }
            
            PriorityQueue<Map.Entry<String, Integer>> pq = new PriorityQueue<>(
                     (a,b) -> a.getValue()==b.getValue() ? b.getKey().compareTo(a.getKey()) : a.getValue()-b.getValue()
            );
            
            for(Map.Entry<String, Integer> entry: map.entrySet())
            {
                pq.offer(entry);
                if(pq.size()>k)
                    pq.poll();
            }
    
            while(!pq.isEmpty())
                result.add(0, pq.poll().getKey());
            
            return result;
        }
    }
    


### Solution 2
This problem is quite similar to the problem [Top K Frequent
Elements](https://leetcode.com/problems/top-k-frequent-elements/description/).
You can refer to [this post](https://discuss.leetcode.com/topic/44237/java-o-
n-solution-bucket-sort) for the solution of the problem.

We can solve this problem with the similar idea:  
Firstly, we need to calculate the frequency of each word and store the result
in a hashmap.

Secondly, we will use bucket sort to store words. Why? Because the minimum
frequency is greater than or equal to 1 and the maximum frequency is less than
or equal to the length of the input string array.

Thirdly, we can define a trie within each bucket to store all the words with
the same frequency. With Trie, it ensures that the lower alphabetical word
will be met first, saving the trouble to sort the words within the bucket.

From the above analysis, we can see the time complexity is O(n).  
Here is my code:

    
    
    public List<String> topKFrequent(String[] words, int k) {
            // calculate frequency of each word
            Map<String, Integer> freqMap = new HashMap<>();
            for(String word : words) {
                freqMap.put(word, freqMap.getOrDefault(word, 0) + 1);
            }
            // build the buckets
            TrieNode[] count = new TrieNode[words.length + 1];
            for(String word : freqMap.keySet()) {
                int freq = freqMap.get(word);
                if(count[freq] == null) {
                    count[freq] = new TrieNode();
                }
                addWord(count[freq], word);
            }
            // get k frequent words
            List<String> list = new LinkedList<>();
            for(int f = count.length - 1; f >= 1 && list.size() < k; f--) {
                if(count[f] == null) continue;
                getWords(count[f], list, k);
            }
            return list;
        }
        
        private void getWords(TrieNode node, List<String> list, int k) {
            if(node == null) return;
            if(node.word != null) {
                list.add(node.word);
            }
            if(list.size() == k) return;
            for(int i = 0; i < 26; i++) {
                if(node.next[i] != null) {
                    getWords(node.next[i], list, k);
                }
            }
        }
        
        private boolean addWord(TrieNode root, String word) {
            TrieNode curr = root;
            for(char c : word.toCharArray()) {
                if(curr.next[c - 'a'] == null) {
                    curr.next[c - 'a'] = new TrieNode();
                }
                curr = curr.next[c - 'a'];
            }
            curr.word = word;
            return true;
        }
        
        class TrieNode {
            TrieNode[] next;
            String word;
            TrieNode() {
                this.next = new TrieNode[26];
                this.word = null;
            }
        }
    


### Solution 3
    
    
    import collections
    import heapq
    import functools
    
    @functools.total_ordering
    class Element:
        def __init__(self, count, word):
            self.count = count
            self.word = word
            
        def __lt__(self, other):
            if self.count == other.count:
                return self.word > other.word
            return self.count < other.count
        
        def __eq__(self, other):
            return self.count == other.count and self.word == other.word
    
    class Solution(object):
        def topKFrequent(self, words, k):
            """
            :type words: List[str]
            :type k: int
            :rtype: List[str]
            """
            counts = collections.Counter(words)   
            
            freqs = []
            heapq.heapify(freqs)
            for word, count in counts.items():
                heapq.heappush(freqs, (Element(count, word), word))
                if len(freqs) > k:
                    heapq.heappop(freqs)
            
            res = []
            for _ in range(k):
                res.append(heapq.heappop(freqs)[1])
            return res[::-1]
        
    



