---
layout: post
title: 1113. Moving Stones Until Consecutive II
---
### Question
On an **infinite** number line, the position of the i-th stone is given by
`stones[i]`.  Call a stone an _endpoint stone_ if it has the smallest or
largest position.

Each turn, you pick up an endpoint stone and move it to an unoccupied position
so that it is no longer an endpoint stone.

In particular, if the stones are at say, `stones = [1,2,5]`, you **cannot**
move the endpoint stone at position 5, since moving it to any position (such
as 0, or 3) will still keep that stone as an endpoint stone.

The game ends when you cannot make any more moves, ie. the stones are in
consecutive positions.

When the game ends, what is the minimum and maximum number of moves that you
could have made?  Return the answer as an length 2 array: `answer =
[minimum_moves, maximum_moves]`



 **Example 1:**

    
    
     **Input:** [7,4,9]
    **Output:** [1,2]
    **Explanation:**
    We can move 4 - > 8 for one move to finish the game.
    Or, we can move 9 -> 5, 4 -> 6 for two moves to finish the game.
    

**Example 2:**

    
    
    **Input:** [6,5,4,3,10]
    **Output:** [2,3]
    We can move 3 -> 8 then 10 -> 7 to finish the game.
    Or, we can move 3 -> 7, 4 -> 8, 5 -> 9 to finish the game.
    Notice we cannot move 10 -> 2 to finish the game, because that would be an illegal move.
    

**Example 3:**

    
    
    **Input:** [100,101,104,102,103]
    **Output:** [0,0]



 **Note:**

  1. `3 <= stones.length <= 10^4`
  2. `1 <= stones[i] <= 10^9`
  3. `stones[i]` have distinct values.

### Solution 1
## Lower Bound

