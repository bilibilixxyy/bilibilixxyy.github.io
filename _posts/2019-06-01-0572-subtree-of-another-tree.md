---
layout: post
title: 572. Subtree of Another Tree
---
### Question
Given two non-empty binary trees **s** and **t** , check whether tree **t**
has exactly the same structure and node values with a subtree of **s**. A
subtree of **s** is a tree consists of a node in **s** and all of this node's
descendants. The tree **s** could also be considered as a subtree of itself.

 **Example 1:**  
Given tree s:

    
    
         3
        / \
       4   5
      / \
     1   2
    

Given tree t:

    
    
       4 
      / \
     1   2
    

Return **true** , because t has the same structure and node values with a
subtree of s.

**Example 2:**  
Given tree s:

    
    
         3
        / \
       4   5
      / \
     1   2
        /
       0
    

Given tree t:

    
    
       4
      / \
     1   2
    

Return **false**.

### Solution 1
For each node during pre-order traversal of `s`, use a recursive function
`isSame` to validate if sub-tree started with this node is the same with `t`.

    
    
    public class Solution {
        public boolean isSubtree(TreeNode s, TreeNode t) {
            if (s == null) return false;
            if (isSame(s, t)) return true;
            return isSubtree(s.left, t) || isSubtree(s.right, t);
        }
        
        private boolean isSame(TreeNode s, TreeNode t) {
            if (s == null && t == null) return true;
            if (s == null || t == null) return false;
            
            if (s.val != t.val) return false;
            
            return isSame(s.left, t.left) && isSame(s.right, t.right);
        }
    }
    


### Solution 2
**Naive approach, O(|s| * |t|)**  
For each node of `s`, let's check if it's subtree equals `t`. We can do that
in a straightforward way by an `isMatch` function: check if `s` and `t` match
at the values of their roots, plus their subtrees match. Then, in our main
function, we want to check if `s` and `t` match, or if `t` is a subtree of a
child of `s`.

    
    
    def isMatch(self, s, t):
        if not(s and t):
            return s is t
        return (s.val == t.val and 
                self.isMatch(s.left, t.left) and 
                self.isMatch(s.right, t.right))
    
    def isSubtree(self, s, t):
        if self.isMatch(s, t): return True
        if not s: return False
        return self.isSubtree(s.left, t) or self.isSubtree(s.right, t)
    

**Advanced approach, O(|s| + |t|) (Merkle hashing):**  
For each node in a tree, we can create `node.merkle`, a hash representing it's
subtree.  
This hash is formed by hashing the concatenation of the merkle of the left
child, the node's value, and the merkle of the right child. Then, two trees
are identical if and only if the merkle hash of their roots are equal (except
when there is a hash collision.) From there, finding the answer is
straightforward: we simply check if any `node` in `s` has `node.merkle ==
t.merkle`

    
    
    def isSubtree(self, s, t):
        from hashlib import sha256
        def hash_(x):
            S = sha256()
            S.update(x)
            return S.hexdigest()
            
        def merkle( node):
            if not node:
                return '#'
            m_left = merkle(node.left)
            m_right = merkle(node.right)
            node.merkle = hash_(m_left + str(node.val) + m_right)
            return node.merkle
            
        merkle(s)
        merkle(t)
        def dfs(node):
            if not node:
                return False
            return (node.merkle == t.merkle or 
                    dfs(node.left) or dfs(node.right))
                        
        return dfs(s)
    


### Solution 3
    
    
    public class Solution {
     public boolean isSubtree(TreeNode s, TreeNode t) {
            String spreorder = generatepreorderString(s); 
            String tpreorder = generatepreorderString(t);
            
            return spreorder.contains(tpreorder) ;
        }
        public String generatepreorderString(TreeNode s){
            StringBuilder sb = new StringBuilder();
            Stack<TreeNode> stacktree = new Stack();
            stacktree.push(s);
            while(!stacktree.isEmpty()){
               TreeNode popelem = stacktree.pop();
               if(popelem==null)
                  sb.append(",#"); // Appending # inorder to handle same values but not subtree cases
               else      
                  sb.append(","+popelem.val);
               if(popelem!=null){
                    stacktree.push(popelem.right);    
                    stacktree.push(popelem.left);  
               }
            }
            return sb.toString();
        }
    }
    



