---
layout: post
title: 1122. Longest Duplicate Substring
---
### Question
Given a string `S`, consider all _duplicated substrings_ : (contiguous)
substrings of S that occur 2 or more times.  (The occurrences may overlap.)

Return **any** duplicated  substring that has the longest possible length.
(If `S` does not have a duplicated substring, the answer is `""`.)



 **Example 1:**

    
    
     **Input:** "banana"
    **Output:** "ana"
    

**Example 2:**

    
    
    **Input:** "abcd"
    **Output:** ""
    



 **Note:**

  1. `2 <= S.length <= 10^5`
  2. `S` consists of lowercase English letters.

### Solution 1
##  **Intuition**

Suffix array is typical solution for this problem.  
The fastest way is to copy a template form the Internet.  
The code will be quite long.  
Here I want to share a binary search solution.  
  

##  **Explanation**

Binary search the length of longest duplicate substring and call the help
function `test(L)`.  
`test(L)` slide a window of length `L`,  
rolling hash the string in this window,  
record the `seen` string in a hashset,  
and try to find duplicated string.

I give it a big `mod` for rolling hash and it should be enough for this
problem.  
Actually there could be hash collision.  
One solution is to have two different mod for hash.  
Or we can use a hashmap to record the index of string.  
  

##  **Complexity**

Binary Search in range 1 and N, so it's `O(logN)`  
Rolling hash `O(N)`  
Overall `O(NlogN)`  
Space`O(N)`  
  

 **Python:**

    
    
        def longestDupSubstring(self, S):
            A = [ord( c) - ord('a') for c in S]
            mod = 2**63 - 1
    
            def test(L):
                p = pow(26, L, mod)
                cur = reduce(lambda x, y: (x * 26 + y) % mod, A[:L], 0)
                seen = {cur}
                for i in xrange(L, len(S)):
                    cur = (cur * 26 + A[i] - A[i - L] * p) % mod
                    if cur in seen: return i - L + 1
                    seen.add(cur)
            res, lo, hi = 0, 0, len(S)
            while lo < hi:
                mi = (lo + hi + 1) / 2
                pos = test(mi)
                if pos:
                    lo = mi
                    res = pos
                else:
                    hi = mi - 1
            return S[res:res + lo]
    


### Solution 2
**Intuition and Algorithm**  
Please check, Suffix array tutorial  
[https://www.geeksforgeeks.org/­­kasais-algorithm-for-construction-of-lcp-
array-from-suffix-array/](https://www.geeksforgeeks.org/%C2%AD%C2%ADkasais-
algorithm-for-construction-of-lcp-array-from-suffix-array/)  
<https://www.geeksforgeeks.org/suffix-array-set-1-introduction/>

Given a String we build its Suffix array and LCP (longest common Prefix).  
The maximum LCP is the size of longest duplicated substring.

For example:  
 **S= "banana"**

 **start- index** |  **LCP(array)**|  **suffix**  
---|---|---  
5 | 1 | a  
3 | 3 | **ana**  
1  |  0  |  anana  
0  |  0  |  banana  
4 |  2  |  na  
2  |  \-  |  nana  
  
**Approach 1 (Suffix Array)**

    
    
    	struct suffix { 
    		 int index; 
    		int rank[2];
    	}; 
    	
    	int cmp(struct suffix a, struct suffix b) { 
    		return (a.rank[0] == b.rank[0])? (a.rank[1] < b.rank[1] ?1: 0): 
    			(a.rank[0] < b.rank[0] ?1: 0); 
    	} 
    	
    	vector<int> buildSuffixArray(string txt, int n) { 
    		struct suffix suffixes[n]; 
    		for (int i = 0; i < n; i++) { 
    			suffixes[i].index = i; 
    			suffixes[i].rank[0] = txt[i] - 'a'; 
    			suffixes[i].rank[1] = ((i+1) < n)? (txt[i + 1] - 'a'): -1; 
    		} 
    	
    		sort(suffixes, suffixes+n, cmp); 
    		int ind[n];
    		
    		for (int k = 4; k < 2*n; k = k*2) { 
    			int rank = 0; 
    			int prev_rank = suffixes[0].rank[0]; 
    			suffixes[0].rank[0] = rank; 
    			ind[suffixes[0].index] = 0; 
    	
    			// Assigning rank to suffixes 
    			for (int i = 1; i < n; i++) { 
    				if (suffixes[i].rank[0] == prev_rank && 
    						suffixes[i].rank[1] == suffixes[i-1].rank[1]) 
    				{ 
    					prev_rank = suffixes[i].rank[0]; 
    					suffixes[i].rank[0] = rank; 
    				} 
    				else{ 
    					prev_rank = suffixes[i].rank[0]; 
    					suffixes[i].rank[0] = ++rank; 
    				} 
    				ind[suffixes[i].index] = i; 
    			} 
    	
    			for (int i = 0; i < n; i++) { 
    				int nextindex = suffixes[i].index + k/2; 
    				suffixes[i].rank[1] = (nextindex < n)? 
    									suffixes[ind[nextindex]].rank[0]: -1; 
    			} 
    			sort(suffixes, suffixes+n, cmp); 
    		} 
    	
    		vector<int>suffixArr; 
    		for (int i = 0; i < n; i++) 
    			suffixArr.push_back(suffixes[i].index); 
    	
    		return  suffixArr; 
    	} 
    	
    	vector<int> kasai(string txt, vector<int> suffixArr) { 
    		int n = suffixArr.size(); 
    	
    		// To store LCP array 
    		vector<int> lcp(n, 0); 
    		vector<int> invSuff(n, 0); 
    	
    		for (int i=0; i < n; i++) 
    			invSuff[suffixArr[i]] = i; 
    		
    		int k = 0;  
    		for (int i=0; i<n; i++) { 
    			if (invSuff[i] == n-1) { 
    				k = 0; 
    				continue; 
    			} 
    			int j = suffixArr[invSuff[i]+1];
    			while (i+k<n && j+k<n && txt[i+k]==txt[j+k]) 
    				k++; 
    	
    			lcp[invSuff[i]] = k;
    			if (k>0) 
    				k--; 
    		} 
    		return lcp; 
    	} 
    	
    	class Solution {
    	public:
    		string longestDupSubstring(string S) {
    			vector<int>suffixArr = buildSuffixArray(S, S.size()); 
    			int n = suffixArr.size();
    			vector<int>lcp = kasai(S, suffixArr); 
    			
    			int ans=0;
    			int start=0;
    			
    			for(int i=0;i<n;i++){
    				if(lcp[i]>ans){
    					ans = lcp[i];
    					start = suffixArr[i];
    				}     
    			}
    			
    			if(ans==0)return "";
    			return S.substr(start,ans);
    		}
    	};
    

