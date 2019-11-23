---
layout: post
title: 853. Most Profit Assigning Work
---
### Question
We have jobs: `difficulty[i]` is the difficulty of the `i`th job, and
`profit[i]` is the profit of the `i`th job.

Now we have some workers. `worker[i]` is the ability of the `i`th worker,
which means that this worker can only complete a job with difficulty at most
`worker[i]`.

Every worker can be assigned at most one job, but one job can be completed
multiple times.

For example, if 3 people attempt the same job that pays $1, then the total
profit will be $3.  If a worker cannot complete any job, his profit is $0.

What is the most profit we can make?

 **Example 1:**

    
    
     **Input:** difficulty = [2,4,6,8,10], profit = [10,20,30,40,50], worker = [4,5,6,7]
    **Output:** 100 
    **Explanation: W** orkers are assigned jobs of difficulty [4,4,6,6] and they get profit of [20,20,30,30] seperately.

 **Notes:**

  * `1 <= difficulty.length = profit.length <= 10000`
  * `1 <= worker.length <= 10000`
  * `difficulty[i], profit[i], worker[i]`  are in range `[1, 10^5]`

### Solution 1
# Solution 1

  1. zip `difficulty` and `profit` as jobs.
  2. sort `jobs` and sort 'worker'.
  3. Use 2 pointers. For each worker, find his maximum profit `best` he can make under his ability.  
  

Because we have sorted `jobs` and `worker`,  
we will go through two lists only once.  
this will be only `O(D + W)`.  
`O(DlogD + WlogW)`, as we sort jobs.  
  

 **C++**

    
    
         int maxProfitAssignment(vector<int>& difficulty, vector<int>& profit, vector<int>& worker) {
            vector<pair<int, int>> jobs;
            int N = profit.size(), res = 0, i = 0, best = 0;
            for (int j = 0; j < N; ++j)
                jobs.push_back(make_pair(difficulty[j], profit[j]));
            sort(jobs.begin(), jobs.end());
            sort(worker.begin(), worker.end());
            for (int & ability : worker) {
                while (i < N && ability >= jobs[i].first)
                    best = max(jobs[i++].second, best);
                res += best;
            }
            return res;
        }
    

**Java:**

    
    
         public int maxProfitAssignment(int[] difficulty, int[] profit, int[] worker) {
            List<Pair<Integer, Integer>> jobs = new ArrayList<>();
            int N = profit.length, res = 0, i = 0, best = 0;
            for (int j = 0; j < N; ++j)
                jobs.add(new Pair<Integer, Integer>(difficulty[j], profit[j]));
            Collections.sort(jobs, Comparator.comparing(Pair::getKey));
            Arrays.sort(worker);
            for (int ability : worker) {
                while (i < N && ability >= jobs.get(i).getKey())
                    best = Math.max(jobs.get(i++).getValue(), best);
                res += best;
            }
            return res;
        }
    

**Python:**

    
    
         def maxProfitAssignment(self, difficulty, profit, worker):
            jobs = sorted(zip(difficulty, profit))
            res = i = best = 0
            for ability in sorted(worker):
                while i < len(jobs) and ability >= jobs[i][0]:
                    best = max(jobs[i][1], best)
                    i += 1
                res += best
            return res
    
    

  

# Solution 2

  1. Use a treemap<difficulty, profit>
  2. Go through the treemap once, find the max profit `best` for each difficulty.  
Time `O(DlogD + WlogD)`  
  

**Java**

    
    
         public int maxProfitAssignment(int[] difficulty, int[] profit, int[] worker) {
            TreeMap<Integer, Integer> map = new TreeMap<>();
            map.put(0, 0);
            for (int i = 0; i < difficulty.length; i++) {
                map.put(difficulty[i], Math.max(profit[i], map.getOrDefault(difficulty[i], 0)));
            }
            int best = 0, res = 0;
            for (Integer key : map.keySet()) {
                best = Math.max(map.get(key), best);
                map.put(key, best);
            }
            for (int i = 0; i < worker.length; i++) {
                res += map.floorEntry(worker[i]).getValue();
            }
            return res;
        }
    

