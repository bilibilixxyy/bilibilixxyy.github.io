---
layout: post
title: 70. Climbing Stairs
---
### Question
You are climbing a stair case. It takes _n_ steps to reach to the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you
climb to the top?

 **Note:** Given _n_ will be a positive integer.

 **Example 1:**

    
    
     **Input:** 2
    **Output:** 2
    **Explanation:** There are two ways to climb to the top.
    1. 1 step + 1 step
    2. 2 steps
    

**Example 2:**

    
    
    **Input:** 3
    **Output:** 3
    **Explanation:** There are three ways to climb to the top.
    1. 1 step + 1 step + 1 step
    2. 1 step + 2 steps
    3. 2 steps + 1 step
    

### Solution 1
The problem seems to be a _dynamic programming_ one. **Hint** : the tag also
suggests that!  
Here are the steps to get the solution incrementally.

  * Base cases:  
if n <= 0, then the number of ways should be zero.  
if n == 1, then there is only way to climb the stair.  
if n == 2, then there are two ways to climb the stairs. One solution is one
step by another; the other one is two steps at one time.

  * The key intuition to solve the problem is that given a number of stairs n, if we know the number ways to get to the points `[n-1]` and `[n-2]` respectively, denoted as `n1` and `n2` , then the total ways to get to the point `[n]` is `n1 + n2`. Because from the `[n-1]` point, we can take one single step to reach `[n]`. And from the `[n-2]` point, we could take two steps to get there.

  * The solutions calculated by the above approach are _**complete**_ and _**non-redundant**_. The two solution sets (`n1` and `n2`) cover all the possible cases on how the final step is taken. And there would be NO overlapping among the final solutions constructed from these two solution sets, because they differ in the final step.

Now given the above intuition, one can construct an array where each node
stores the solution for each number n. Or if we look at it closer, it is clear
that this is basically a fibonacci number, with the starting numbers as 1 and
2, instead of 1 and 1.

The implementation in Java as follows:

    
    
    public int climbStairs(int n) {
        // base cases
        if(n <= 0) return 0;
        if(n == 1) return 1;
        if(n == 2) return 2;
        
        int one_step_before = 2;
        int two_steps_before = 1;
        int all_ways = 0;
        
        for(int i=2; i<n; i++){
        	all_ways = one_step_before + two_steps_before;
        	two_steps_before = one_step_before;
            one_step_before = all_ways;
        }
        return all_ways;
    }


### Solution 2
Same simple algorithm written in every offered language. Variable `a` tells
you the number of ways to reach the current step, and `b` tells you the number
of ways to reach the next step. So for the situation one step further up, the
old `b` becomes the new `a`, and the new `b` is the old `a+b`, since that new
step can be reached by climbing 1 step from what `b` represented or 2 steps
from what `a` represented.

Ruby wins, and _"the C languages"_ all look the same.

 **Ruby** (60 ms)

    
    
     def climb_stairs(n)
        a = b = 1
        n.times { a, b = b, a+b }
        a
    end
    

**C++** (0 ms)

    
    
     int climbStairs(int n) {
        int a = 1, b = 1;
        while (n--)
            a = (b += a) - a;
        return a;
    }
    

**Java** (208 ms)

    
    
     public int climbStairs(int n) {
        int a = 1, b = 1;
        while (n-- > 0)
            a = (b += a) - a;
        return a;
    }
    

**Python** (52 ms)

    
    
     def climbStairs(self, n):
        a = b = 1
        for _ in range(n):
            a, b = b, a + b
        return a
    

**C** (0 ms)

    
    
     int climbStairs(int n) {
        int a = 1, b = 1;
        while (n--)
            a = (b += a) - a;
        return a;
    }
    

**C#** (48 ms)

    
    
     public int ClimbStairs(int n) {
        int a = 1, b = 1;
        while (n-- > 0)
            a = (b += a) - a;
        return a;
    }
    

**Javascript** (116 ms)

    
    
     var climbStairs = function(n) {
        a = b = 1
        while (n--)
            a = (b += a) - a
        return a
    };


### Solution 3
## Leetcode

### 70 Climbing Stairs

    
    
    > 类型：DP基础题
    > Time Complexity O(N)
    > Space Complexity O(1)
    

这道题作为DP的启蒙题(拥有非常明显的重叠子结构)，我在这详细的讲一讲LC大神们的答案是如何从一个毫无优化的做法，效率极低的递归解法，慢慢的进化到他们现在的答案，也给不会DP思路的同学补一补基础。

  

## Top-Down

这道题自顶向下的思考：如果要爬到`n`台阶，有两种可能性:

  1. 在`n-1`的台阶处爬一层台阶
  2. 在`n-2`的台阶处爬两层台阶

