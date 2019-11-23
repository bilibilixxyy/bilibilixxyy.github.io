---
layout: post
title: 1140. Distant Barcodes
---
### Question
In a warehouse, there is a row of barcodes, where the `i`-th barcode is
`barcodes[i]`.

Rearrange the barcodes so that no two adjacent barcodes are equal.  You may
return any answer, and it is guaranteed an answer exists.



 **Example 1:**

    
    
     **Input:** [1,1,1,2,2,2]
    **Output:** [2,1,2,1,2,1]
    

**Example 2:**

    
    
    **Input:** [1,1,1,1,2,2,3,3]
    **Output:** [1,3,1,3,2,1,2,1]



 **Note:**

  1. `1 <= barcodes.length <= 10000`
  2. `1 <= barcodes[i] <= 10000`

### Solution 1
# Intuition

In the worst case, we can have `(N + 1) / 2` occurrences of the same barcode.
This barcode needs to be placed in `[0, 2, 4 ...]` positions to avoid the
repetition.

# Solution

  1. Count occurrences of each barcode using a hash map
  2. Use a set to sort barcodes by their number of occurrences
  3. Starting from most frequent, fill even positions with barcodes
  4. Then fill odd positions with remaining barcodes

![image](https://assets.leetcode.com/users/votrubac/image_1558849617.png)

    
    
    vector<int> rearrangeBarcodes(vector<int>& b, int pos = 0) {
      unordered_map<int, int> m;
      set<pair<int, int>> s;
      for (auto n : b) ++m[n];
      for (auto it = begin(m); it != end(m); ++it) s.insert({ it->second, it->first });
      for (auto it = s.rbegin(); it != s.rend(); ++it) {
        for (auto cnt = 0; cnt < it->first; ++cnt, pos += 2) {
          if (pos >= b.size()) pos = 1;
          b[pos] = it->second;
        }
      }
      return b;
    }
    

## Complexity Analysis

Runtime: _O(n log n)_ , where _n_ is the number of unique elements.  
Memory: _O(n)_. We store unique elements in the map and set.

# O(N) Solution

Like [Jianwen](https://leetcode.com/1033051159/) observed below, we do not
need to sort all unique elements, we just need to determine the most frequent
one and fill it first. The rest can be filled in any order.

Since barcodes are limited to `[1...10000]`, we can use an array instead of
hash map to make it even faster.

    
    
    vector<int> rearrangeBarcodes(vector<int>& b) {
      short m[10001] = {};
      short max_cnt = 0, max_n = 0, pos = 0;
      for (auto n : b) {
          max_cnt = max(max_cnt, ++m[n]);
          max_n = max_cnt == m[n] ? n : max_n;
      }
      for (auto i = 0; i <= 10000; ++i) {
        auto n = i == 0 ? max_n : i;
        while (m[n]-- > 0) {
            b[pos] = n;
            pos = pos + 2 < b.size() ? pos + 2 : 1;
        }
      }
      return b;
    }
    

## Complexity Analysis

Runtime: _O(N)_ , where _N_ is the total number of elements.  
Memory: _O(n)_ , where _n_ is the number of unique elements we track the count
for.


### Solution 2
Sort bar codes depending on its occurrence.  
We put the most frequent on every two positions,(first, third, fifth...)  
In this we, we make sure that no two adjacent bar codes are equal.

 **Python:**

    
    
        def rearrangeBarcodes(self, packages):
            i, n =  0, len(packages)
            res = [0] * n
            for k, v in collections.Counter(packages).most_common():
                for _ in xrange(v):
                    res[i] = k
                    i += 2
                    if i >= n: i = 1
            return res
    

Shorter version:

    
    
        def rearrangeBarcodes(self, A):
            count = collections.Counter(A)
            A.sort(key=lambda a: (count[a], a))
            A[1::2], A[::2] = A[0:len(A) / 2], A[len(A) / 2:]
            return A
    


### Solution 3
  * Store all the elements along with their number of occurrences in a HashMap.
  * Add them to a PriorityQueue in descending order of their occurrences.
  * Each time, poll an element from the head of the queue to add to the result list, decrease the occurrences of that element and store it in a temporary list to freeze it from being picked for K elements where K = 2.
  * Add elements from the temporary list back into the PriorityQueue and repeat until the PriorityQueue is empty.
  * Return the result array.

    
    
    class Solution {
        public int[] rearrangeBarcodes(int[] barcodes) {
            if(barcodes == null || barcodes.length == 0)
                return new int[0];
            Map<Integer, Integer> map = new HashMap<Integer, Integer>();
            for(int i: barcodes)
                map.put(i, map.getOrDefault(i, 0) + 1);
            PriorityQueue<Map.Entry<Integer, Integer>> pq = new PriorityQueue<Map.Entry<Integer, Integer>>(
    		(a,b)->b.getValue()-a.getValue() == 0?a.getKey() - b.getKey(): b.getValue() - a.getValue());
            for(Map.Entry<Integer, Integer> entry:map.entrySet())
                pq.offer(entry);
            int[] res = new int[barcodes.length];
            int i = 0;
            while(!pq.isEmpty()) {
                int k = 2;
                List<Map.Entry> tempList = new ArrayList<Map.Entry>();
                while(k > 0 && !pq.isEmpty()) {
                    Map.Entry<Integer, Integer> head = pq.poll();
                    head.setValue(head.getValue() - 1);
                    res[i++] = head.getKey();
                    tempList.add(head);
                    k--;
                }
                for(Map.Entry<Integer, Integer> e: tempList) {
                    if(e.getValue() > 0) 
                        pq.add(e);
                }
                if(pq.isEmpty())
                    break;
            }
            return res;
        }
    }
    

Complexity Analysis  
Runtime: O(n log n), where n is the number of elements in the array.  
Memory: O(m). We store m unique elements in the map and temporary list.



