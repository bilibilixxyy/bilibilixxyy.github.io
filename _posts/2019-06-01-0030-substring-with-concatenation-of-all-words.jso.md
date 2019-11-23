---
layout: post
title: 30. Substring with Concatenation of All Words
---
### Question
You are given a string, **s** , and a list of words, **words** , that are all
of the same length. Find all starting indices of substring(s) in **s** that is
a concatenation of each word in **words** exactly once and without any
intervening characters.

 **Example 1:**

    
    
     **Input:
      s =** "barfoothefoobarman",
    **words =** ["foo","bar"]
    **Output:** [0,9]
    **Explanation:** Substrings starting at index 0 and 9 are "barfoor" and "foobar" respectively.
    The output order does not matter, returning [9,0] is fine too.
    

**Example 2:**

    
    
    **Input:
      s =** "wordgoodgoodgoodbestword",
    **words =** ["word","good","best","word"]
    **Output:** []
    

### Solution 1
I think the following code is self-explanatory enough. We use an
`unordered_map<string, int> counts` to record the expected times of each word
and another `unordered_map<string, int> seen` to record the times we have
seen. Then we check for every possible position of `i`. Once we meet an
unexpected word or the times of some word is larger than its expected times,
we stop the check. If we finish the check successfully, push `i` to the result
`indexes`.

  * C++

class Solution {  
public:  
vector findSubstring(string s, vector& words) {  
unordered_map<string, int> counts;  
for (string word : words)  
counts[word]++;  
int n = s.length(), num = words.size(), len = words[0].length();  
vector indexes;  
for (int i = 0; i < n - num * len + 1; i++) {  
unordered_map<string, int> seen;  
int j = 0;  
for (; j < num; j++) {  
string word = s.substr(i + j * len, len);  
if (counts.find(word) != counts.end()) {  
seen[word]++;  
if (seen[word] > counts[word])  
break;  
}  
else break;  
}  
if (j == num) indexes.push_back(i);  
}  
return indexes;  
}  
};

  * Java

    
    
    class Solution {
        public List<Integer> findSubstring(String s, String[] words) {
            final Map<String, Integer> counts = new HashMap<>();
            for (final String word : words) {
                counts.put(word, counts.getOrDefault(word, 0) + 1);
            }
            final List<Integer> indexes = new ArrayList<>();
            final int n = s.length(), num = words.length, len = words[0].length();
            for (int i = 0; i < n - num * len + 1; i++) {
                final Map<String, Integer> seen = new HashMap<>();
                int j = 0;
                while (j < num) {
                    final String word = s.substring(i + j * len, i + (j + 1) * len);
                    if (counts.containsKey(word)) {
                        seen.put(word, seen.getOrDefault(word, 0) + 1);
                        if (seen.get(word) > counts.getOrDefault(word, 0)) {
                            break;
                        }
                    } else {
                        break;
                    }
                    j++;
                }
                if (j == num) {
                    indexes.add(i);
                }
            }
            return indexes;
        }
    }
    


### Solution 2
    
    
        // travel all the words combinations to maintain a window
        // there are wl(word len) times travel
        // each time, n/wl words, mostly 2 times travel for each word
        // one left side of the window, the other right side of the window
        // so, time complexity O(wl * 2 * N/wl) = O(2N)
        vector<int> findSubstring(string S, vector<string> &L) {
            vector<int> ans;
            int n = S.size(), cnt = L.size();
            if (n <= 0 || cnt <= 0) return ans;
            
            // init word occurence
            unordered_map<string, int> dict;
            for (int i = 0; i < cnt; ++i) dict[L[i]]++;
            
            // travel all sub string combinations
            int wl = L[0].size();
            for (int i = 0; i < wl; ++i) {
                int left = i, count = 0;
                unordered_map<string, int> tdict;
                for (int j = i; j <= n - wl; j += wl) {
                    string str = S.substr(j, wl);
                    // a valid word, accumulate results
                    if (dict.count(str)) {
                        tdict[str]++;
                        if (tdict[str] <= dict[str]) 
                            count++;
                        else {
                            // a more word, advance the window left side possiablly
                            while (tdict[str] > dict[str]) {
                                string str1 = S.substr(left, wl);
                                tdict[str1]--;
                                if (tdict[str1] < dict[str1]) count--;
                                left += wl;
                            }
                        }
                        // come to a result
                        if (count == cnt) {
                            ans.push_back(left);
                            // advance one word
                            tdict[S.substr(left, wl)]--;
                            count--;
                            left += wl;
                        }
                    }
                    // not a valid word, reset all vars
                    else {
                        tdict.clear();
                        count = 0;
                        left = j + wl;
                    }
                }
            }
            
            return ans;
        }


