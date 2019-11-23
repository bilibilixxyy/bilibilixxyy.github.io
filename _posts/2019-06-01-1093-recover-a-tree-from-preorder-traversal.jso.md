---
layout: post
title: 1093. Recover a Tree From Preorder Traversal
---
### Question
We run a preorder depth first search on the `root` of a binary tree.

At each node in this traversal, we output `D` dashes (where `D` is the _depth_
of this node), then we output the value of this node.    _(If the depth of a
node is`D`, the depth of its immediate child is `D+1`.  The depth of the root
node is `0`.)_

If a node has only one child, that child is guaranteed to be the left child.

Given the output `S` of this traversal, recover the tree and return its
`root`.



 **Example 1:**

 **![](https://assets.leetcode.com/uploads/2019/04/08/recover-a-tree-from-
preorder-traversal.png)**

    
    
     **Input:** "1-2--3--4-5--6--7"
    **Output:** [1,2,5,3,4,6,7]
    

**Example 2:**

**![](https://assets.leetcode.com/uploads/2019/04/11/screen-
shot-2019-04-10-at-114101-pm.png)**

    
    
    **Input:** "1-2--3---4-5--6---7"
    **Output:** [1,2,5,3,null,6,null,4,null,7]



**Example 3:**

![](https://assets.leetcode.com/uploads/2019/04/11/screen-
shot-2019-04-10-at-114955-pm.png)

    
    
    **Input:** "1-401--349---90--88"
    **Output:** [1,401,null,349,88,90]
    



 **Note:**

  * The number of nodes in the original tree is between `1` and `1000`.
  * Each node will have a value between `1` and `10^9`.

### Solution 1
##  **Explanation**

We save the construction path in a `stack`.  
In each loop,  
we get the number `level` of `'-'`  
we get the value `val` of `node` to add.

If the size of stack is bigger than the level of node,  
we pop the stack until it's not.

Finally we return the first element in the stack, as it's root of our tree.

##  **Complexity**

Time `O(S)`, Space `O(N)`

 **Java**

    
    
        public TreeNode recover FromPreorder(String S) {
            int level, val;
            Stack<TreeNode> stack = new Stack<>();
            for (int i = 0; i < S.length();) {
                for (level = 0; S.charAt(i) == '-'; i++) {
                    level++;
                }
                for (val = 0; i < S.length() && S.charAt(i) != '-'; i++) {
                    val = val * 10 + (S.charAt(i) - '0');
                }
                while (stack.size() > level) {
                    stack.pop();
                }
                TreeNode node = new TreeNode(val);
                if (!stack.isEmpty()) {
                    if (stack.peek().left == null) {
                        stack.peek().left = node;
                    } else {
                        stack.peek().right = node;
                    }
                }
                stack.add(node);
            }
            while (stack.size() > 1) {
                stack.pop();
            }
            return stack.pop();
        }
    

**C++**

    
    
         TreeNode* recoverFromPreorder(string S) {
            vector<TreeNode*> stack;
            for (int i = 0, level, val; i < S.length();) {
                for (level = 0; S[i] == '-'; i++)
                    level++;
                for (val = 0; i < S.length() && S[i] != '-'; i++)
                    val = val * 10 + S[i] - '0';
                TreeNode* node = new TreeNode(val);
                while (stack.size() > level) stack.pop_back();
                if (!stack.empty())
                    if (!stack.back()->left) stack.back()->left = node;
                    else stack.back()->right = node;
                stack.push_back(node);
            }
            return stack[0];
        }
    

**Python:**

    
    
        def recoverFromPreorder(self, S):
            stack, i = [],  0
            while i < len(S):
                level, val = 0, ""
                while i < len(S) and S[i] == '-':
                    level, i = level + 1, i + 1
                while i < len(S) and S[i] != '-':
                    val, i = val + S[i], i + 1
                while len(stack) > level:
                    stack.pop()
                node = TreeNode(val)
                if stack and stack[-1].left is None:
                    stack[-1].left = node
                elif stack:
                    stack[-1].right = node
                stack.append(node)
            return stack[0]
    


### Solution 2
<https://www.youtube.com/watch?v=ZYu2-7ovvw8>

Wasted 4 mins debugging the last problem, and turns out I misread the test
case...  
otherwise, I would have won this contest. sad...


### Solution 3
    
    
    class Solution {
        int index = 0;
        public TreeNode recoverFromPreorder(String S) {
            return helper(S, 0);
        }
        
        public TreeNode helper(String s, int depth) {
            int numDash = 0;
            while (index + numDash < s.length() && s.charAt(index + numDash) == '-') {
                numDash++;
            }
            if (numDash != depth) return null;
            int next = index + numDash;
            while (next < s.length() && s.charAt(next) != '-') next++;
            int val = Integer.parseInt(s.substring(index + numDash, next));
            index = next;
            TreeNode root = new TreeNode(val);
            root.left = helper(s, depth + 1);
            root.right = helper(s, depth + 1);
            return root;
        }
    }
    



