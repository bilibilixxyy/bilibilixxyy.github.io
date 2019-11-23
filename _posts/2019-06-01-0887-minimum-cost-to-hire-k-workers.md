---
layout: post
title: 887. Minimum Cost to Hire K Workers
---
### Question
There are `N` workers.  The `i`-th worker has a `quality[i]` and a minimum
wage expectation `wage[i]`.

Now we want to hire exactly `K` workers to form a _paid group_.   When hiring
a group of K workers, we must pay them according to the following rules:

  1. Every worker in the paid group should be paid in the ratio of their quality compared to other workers in the paid group.
  2. Every worker in the paid group must be paid at least their minimum wage expectation.

Return the least amount of money needed to form a paid group satisfying the
above conditions.



 **Example 1:**

    
    
     **Input:** quality = [10,20,5], wage = [70,50,30], K = 2
    **Output:** 105.00000
    **Explanation** : We pay 70 to 0-th worker and 35 to 2-th worker.
    

**Example 2:**

    
    
    **Input:** quality = [3,1,10,10,1], wage = [4,8,2,2,7], K = 3
    **Output:** 30.66667
    **Explanation** : We pay 4 to 0-th worker, 13.33333 to 2-th and 3-th workers seperately. 
    



 **Note:**

  1. `1 <= K <= N <= 10000`, where `N = quality.length = wage.length`
  2. `1 <= quality[i] <= 10000`
  3. `1 <= wage[i] <= 10000`
  4. Answers within `10^-5` of the correct answer will be considered correct.

### Solution 1
Let's read description first and figure out the two rules:

 **"1. Every worker in the paid group should be paid in the ratio of their
quality compared to other workers in the paid group."**  
So for any two workers in the paid group,  
we have `wage[i] : wage[j] = quality[i] : quality[j]`  
So we have `wage[i] : quality[i] = wage[j] : quality[j]`  
We pay wage to every worker in the group with the same ratio compared to his
own quality.

 **"2. Every worker in the paid group must be paid at least their minimum wage
expectation."  
For every worker, he has an expected ratio of wage compared to his quality.**

So to minimize the total wage, we want a small ratio.  
So we sort all workers with their expected ratio, and pick up `K` first
worker.  
Now we have a minimum possible ratio for `K` worker and we their total
quality.

As we pick up next worker with bigger ratio, we increase the ratio for whole
group.  
Meanwhile we remove a worker with highest quality so that we keep `K` workers
in the group.  
We calculate the current ratio * total quality = total wage for this group.

We redo the process and we can find the minimum total wage.  
Because workers are sorted by ratio of wage/quality.  
For every ratio, we find the minimum possible total quality of K workers.

 **Time Complexity**  
`O(NlogN)` for sort.  
`O(NlogK)` for priority queue.

 **C++:**

    
    
         double mincostToHireWorkers(vector<int> q, vector<int> w, int K) {
            vector<vector<double>> workers;
            for (int i = 0; i < q.size(); ++i)
                workers.push_back({(double)(w[i]) / q[i], (double)q[i]});
            sort(workers.begin(), workers.end());
            double res = DBL_MAX, qsum = 0;
            priority_queue<int> pq;
            for (auto worker: workers) {
                qsum += worker[1], pq.push(worker[1]);
                if (pq.size() > K) qsum -= pq.top(), pq.pop();
                if (pq.size() == K) res = min(res, qsum * worker[0]);
            }
            return res;
        }
    

**Java:**

    
    
         public double mincostToHireWorkers(int[] q, int[] w, int K) {
            double[][] workers = new double[q.length][2];
            for (int i = 0; i < q.length; ++i)
                workers[i] = new double[]{(double)(w[i]) / q[i], (double)q[i]};
            Arrays.sort(workers, (a, b) -> Double.compare(a[0], b[0]));
            double res = Double.MAX_VALUE, qsum = 0;
            PriorityQueue<Double> pq = new PriorityQueue<>();
            for (double[] worker: workers) {
                qsum += worker[1];
                pq.add(-worker[1]);
                if (pq.size() > K) qsum += pq.poll();
                if (pq.size() == K) res = Math.min(res, qsum * worker[0]);
            }
            return res;
        }
    

**Python:**

    
    
         def mincostToHireWorkers(self, quality, wage, K):
            workers = sorted([float(w) / q, q] for w, q in zip(wage, quality))
            res = float('inf')
            qsum = 0
            heap = []
            for r, q in workers:
                heapq.heappush(heap, -q)
                qsum += q
                if len(heap) > K: qsum += heapq.heappop(heap)
                if len(heap) == K: res = min(res, qsum * r)
            return res
    

**FAQ** :  
 **Question:** "However, it is possible that current worker has the highest