As I mentioned in my [video](https://www.bilibili.com/video/av50812821) last
week,  
in case of `n` stones,  
we need to find a consecutive `n` positions and move the stones in.

This idea led the solution with sliding windows.

Slide a window of size `N`, and find how many stones are already in this
window.  
We want moves other stones into this window.  
For each missing stone, we need at least one move.

Generally, the number of missing stones and the moves we need are the same.  
Only one corner case in this problem, we need to move the endpoint to no
endpoint.

For case `1,2,4,5,10`,  
1 move needed from `10` to `3`.

For case `1,2,3,4,10`,  
2 move needed from `1` to `6`, then from `10` to `5`.  
  

## Upper Bound

We try to move all stones to leftmost or rightmost.  
For example of to rightmost.  
We move the `A[0]` to `A[1] + 1`.  
Then each time, we pick the stone of left endpoint, move it to the next empty
position.  
During this process, the position of leftmost stones increment 1 by 1 each
time.  
Until the leftmost is at `A[n - 1] - n + 1`.  
  

## Complexity

Time of quick sorting `O(NlogN)`  
Time of sliding window `O(N)`  
Space `O(1)`  
  

 **Java:**

    
    
         public int[] numMovesStonesII(int[] A) {
            Arrays.sort(A);
            int i = 0, n = A.length, low = n;
            int high = Math.max(A[n - 1] - n + 2 - A[1], A[n - 2] - A[0] - n + 2);
            for (int j = 0; j < n; ++j) {
                while (A[j] - A[i] >= n) ++i;
                if (j - i + 1 == n - 1 && A[j] - A[i] == n - 2)
                    low = Math.min(low, 2);
                else
                    low = Math.min(low, n - (j - i + 1));
            }
            return new int[] {low, high};
        }
    

**C++:**

    
    
        vector<int> numMovesStonesII(vector<int>& A) {
             sort(A.begin(), A.end());
            int i = 0, n = A.size(), low = n;
            int high = max(A[n - 1] - n + 2 - A[1], A[n - 2] - A[0] - n + 2);
            for (int j = 0; j < n; ++j) {
                while (A[j] - A[i] >= n) ++i;
                if (j - i + 1 == n - 1 && A[j] - A[i] == n - 2)
                    low = min(low, 2);
                else
                    low = min(low, n - (j - i + 1));
            }
            return {low, high};
        }
    

**Python:**

    
    
        def numMovesStonesII(self, A):
            A.sort()
            i, n, low =  0, len(A), len(A)
            high = max(A[-1] - n + 2 - A[1], A[-2] - A[0] - n + 2)
            for j in range(n):
                while A[j] - A[i] >= n: i += 1
                if j - i + 1 == n - 1 and A[j] - A[i] == n - 2:
                    low = min(low, 2)
                else:
                    low = min(low, n - (j - i + 1))
            return [low, high]
    


### Solution 2
首先对`stones`升序排序。

**求最大值** ：

每次可以移动最左端或者最右端，因此最大值一定是要么第一次移动的是最左端，要么第一次移动的是最右端；

如果第一次移动最右端，那么第一步一定是移到最右边开始第二个的前面，即`stones[n-2]`，`n`为`stones`的长度，并且最左的位置是`stones[0]`，  
因此移动距离就是`stones[n-2]-stones[0]-1-(n-3)`，也就是当前区间内部的位置，减去内部已经有石头的数量(有`3`个不在区间内部，分别是区间左右端2个和最后1个`stones[n-1]`)，剩下的空位置就是能移动石头的位置。

例如：`[1,3,5,7,12,65]`，`1`到`12`之间还剩下7个空位置，也就是先移动右端，能最多移动7次。

同样的，如果第一次移动最左端，那么第一步一定是移动到最左边开始第二个的后面，即`stones[1]`，它的最右端即是`stones[n-1]`，  
因此移动距离就是`stones[n-1]-stones[1]-1-(n-3)`；

例如：`[1,3,5,7,12,65]`，`3`到`65`之间还剩下58个空位置，也就是先移动左端，能最多移动58次。

上面两个值取最大值就是当前能移动的最大步数。

**求最小值** ：

`window
slide`，不断构造一个区间，这个区间`[i,j]`满足，区间的长度不会大于石头的数量，即这个区间是可以被区间外部的石头塞满的(可以塞不下，但不能有空隙)。

接着检查如果这个区间是 **连续** 的并且外部非连续的只有 **1** 个，那么这是一种特殊情况，返回`2`。

例如`[1,2,3,4,10]`，区间`[1,2,3,4]`是连续的，但外部非连续的只有1个，需要将`1`放到`6`的位置，再将`10`放到`5`的位置。

如果不是以上的特殊情况，那么不需要检查区间内部是否连续了，也就是将除了`[i,j]`区间以外的数字放入`[i,j]`区间，放不下的要让它们连续，只需要`n-(j-i+1)`步。

例如：

区间内部不连续：

`[1,4,7,9,20,30]`，当前`i`为0，`j`为1，那么将`[7,9,20,30]`与`[1,4]`连续只需要4步

  1. 将`30`放入`6`的位置，`[1,4,6,7,9,20]`；
  2. 将`20`放入`5`的位置，`[1,4,5,6,7,9]`；
  3. 将`9`放入`3`的位置，`[1,3,4,5,6,7]`；
  4. 将`7`放入`2`的位置，`[1,2,3,4,5,6]`，结束。

区间内部连续：

`[1,2,3,56,89]`，`i`为0，`j`为2，只需要2步

  1. 将`89`放入`5`的位置，`[1,2,3,5,56]`；
  2. 将`56`放入`4`的位置，`[1,2,3,4,5]`，结束。


### Solution 3
## the max move

 **intuition we need the max move** `A[n-1]-A[0]-n+1`

use the case `1 2 99 100`

![image](https://assets.leetcode.com/users/xiongqiangcs/image_1557397438.png)

 **but the endpoint stone exist an hole** , if you move the endpoint stone
firstly, the hole will disappear

use the case `1 100 102 105 200`

![image](https://assets.leetcode.com/users/xiongqiangcs/image_1557396468.png)

  * move the leftmost endpoint stone firstly, the left hole disappear, and the move is `A[n-1]-A[1]-n+2`  
![image](https://assets.leetcode.com/users/xiongqiangcs/image_1557397242.png)

  * samely，move the rightmost endpoint stone firstly, the right hole disappear, and the max move is `A[n-2]-A[0]-n+2`  
![image](https://assets.leetcode.com/users/xiongqiangcs/image_1557397698.png)

  * last, the max move is `min(A[n-1]-A[1]-n+2, A[n-2]-A[0]-n+2)`

### the min move

![image](https://assets.leetcode.com/users/xiongqiangcs/image_1557475637.png)

    
    
    vector<int> numMovesStonesII(vector<int>& A) {
    	sort(A.begin(), A.end());
    	int n = A.size(), low = n;
    	for (int i = 0, j = 0; j < n; ++ j) {
    		while (A[j]-A[i]+1 > n) ++ i;
    		int already_store = (j-i+1);
    		if (already_store == n - 1 &&  A[j] - A[i] + 1 == n-1) {
    			low = min(low, 2);
    		} else {
    			low = min(low, n - already_store);
    		}
    	}
    	return  {low, max(A[n-1]-A[1]-n+2, A[n-2]-A[0]-n+2)};
    }
    



