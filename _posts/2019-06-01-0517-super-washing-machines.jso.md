---
layout: post
title: 517. Super Washing Machines
---
### Question
You have **n** super washing machines on a line. Initially, each washing
machine has some dresses or is empty.

For each **move** , you could choose **any m** (1 ≤ m ≤ n) washing machines,
and pass **one dress** of each washing machine to one of its adjacent washing
machines **at the same time**.

Given an integer array representing the number of dresses in each washing
machine from left to right on the line, you should find the **minimum number
of moves** to make all the washing machines have the same number of dresses.
If it is not possible to do it, return -1.

 **Example1**

    
    
     **Input:** [1,0,5]
    
    **Output:** 3
    
    **Explanation:** 
    1st move:    1     0  <-- 5    =>    1     1     4
    2nd move:    1 <-- 1 <-- 4    =>    2     1     3    
    3rd move:    2     1 <-- 3    =>    2     2     2   
    

**Example2**

    
    
    **Input:** [0,3,0]
    
    **Output:** 2
    
    **Explanation:** 
    1st move:    0  <-- 3     0    =>    1     2     0    
    2nd move:    1     2 --> 0    =>    1     1     1     
    

**Example3**

    
    
    **Input:** [0,2,0]
    
    **Output:** -1
    
    **Explanation:** 
    It's impossible to make all the three washing machines have the same number of dresses. 
    

**Note:**  

  1. The range of n is [1, 10000].
  2. The range of dresses number in a super washing machine is [0, 1e5].

### Solution 1
    
    
    public class Solution {
        public int findMinMoves(int[] machines) {
            int total = 0; 
            for(int i: machines) total+=i;
            if(total%machines.length!=0) return -1;
            int avg = total/machines.length, cnt = 0, max = 0;
            for(int load: machines){
                cnt += load-avg; //load-avg is "gain/lose"
                max = Math.max(Math.max(max, Math.abs(cnt)), load-avg);
            }
            return max;
        }
    }
    

Let me use an example to briefly explain this. For example, your machines[] is
[0,0,11,5]. So your total is 16 and the target value for each machine is 4.
Convert the machines array to a kind of gain/lose array, we get: [-4,-4,7,1].
Now what we want to do is go from the first one and try to make all of them 0.  
To make the 1st machines 0, you need to give all its "load" to the 2nd
machines.  
So we get: [0,-8,7,1]  
then: [0,0,-1,1]  
lastly: [0,0,0,0], done.  
You don't have to worry about the details about how these machines give load
to each other. In this process, the least steps we need to eventually finish
this process is determined by the peak of abs(cnt) and the max of "gain/lose"
array. In this case, the peak of abs(cnt) is 8 and the max of gain/lose array
is 7. So the result is 8.

Some other example:  
machines: [0,3,0]; gain/lose array: [-1,2,-1]; max = 2, cnt = 0, -1, 1, 0, its
abs peak is 1. So result is 2.  
machines: [1,0,5]; gain/lose array: [-1,-2,3]; max = 3, cnt = 0, -1, -3, 0,
its abs peak is 3. So result is 3.


### Solution 2
First we check the sum of dresses in all machines. if that number cannot be
divided by count of machines, there is no solution.

Otherwise, we can always transfer a dress from one machine to another, one at
a time until every machines reach the same number, so there must be a
solution. In this way, the total actions is sum of operations on every
machine.

Since we can operate several machines at the same time, the minium number of
moves is the maximum number of necessary operations on every machine.

For a single machine, necessary operations is to transfer dresses from one
side to another until sum of both sides and itself reaches the average number.
We can calculate (required dresses) - (contained dresses) of each side as L
and R:

L > 0 && R > 0: both sides lacks dresses, and we can only export one dress
from current machines at a time, so result is abs(L) + abs(R)  
L < 0 && R < 0: both sides contains too many dresses, and we can import
dresses from both sides at the same time, so result is max(abs(L), abs(R))  
L < 0 && R > 0 or L >0 && R < 0: the side with a larger absolute value will
import/export its extra dresses from/to current machine or other side, so
result is max(abs(L), abs(R))