quality, so you removed his quality in the last step, which leads to the
problem that you are "using his ratio without him".  
 **Answer:** It doesn't matter. The same group will be calculated earlier with
smaller ratio.  
And it doesn't obey my logic here: For a given ratio of wage/quality, find
minimum total wage of K workers.


### Solution 2
 **Thought**  
Every worker in the paid group should be paid in the ratio of their quality
compared to other workers in the paid group.  
->
    
    
     quality[i]   money[i] 
     _________  = ________
     quality[j]   money[j]
     
     money[i] / quality[i] = money[j] / quality[j] -- ratio
    

That is, **the group of people should have the same ratio**.

Every worker in the paid group must be paid at least their minimum wage
expectation.  
->
    
    
    money[i] >= wage[i]
    ratio >= wage[i] / quality[i] -- ratio2
    

->  
**`ratio` is at least the maximum `ratio2` within the group of K people**.

If we keep current candidates in a priority queue as below,

    
    
    |-- < k candidates--|    
    			new worker to join is with higher ratio2
    

If there are k + 1candidates, we pop the highest quality to reduce total cost.  
If there are k candidates, we can calculate the total cost.

    
    
        public double mincostToHireWorkers(int[] quality, int[] wage, int K) {       
            int numWorkers = quality.length;
            
            /* qualityRatio[i] = {quality, wage[i] / quality[i]}. */
            double[][] qualityRatio = new double[numWorkers][2];
            
            for (int i = 0; i < numWorkers; i++) {
                qualityRatio[i][0] = quality[i];
                qualityRatio[i][1] = (double) wage[i] / quality[i];
            }
            
            Arrays.sort(qualityRatio, (a, b) -> Double.compare(a[1], b[1]));
            double minSumSalary = Integer.MAX_VALUE;
            int sumQuality = 0;;
            
            /* Always remove maximum quality. */
            PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> Integer.compare(b, a));
            
            for (int i = 0; i < numWorkers; i++) {
                maxHeap.add((int)qualityRatio[i][0]);
                sumQuality += qualityRatio[i][0];
                if (maxHeap.size() > K) {
                    int qualityPolled = maxHeap.poll();
                    sumQuality -= qualityPolled;
                }        
                if (maxHeap.size() == K) {
                    
                    /* Calculate sumSalary. */
                    double curRatio = qualityRatio[i][1];
                    minSumSalary = Math.min(minSumSalary, sumQuality * curRatio);
                }
            }
            
            return minSumSalary;
        }
    

**(ﾉ >ω<)ﾉ Vote up, please!**


### Solution 3
If you're looking for a hint, but want to solve the code on you're own, here's
my notes assembled as I solved this problem:

    
    
        // Some things I derived while figuring out this problem:
        // Most importantly: You must pay at least one worker his minimum requested wage (otherwise you are wasting money).
        //                   Who this worker is allows you to pretty easily assemble the rest of the team. To minimize cost,
        //                   you want to minimize the total experience in the group, because the equation for total cost is:
        //         Given fixed worker with wage Wf and quality Qf and total sum of quality in the group Qs:
        //         Cost = Wf * Qs / Qf
        //                   Thus, given a fixed worker that is paid their wage, you can minimize the cost by minimizing Qs, the
        //                   total sum of "quality" in the group.
        //
        // However: Who you pick as the worker with the fixed wage determines who you can add to the group. This is because the ratio
        //          of quality to minimum cost will mean you would pay some workers you added to the group less then their minimum wage.
        //          Given the Wf, Qf above, it can be derived that you will pay any worker with quality Q added to the group:
        //          Wage = Wf * Qs / Qf * Q / Qs (which simplifies to:)
        //               = Wf * Q / Qf
        //          With the above equation on hand, and the knowledge that you must pay any worker in the group their minimum wage, W, you can 
        //          derive a more useful equation:
        //          W <= Wf * Q / Qf (which simlifies to:)
        //          W / Q <= Wf / Qf
        //          This means that the ratio of Wf / Qf for the worker you select must be the highest in the group you assemble.
        //
        // From here, solving this problem involves a few steps:
        // 1) Sort the input array by Wf / Qf.
        // 2) Assemble a list of K workers with the lowest Wf / Qf ratios. This is the group with the smallest possible Wf / Qf ratios, dominated
        //    by a "fixed" worker (Wf/Qf from above) at the last index/node. You should be able to compute the cost of this group of workers
        //    using the first equation derived above.
        // 3) Work along the rest of the sorted Wf/Qf workers. For each additional worker, remove the worker with the highest "quality" from the
        //    list from (2) and add in your new "next-highest" Wf/Qf worker. This will allow you to keep track of your Wf/Qf values as well as
        //    maintain a group of optimally low quality to minimize total cost. Compute the cost for each step and record the minimum.
    



