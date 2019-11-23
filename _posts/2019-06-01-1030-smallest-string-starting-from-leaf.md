---
layout: post
title: 1030. Smallest String Starting From Leaf
---
### Question
Given the `root` of a binary tree, each node has a value from `0` to `25`
representing the letters `'a'` to `'z'`: a value of `0` represents `'a'`, a
value of `1` represents `'b'`, and so on.

Find the lexicographically smallest string that starts at a leaf of this tree
and ends at the root.

 _(As a reminder, any shorter prefix of a string is lexicographically smaller:
for example,`"ab"` is lexicographically smaller than `"aba"`.  A leaf of a
node is a node that has no children.)_



 **Example 1:**

 **![](https://assets.leetcode.com/uploads/2019/01/30/tree1.png)**

    
    
     **Input:** [0,1,2,3,4,3,4]
    **Output:** "dba"
    

**Example 2:**

**![](https://assets.leetcode.com/uploads/2019/01/30/tree2.png)**

    
    
    **Input:** [25,1,3,1,3,0,2]
    **Output:** "adz"
    

**Example 3:**

**![](https://assets.leetcode.com/uploads/2019/02/01/tree3.png)**

    
    
    **Input:** [2,2,1,null,1,0,null,0]
    **Output:** "abc"
    



 **Note:**

  1. The number of nodes in the given tree will be between `1` and `8500`.
  2. Each node in the tree will have a value between `0` and `25`.

### Solution 1
Traverse the tree, building up the string in the reverse order. Return the
value when we find a leaf; track the smalest string of left and right
subtrees.

The trick here is to ignore non-leaf nodes with one child. To do that, we can
return "|", which is larger than 'z'.

Also thanks [@Ninja_fz](https://leetcode.com/Ninja_fz) for simplifying my
original solution!

    
    
    string smallestFromLeaf(TreeNode* r) {
      if (r == nullptr) return "|";
      auto s = string(1, 'a' + r->val);
      return r->left == r->right ? s : min(smallestFromLeaf(r->left) + s, smallestFromLeaf(r->right) + s);
    }
    

As [@jamesPB](https://leetcode.com/jamespb/) noted below, the bottom-up
approach is accepted but it does not work for case like this:

    
    
    [25, 1, null, 0, 0, 1, null, null, null, 0]
    

So, it looks like we do need to use a top-down approach instead:

    
    
    string smallestFromLeaf(TreeNode* r, string s = "") {
      if (r == nullptr) return "|";
      s = string(1, 'a' + r->val) + s;
      return r->left == r->right ? s : min(smallestFromLeaf(r->left, s), smallestFromLeaf(r->right, s));
    }
    


### Solution 2
Many people have solved this problem with the basic idea of divide and
conquer, where the key strategy is something like this:

    
    
    answer(root) = min(answer(left) + root.val, answer(right) + root.val)
    

which is incorrect. Surprisingly, these solutions got accepted.  
Let me explain this by this case:  
[25, 1, null, 0, 0, 1, null, null, null, 0].  
![image](https://assets.leetcode.com/users/jamespb/image_1551131779.png)

The expected answer is "ababz", while by divide-and-conqure we would get
"abz".  
The problem here is

    
    
    string X < string Y
    

doesn't guarantee

    
    
    X + a < Y + a
    

where a is a character. e.g.:

    
    
    "ab" < "abab", but "abz" > "ababz"
    

So maybe the only correct way is a full DFS with backtracking.  
(Still working on my solution, perhaps post later.)


### Solution 3
update on 2019/03/10 with <http://www.noteanddata.com/leetcode-988-Smallest-
String-Starting-From-Leaf-java-solution-update.html> thanks for @ayyildiz
pointing out my mistakes

below is the new version:

    
    
    public String smallestFromLeaf(TreeNode root) {
        return dfs(root, "");
    }
    
    public String dfs(TreeNode node, String suffix) {
        if(null == node) {
            return suffix;
        }
        suffix = "" + (char)('a' + node.val) + suffix;
        if(null == node.left && null == node.right) {
            return suffix;
        }
        if(null == node.left || null == node.right) {
            return (null == node.left)? dfs(node.right, suffix) :dfs(node.left, suffix);
        }
        
        String left = dfs(node.left, suffix);
        String right = dfs(node.right, suffix);
        
        return left.compareTo(right) <= 0? left: right;
    }
    

**previous version can be accepted but is wrong**  
<http://www.noteanddata.com/leetcode-988-Smallest-String-Starting-From-Leaf-
java-solution-note.html>

    
    
     public String smallestFromLeaf(TreeNode root) {
        return dfs(root);
    }
    
    public String dfs(TreeNode node) {
        if(null == node) {
            return null;
        }
        else {
            char ch = (char)('a' + node.val);
            String left = dfs(node.left);
            String right = dfs(node.right);
            if(left == null && right == null) { // leaf
                return "" + ch;
            }
            else if(left == null || right == null) {
                return left != null ? (left + ch) : (right+ch);
            }
            else { // both are not null
                if(left.compareTo(right) < 0) {
                    return left + ch;
                }
                else {
                    return right + ch;
                }
            }
        }
    }
    



