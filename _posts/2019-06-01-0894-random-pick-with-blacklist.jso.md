---
layout: post
title: 894. Random Pick with Blacklist
---
### Question
Given a blacklist `B` containing unique integers from `[0, N)`, write a
function to return a uniform random integer from `[0, N)` which is **NOT**  in
`B`.

Optimize it such that it minimizes the call to system’s `Math.random()`.

 **Note:**

  1. `1 <= N <= 1000000000`
  2. `0 <= B.length < min(100000, N)`
  3. `[0, N)` does NOT include N. See [interval notation](https://en.wikipedia.org/wiki/Interval_\(mathematics\)).

 **Example 1:**

    
    
     **Input:** ["Solution","pick","pick","pick"]
    [[1,[]],[],[],[]]
    **Output:** [null,0,0,0]
    

**Example 2:**

    
    
    **Input:** ["Solution","pick","pick","pick"]
    [[2,[]],[],[],[]]
    **Output:** [null,1,1,1]
    

**Example 3:**

    
    
    **Input:** ["Solution","pick","pick","pick"]
    [[3,[1]],[],[],[]]
    **Output:** [null,0,0,2]
    

**Example 4:**

    
    
    **Input:** ["Solution","pick","pick","pick"]
    [[4,[2]],[],[],[]]
    **Output:** [null,1,3,1]
    

**Explanation of Input Syntax:**

The input is two lists: the subroutines called and their arguments.
`Solution`'s constructor has two arguments, `N` and the blacklist `B`. `pick`
has no arguments. Arguments are always wrapped with a list, even if there
aren't any.

### Solution 1
Suppose N=10, blacklist=[3, 5, 8, 9], re-map 3 and 5 to 7 and 6.  
![image](https://s3-lc-
upload.s3.amazonaws.com/users/cafebaby/image_1530657902.png)

    
    
    class Solution {
        
        // N: [0, N)
        // B: blacklist
        // B1: < N
        // B2: >= N
        // M: N - B1
        int M;
        Random r;
        Map<Integer, Integer> map;
    
        public Solution(int N, int[] blacklist) {
            map = new HashMap();
            for (int b : blacklist) // O(B)
                map.put(b, -1);
            M = N - map.size();
            
            for (int b : blacklist) { // O(B)
                if (b < M) { // re-mapping
                    while (map.containsKey(N - 1))
                        N--;
                    map.put(b, N - 1);
                    N--;
                }
            }
            
            r = new Random();
        }
        
        public int pick() {
            int p = r.nextInt(M);
            if (map.containsKey(p))
                return map.get(p);
            return p;
        }
    }
    


### Solution 2
It is not recommand to use `rand()` when N is large.  
<https://en.cppreference.com/w/cpp/numeric/random/uniform_int_distribution>

Other solutions which use `rand()/M` are wrong.  
`rand()` generates random numbers in [0, RAND_MAX], where `RAND_MAX` depends
on the complier.  
Usually, `RAND_MAX` is 32767 (in leetcode it is 2147483647), which could not
generate numbers larger than it. In this problem, `N` could be 1000000000, so
we cannot directly use `rand()/M` here.

Another problem is, even if N is less than 10000, it is also not accurate to
use `rand()/M`.  
For example, use `rand()/10000` to generate numbers in [0,9999]. It is more
likely to generate a number in [0, 2767] than in [2868, 9999]:

    
    
    [0,2767] % 10000->[0,2767]
    [2768,9999] % 10000->[2768,9999]
    [10000,12767] % 10000->[0,2767]
    [12768,19999] % 10000->[2768,9999]
    [20000,22767] % 10000->[0,2767]
    [22768,29999] % 10000->[2768,9999]
    [30000,32767] % 10000->[0,2767]
    

Here is my code, O(b log b) construction and O(log b) pick. (not the fastest,
see other solutions)

    
    
    class Solution {
    public:
        vector<int> v;
        std::mt19937 gen;
        std::uniform_int_distribution<> dis;
        Solution(int N, vector<int> blacklist) {
            v = blacklist;
            sort(v.begin(), v.end());
            v.push_back(N);
            for (int i = 0; i < v.size(); i++) v[i] -= i;
            
            std::random_device rd;  //Will be used to obtain a seed for the random number engine
            gen = std::mt19937(rd()); //Standard mersenne_twister_engine seeded with rd()
            dis = std::uniform_int_distribution<>(0, N - v.size());
        }
        
        int pick() {
            int rnd = dis(gen);
            auto it = upper_bound(v.begin(), v.end(), rnd) - 1;
            int idx = it - v.begin();
            return idx + rnd + 1;
        }
    };
    
    /**
     * Your Solution object will be instantiated and called as such:
     * Solution obj = new Solution(N, blacklist);
     * int param_1 = obj.pick();
     */
    


### Solution 3
Treat the first N - |B| numbers as those we can pick from. Iterate through the
blacklisted numbers and map each of them to to one of the remaining non-
blacklisted |B| numbers

For picking, just pick a random uniform int in 0, N - |B|. If its not
blacklisted, return the number. If it is, return the number that its mapped to

    
    
    import random
    
    class Solution:
        def __init__(self, N, blacklist):
            blacklist = sorted(blacklist)
            self.b = set(blacklist)
            self.m = {}
            self.length = N - len(blacklist)
            j = 0
            for i in range(self.length, N):
                if i not in self.b:
                    self.m[blacklist[j]] = i
                    j += 1
    
        def pick(self):
            i = random.randint(0, self.length - 1)
            return self.m[i] if i in self.m else i
    