### Solution 3
It's not too hard to find some resemblance between this problem and [minimum-
window-substring](https://leetcode.com/problems/minimum-window-substring/).
Actually the main difference is the fact that we are interested at some
interval length: we want intervals with fixed length K * M, where K is the
number of strings in the "words" array and M the length of each target string.
In order to apply the same idea we used for that problem, all we need to do is
to map each string from the "words" array to something we are able to index (I
prefer to use hashing for this). Also, in order to speed up the algorithm, we
can find all occurrences of those strings in S (which is equivalent to do it
on demand, but we will potentially do the same matching twice). Notice that,
we can simply apply these occurrences as they appear because we are assured
that no word is contained by some other. Finally, we use all this information
to process each possibility. Notice here that, the fact that all strings has
the same length, implies that we have just M (being M the length of each
target string) possible starting points, hence we end up performing M linear
scans over array with length O(N/M) (being N the length of S) and that makes
the scanning stage of the algorithm to be linear on the length of S.

    
    
    public List<Integer> findSubstring(String s, String[] words) {
    	int N = s.length();
    	List<Integer> indexes = new ArrayList<Integer>(s.length());
    	if (words.length == 0) {
    		return indexes;
    	}
    	int M = words[0].length();
    	if (N < M * words.length) {
    		return indexes;
    	}
    	int last = N - M + 1;
    	
    	//map each string in words array to some index and compute target counters
    	Map<String, Integer> mapping = new HashMap<String, Integer>(words.length);
    	int [][] table = new int[2][words.length];
    	int failures = 0, index = 0;
    	for (int i = 0; i < words.length; ++i) {
    		Integer mapped = mapping.get(words[i]);
    		if (mapped == null) {
    			++failures;
    			mapping.put(words[i], index);
    			mapped = index++;
    		}
    		++table[0][mapped];
    	}
    	
    	//find all occurrences at string S and map them to their current integer, -1 means no such string is in words array
    	int [] smapping = new int[last];
    	for (int i = 0; i < last; ++i) {
    		String section = s.substring(i, i + M);
    		Integer mapped = mapping.get(section);
    		if (mapped == null) {
    			smapping[i] = -1;
    		} else {
    			smapping[i] = mapped;
    		}
    	}
    	
    	//fix the number of linear scans
    	for (int i = 0; i < M; ++i) {
    		//reset scan variables
    		int currentFailures = failures; //number of current mismatches
    		int left = i, right = i;
    		Arrays.fill(table[1], 0);
    		//here, simple solve the minimum-window-substring problem
    		while (right < last) {
    			while (currentFailures > 0 && right < last) {
    				int target = smapping[right];
    				if (target != -1 && ++table[1][target] == table[0][target]) {
    					--currentFailures;
    				}
    				right += M;
    			}
    			while (currentFailures == 0 && left < right) {
    				int target = smapping[left];
    				if (target != -1 && --table[1][target] == table[0][target] - 1) {
    					int length = right - left;
    					//instead of checking every window, we know exactly the length we want
    					if ((length / M) ==  words.length) {
    						indexes.add(left);
    					}
    					++currentFailures;
    				}
    				left += M;
    			}
    		}
    		
    	}
    	return indexes;
    }
    