For example, [1, 0, 5], average is 2  
for 1, L = 0 * 2 - 0 = 0, R = 2 * 2 - 5= -1, result = 1  
for 0, L = 1 * 2 - 1= 1, R = 1 * 2 - 5 = -3, result = 3  
for 5, L = 2 * 2 - 1= 3, R = 0 * 2 - 0= 0, result = 3  
so minium moves is 3

    
    
    class Solution {
    public:
        int findMinMoves(vector<int>& machines) {
            int len = machines.size();
            vector<int> sum(len + 1, 0);
            for (int i = 0; i < len; ++i)
                sum[i + 1] = sum[i] + machines[i];
    
            if (sum[len] % len) return -1;
    
            int avg = sum[len] / len;
            int res = 0;
            for (int i = 0; i < len; ++i)
            {
                int l = i * avg - sum[i];
                int r = (len - i - 1) * avg - (sum[len] - sum[i] - machines[i]);
    
                if (l > 0 && r > 0)
                    res = std::max(res, std::abs(l) + std::abs(r));
                else
                    res = std::max(res, std::max(std::abs(l), std::abs(r)));
            }
            return res;
        }
    };
    


### Solution 3
Instead of using some DP methodology to solve the problem, I have a very
intuitive way to approach the solution.

Think about the machine `i`, after we make all machines have the same dresses,
**how many dresses will be passed through machine`i`**?  
Let's denote the current sum of dresses of machines `[0...i-1]` as
`leftSums[i]`, and the current sum of dresses of machines `[i+1...n-1]` as
rightSums[i].  
Let's denote the expected sum of dresses of machines `[0...i-1]` as `expLeft`,
which means after all dresses are equally distributed, the sum of address in
machines `[0...i-1]` should be `expLeft`. The same logic applies to machines
`[i+1...n-1]`, denoted as `expRight`.

Then the above question should be clearly answered. If `expLeft` is larger
than `leftSums[i]`, that means no matter how you move the dresses, there will
be at least `expLeft - leftSums[i]` dresses being moved to left of machine
`i`, which means pass through machine `i`. For the right machines of machine
i, the logic remains the same. So we could conclude that the minimum dresses
passed through machine `i` will be:

    
    
    left = expLeft > leftSums[i] ? expLeft - leftSums[i] : 0;
    right = expRight > rightSums[i] ? expRight - rightSums[i] : 0;
    total = left + right;
    

With this answer in mind, we could know that the minimum moves is the maximum
dresses that pass through for each single machine, because for each dress, it
will require at least one move. Hence the following solution. The code could
be more concise, but I will leave it here for purpose of explanation.

If you have any doubts or suggestions for this solution, any comments are
welcome.

    
    
    public class Solution {
        public int findMinMoves(int[] machines) {
            int n = machines.length;
            int sum = 0;
            for (int num : machines) {
                sum += num;
            }
            if (sum % n != 0) {
                return -1;
            }
            int avg = sum / n;
            int[] leftSums = new int[n];
            int[] rightSums = new int[n];
            for (int i = 1; i < n; i ++) {
                leftSums[i] = leftSums[i-1] + machines[i-1];
            }
            for (int i = n - 2; i >= 0; i --) {
                rightSums[i] = rightSums[i+1] + machines[i+1];
            }
            int move = 0;
            for (int i = 0; i < n; i ++) {
                int expLeft = i * avg;
                int expRight = (n - i - 1) * avg;
                int left = 0;
                int right = 0;
                if (expLeft > leftSums[i]) {
                    left = expLeft - leftSums[i];
                } 
                if (expRight > rightSums[i]) {
                    right = expRight - rightSums[i];
                }
                move = Math.max(move, left + right);
            }
            return move;
        }
    }
    