继续向下延伸思考，`到达每一次层一共有几种方法`这个问题就变成了2个子问题：

  1. 到达`n-1`层台阶有几种方法
  2. 到达`n-2`层台阶有几种方法

之后对返回子问题之和即可。

具体可以看下图。

![](https://raw.githubusercontent.com/yuzhoujr/spazzatura/master/img_box/78.jpeg)

根据以上的思路得到下面的代码

#### Solution 1: Recursion (TLE)

    
    
    class Solution(object):
        def climbStairs(self, n):
            if n == 1: return 1
            if n == 2: return 2
            return self.climbStairs(n - 1) + self.climbStairs(n - 2)
    

##### TLE原因：

以上代码实现之所以会TLE，是因为递归的时候出现了很多次重复的运算。就如上图显示的爬`n-2`层的计算出现了2次，这种重复计算随着`input`的增大，会出现的越来越多，时间复杂度也会将以指数的级别上升。

##### 优化思路：

这时候的思路为：如果能够将之前的计算好了的结果存起来，之后如果遇到重复计算直接调用结果，效率将会从之前的`指数时间复杂度`，变成`O(N)`的时间复杂度。

##### 实现方法：

有了思路，实现方面则开辟一个长度为N的数组，将其中的值全部赋值成`-1`，具体为什么要用`-1`，是因为这一类问题一般都会要你求多少种可能，在现实生活中，基本不会要你去求负数种可能，所以这里`-1`可以成为一个很好的递归条件/出口。  
然后只要我们的数组任然满足`arr[n] ==
-1`，说明我们在`n`层的数没有被更新，换句话说就是我们还在向下递归或者等待返回值的过程中，所以我们继续递归直到`n-1`和`n-2`的值返回上来。这里注意我们递归的底层，也就是arr[0]和arr[1]，我们要对起始条件进行初始化：`arr[0],
arr[1] = 1, 2`

#### Solution 2: Top-Down (using array)

    
    
    class Solution(object):
        def climbStairs(self, n):
            if n == 1: return 1
            res = [-1 for i in range(n)]
            res[0], res[1] = 1, 2
            return self.dp(n-1, res)
            
        def dp(self, n, res):
            if res[n] == -1:
                res[n] = self.dp(n - 1, res) + self.dp(n - 2, res)
            return res[n]
        
    

这样是不是还是很抽象？我们可以举个例子，当`n = 4`的时候，我们在每一层返回之前打印一下当前的数组的值：

    
    
    # print(n+1, res)
    (2, [1, 2, -1, -1])  
    (1, [1, 2, -1, -1])  
    (3, [1, 2, 3, -1])  
    (2, [1, 2, 3, -1])  
    (4, [1, 2, 3, 5])
    

大家看到了，我们先从第`4`层开始递归，当递归到了`1`和`2`层的base case的时候，开始进行返回的计算，  
当到了第`3`层，将`1`和`2`层加起来，得到`3`，继续返回  
当到了第`4`层，将`2`和`3`层加起来，得到了`5`，这时候res[n] = 5，则满足出口条件，进行返回。

这就是Top-Down的思路，从大化小，思路就和DFS Tree中的分制一样。

  

## Bottom-Up

Bottom-
Up的思路则相反。我们不从`n`层向下考虑，而是解决我们每一层向上的子问题。在每一层，我们其实只需要知道在当前节点，我们的`n-1`和`n-2`的值是多少即可。

当我们有了第1层和第2层的base case，我们则可以直接从base case向上推得第3层，第4层以及第n层的答案，具体实现如下：

#### Solution 3: Bottom-Up (using array)

    
    
    class Solution(object):
        def climbStairs(self, n):
            if n == 1: return 1
            res = [0 for i in range(n)]
            res[0], res[1] = 1, 2
            for i in range(2, n):
                res[i] = res[i-1] + res[i-2]
            return res[-1]
    

这种方法的使用的时候，我们发现其实在每一次更新当前的数的时候，我们最终返回的是最后一次更新的数，而我们的`dependency`是`n-1`
和`n-2`中的值，我们根本不需要一个数组去储存，直接用两个函数即可。所以底下为Bottom-Up的优化：

#### Solution 3: Bottom-Up (Constant Space)

    
    
    class Solution(object):
        def climbStairs(self, n):
            if n == 1: return 1
            a, b = 1, 2
            for _ in range(2, n):
                a, b = b, a + b
            return b
    

  

公瑾在刷题，加入我? : <https://github.com/yuzhoujr/leetcode/projects/1>

![](https://raw.githubusercontent.com/yuzhoujr/spazzatura/master/img_box/vote.jpeg)