**Complexity Analysis**

  * Time Complexity: O(n log(n)* log(n)) where n is the length of S
  * Space Complexity: O(n) where n is the length of S


### Solution 3
[https://en.wikipedia.org/wiki/Rabin–Karp_algorithm](https://en.wikipedia.org/wiki/Rabin%E2%80%93Karp_algorithm)

    
    
    This is a tricky one on two sides:
    1. how to find the length of the lonest string
    2. how to compare the string of the same length
    For the first point, we can use binary search for answer since if a string of length n is
    invalid then for all k > n, there's definetly no solution because length n strings would 
    become a substring of the length k string. Similarly if a string of length n is valid, we have
    no use of checking strings with length less than n. Due to these properties we can use
    binary search for final answer.
    For the second point, we are actually trying to compare a sliding window of string, and
    Rabin Karp algorithm is perfect for doing so. The algorithm basically computes the 
    hash value of all the string and start a character by character comparison only if the two 
    strings have the same hash value. In order to avoid collision we can use a large prime number
    such as 1e9 + 7, 19260817, 99999989, etc.
    The implementation looks as follows:
    class Solution {
    public:
        string longestDupSubstring(string S) {
            ans = "";
            power = vector<int>(S.length(), 1);
            int i;
    		// precompute all the pow(26, k) 0 < k < S.length() modulus prime
            for (i = 1 ; i < S.length(); i++) {
                power[i] = (power[i - 1] * 26) % prime;
            }
            int low = 0, high = S.length();
    		// code for the binary search, very trivial
            while (low <= high) {
                int mid = low + (high - low) / 2;
                string tmp = validate(mid, S);
                if (tmp.length() == 0) {
                    high = mid - 1;
                } else {
                    if (tmp.length() > ans.length()) {
                        ans = tmp;
                    }
                    low = mid + 1;
                }
            }
            
            return ans;
        }
        
    private:
       // large prime number
        int prime = 19260817;
        string ans;
        vector<int> power;
        string validate(int desire, string &str) {
           // if the desire length is 0, return the empty string
            if (desire == 0) return "";
            unordered_map<int, vector<int>> hash = unordered_map<int, vector<int>>();
            long long current = 0;
            int i;
    		// compute the hash value of the first "length" characters
            for (i = 0 ; i < desire; i++) {
                current = ((current * 26) % prime + (str[i] - 'a')) % prime;
            }
            // store the result in a hashmap that maps from hashvalue to starting index
            hash[current] = vector<int>(1, 0);
            for (i = desire ; i < str.length(); i++) {
    		    // sliding window to maintain the current substr's hash value
    			// be aware of overflow
                current = ((current - (long long) power[desire - 1] * (str[i - desire] - 'a')) % prime + prime) % prime;
                current = (current * 26 + (str[i] - 'a')) % prime;
               // if that hash value is not in our set we do nothing and add the value to our map
                if (hash.find(current) == hash.end()) {
                    hash[current] = vector<int>(1, i - desire + 1);
                } else {
    			   // otherwise, start a string by string comparason and see if there's a match
                    for (auto it : hash[current]) {
                        
                        if (strcmp((str.substr(it, desire)).data(), str.substr(i - desire + 1, desire).data()) == 0) {
                            return str.substr(it, desire);
                        }
                    }
                    
                    hash[current].push_back(i - desire + 1);
                }
            }
            
            return "";
        }
    };
    