**C++**

    
    
         int maxProfitAssignment(vector<int>& difficulty, vector<int>& profit, vector<int>& worker) {
            int N = profit.size(), res = 0, i = 0, best = 0;
            map<int, int> jobs;
            jobs[0] = 0;
            for (int j = 0; j < N; ++j)
                jobs[difficulty[j]] = max(jobs[difficulty[j]], profit[j]);
            for (auto &it: jobs)
                it.second = best = max(best, it.second);
            for (int & ability : worker)
                res += (--jobs.upper_bound(ability))->second;
            return res;
        }
    


### Solution 2
    
    
    public int maxProfitAssignment(int[] difficulty, int[] profit, int[] worker) {
           
            TreeMap<Integer, Integer> tmap = new TreeMap<>();
            // in case two jobs have same difficulty but different profit, we want to count
            // the higher profit
            for (int i = 0; i < difficulty.length; i++) {
                tmap.put(difficulty[i], Math.max(profit[i], tmap.getOrDefault(difficulty[i], 0)));
            }
    
            int max = 0, res = 0;
            // maximum profit at this difficulty or below in case
            // lower difficulty job offers higher profit
            for (Integer key : tmap.keySet()) {
                max = Math.max(tmap.get(key), max);
                tmap.put(key, max);
            }
            
            Map.Entry<Integer, Integer> entry = null;
            for (int i = 0; i < worker.length; i++) {
                if (tmap.containsKey(worker[i])) {
                    res += tmap.get(worker[i]);
                } else {
                    entry = tmap.floorEntry(worker[i]);            
                    if (entry != null) {
                        res += entry.getValue();
                    }
                }  
            }
            return res;  
    }


### Solution 3
This solution sorts levels and assigns the maximum profit among all jobs with
equal or lower level:

  1. Load levels and profits into a sorted map.
  2. Update the map so that each level has the maximum possible profit. Practically, if the previous level has higher profit, we assign that profit to the current level.
  3. For each worker, search the map to get the maximum profit for the worker's level.

The complexity is O((n + m) log n), where n and m is the number of jobs and
workers respectfully.

    
    
    int maxProfitAssignment(vector<int>& lv, vector<int>& profit, vector<int>& worker) {
      map<int, int> m = { { 0, 0 } };
      for (auto i = 0; i < lv.size(); ++i) m[lv[i]] = max(m[lv[i]], profit[i]);
      for (auto it = next(m.begin()); it != m.end(); ++it) it->second = max(it->second, prev(it)->second);
      return accumulate(begin(worker), end(worker), 0, [&](int s, int w) {return s + prev(m.upper_bound(w))->second;});
    }
    

As a bonus, below is the solution that uses bucket sort. It requires, however,
additional memory but the runtime complexity is O(N + m), where N is the
number of levels (100,000 in our case). The second solution is only slightly
faster in OJ (100 vs. 110 ms), probably because n is signifficantly smaller
than N in the test cases.

    
    
    int maxProfitAssignment(vector<int>& lv, vector<int>& profit, vector<int>& worker) {
      int m[100001] = {};
      for (auto i = 0; i < lv.size(); ++i) m[lv[i]] = max(m[lv[i]], profit[i]);
      for (auto i = 1; i < 100001; ++i) m[i] = max(m[i], m[i - 1]);
      return accumulate(begin(worker), end(worker), 0, [&](int s, int w) { return s + m[w]; });
    }
    

I also checked the sort and two-pointer solution by @lee215. That solution
requires two sorts, that's why my preference was to sort once and fix profits
in the linear time. Anyway, I adopted the two-pointer solution to use the
bucket sort. Note that I store the number of workers with a given level in the
second array.

    
    
    int maxProfitAssignment(vector<int>& lv, vector<int>& profit, vector<int>& worker) {
      int m[100001] = {}, w[100001] = {}, p = 0, max_p = 0;
      for (auto i = 0; i < lv.size(); ++w[worker[i++]]) m[lv[i]] = max(m[lv[i]], profit[i]);
      return accumulate(begin(w), end(w), 0, [&](int s, int w) { max_p = max(max_p, m[p++]); return s + max_p * w; });
    }
    



