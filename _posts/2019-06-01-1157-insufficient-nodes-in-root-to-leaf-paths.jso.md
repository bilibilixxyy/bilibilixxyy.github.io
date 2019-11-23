---
layout: post
title: 1157. Insufficient Nodes in Root to Leaf Paths
---
### Question
Given the `root` of a binary tree, consider all _root to leaf paths_ : paths
from the root to any leaf.  (A leaf is a node with no children.)

A `node` is _insufficient_ if   **every** such root to leaf path intersecting
this `node` has sum strictly less than `limit`.

Delete all insufficient nodes simultaneously, and return the root of the
resulting binary tree.



 **Example 1:**

    
    
     **![](https://assets.leetcode.com/uploads/2019/06/05/insufficient-11.png)Input:** root = [1,2,3,4,-99,-99,7,8,9,-99,-99,12,13,-99,14], limit = 1
    **![](https://assets.leetcode.com/uploads/2019/06/05/insufficient-2.png)Output:** [1,2,3,4,null,null,7,8,9,null,14]
    

**Example 2:**

    
    
    **![](https://assets.leetcode.com/uploads/2019/06/05/insufficient-3.png)Input:** root = [5,4,8,11,null,17,4,7,1,null,null,5,3], limit = 22
    **![](https://assets.leetcode.com/uploads/2019/06/05/insufficient-4.png)Output:** [5,4,8,11,null,17,4,7,null,null,null,5]



**Example 3:**

    
    
    **![](https://assets.leetcode.com/uploads/2019/06/11/screen-shot-2019-06-11-at-83301-pm.png)Input:** root = [1,2,-3,-5,null,4,null], limit = -1
    ![](https://assets.leetcode.com/uploads/2019/06/11/screen-shot-2019-06-11-at-83517-pm.png)**Output:** [1,null,-3,4]



 **Note:**

  1. The given tree will have between `1` and `5000` nodes.
  2. `-10^5 <= node.val <= 10^5`
  3. `-10^9 <= limit <= 10^9`

### Solution 1
Hello everyone,

~~We have decided to rejudge this problem (140th LeetCode Weekly Contest's Q3
- Insufficient Nodes in Root to Leaf Paths) because of judging error.~~

~~Only the submissions which got "Wrong Answer" will be rejudged. All the
submissions which got "AC" during contest, would be kept as such(even if they
are incorrect).~~

~~Contest ranking will be updated as soon as rejudging finishes!~~

Edit: Even though this contest's Q3 was rejudged and ranking was updated,
after careful consideration, we decided to declare "Weekly Contest 140" as
unrated which means it won't have any effect on the global ranking.

We apologize for the quality of some problems in the last few contests. While
all our contest problems undergo a review and get tested by several people, a
few issues have slipped past our review process. To prevent such mistakes from
happening in the future, we are working on improving our problem review
process.


### Solution 2
-> A leaf is a node with at most one children. (in this problem)


### Solution 3
With the following input, there are only 2 leaf node{-1, 2}, and only 2 paths
from root to leaf. {1} is not a leaf node.  
The description of the problem already highlighted the definition of leaf
node: (A leaf is a node with no children)  
![image](https://assets.leetcode.com/users/pjincz/image_1560053978.png)

So there are only 2 paths from root to leaf.  
The first path is [0, 1, -1], and second is [0, 2].  
If give a limit = 1, there are 2 nodes have to be removed.  
{-1} need to be removed.  
{1} is also needed to be removed, because there's only one path over the node
{1}.

Please obsolete the contest's result. It's totally meanless.



