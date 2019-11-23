---
layout: post
title: 433. Minimum Genetic Mutation
---
### Question
A gene string can be represented by an 8-character long string, with choices
from `"A"`, `"C"`, `"G"`, `"T"`.

Suppose we need to investigate about a mutation (mutation from "start" to
"end"), where ONE mutation is defined as ONE single character changed in the
gene string.

For example, `"AACCGGTT"` -> `"AACCGGTA"` is 1 mutation.

Also, there is a given gene "bank", which records all the valid gene
mutations. A gene must be in the bank to make it a valid gene string.

Now, given 3 things - start, end, bank, your task is to determine what is the
minimum number of mutations needed to mutate from "start" to "end". If there
is no such a mutation, return -1.

 **Note:**

  1. Starting point is assumed to be valid, so it might not be included in the bank.
  2. If multiple mutations are needed, all mutations during in the sequence must be valid.
  3. You may assume start and end string is not the same.



 **Example 1:**

    
    
    start: "AACCGGTT"
    end:   "AACCGGTA"
    bank: ["AACCGGTA"]
    
    return: 1
    



**Example 2:**

    
    
    start: "AACCGGTT"
    end:   "AAACGGTA"
    bank: ["AACCGGTA", "AACCGCTA", "AAACGGTA"]
    
    return: 2
    



**Example 3:**

    
    
    start: "AAAAACCC"
    end:   "AACCCCCC"
    bank: ["AAAACCCC", "AAACCCCC", "AACCCCCC"]
    
    return: 3
    

### Solution 1
    
    
     public class Solution {
        public int minMutation(String start, String end, String[] bank) {
            if(start.equals(end)) return 0;
            
            Set<String> bankSet = new HashSet<>();
            for(String b: bank) bankSet.add(b);
            
            char[] charSet = new char[]{'A', 'C', 'G', 'T'};
            
            int level = 0;
            Set<String> visited = new HashSet<>();
            Queue<String> queue = new LinkedList<>();
            queue.offer(start);
            visited.add(start);
            
            while(!queue.isEmpty()) {
                int size = queue.size();
                while(size-- > 0) {
                    String curr = queue.poll();
                    if(curr.equals(end)) return level;
                    
                    char[] currArray = curr.toCharArray();
                    for(int i = 0; i < currArray.length; i++) {
                        char old = currArray[i];
                        for(char c: charSet) {
                            currArray[i] = c;
                            String next = new String(currArray);
                            if(!visited.contains(next) && bankSet.contains(next)) {
                                visited.add(next);
                                queue.offer(next);
                            }
                        }
                        currArray[i] = old;
                    }
                }
                level++;
            }
            return -1;
        }
    }
    


### Solution 2
If the length of gene is limited, maybe we can consider to encode ATGC with 2
bits

    
    
    class Solution {
    public:
        int minMutation(string start, string end, vector<string>& bank) {
            unordered_set<string> dict(bank.begin(), bank.end());
            if (!dict.count(end)) return -1;
            unordered_set<string> bset, eset, *set1, *set2;
            bset.insert(start), eset.insert(end);
            int step = 0, n = start.size();
            while (!bset.empty() and !eset.empty()) {
                if (bset.size() <= eset.size())
                    set1 = &bset, set2 = &eset;
                else set2 = &bset, set1 = &eset;
                unordered_set<string> tmp;
                step ++;
                for (auto itr = set1->begin(); itr != set1->end(); ++itr) {
                    for (int i = 0; i < n; ++i) {
                        string dna = *itr;
                        for (auto g : string("ATGC")) {
                            dna[i] = g;
                            if (set2->count(dna)) return step;
                            if (dict.count(dna)) {
                                tmp.insert(dna);
                                dict.erase(dna);
                            }
                        }
                    }
                }
                *set1 = tmp;
            }
            return -1;
        }
    };
    


### Solution 3
    
    
    import collections
    
    def viableMutation(current_mutation, next_mutation):
        changes = 0
        for i in xrange(len(current_mutation)):
            if current_mutation[i] != next_mutation[i]:
                changes += 1
        return changes == 1
    
    class Solution(object):
        def minMutation(self, start, end, bank):
            """
            :type start: str
            :type end: str
            :type bank: List[str]
            :rtype: int
            """
            queue = collections.deque()
            queue.append([start, start, 0]) # current, previous, num_steps
            while queue:
                current, previous, num_steps = queue.popleft()
                if current == end:  # in BFS, the first instance of current == end will yield the minimum
                    return num_steps
                for string in bank:
                    if viableMutation(current, string) and string != previous:
                        queue.append([string, current, num_steps+1])
            return -1
    



