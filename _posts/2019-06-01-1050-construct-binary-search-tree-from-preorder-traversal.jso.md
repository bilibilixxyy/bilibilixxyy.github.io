---
layout: post
title: 1050. Construct Binary Search Tree from Preorder Traversal
---
### Question
Return the root node of a binary **search** tree that matches the given
`preorder` traversal.

 _(Recall that a binary search tree  is a binary tree where for every node,
any descendant of `node.left` has a value `<` `node.val`, and any descendant
of `node.right` has a value `>` `node.val`.  Also recall that a preorder
traversal displays the value of the `node` first, then traverses `node.left`,
then traverses `node.right`.)_



 **Example 1:**

    
    
     **Input:** [8,5,1,7,10,12]
    **Output:** [8,5,10,1,7,null,12]
    ![](https://assets.leetcode.com/uploads/2019/03/06/1266.png)
    



 **Note:**  

  1. `1 <= preorder.length <= 100`
  2. The values of `preorder` are distinct.

### Solution 1
# **Intuition** :

Find the left part and right part,  
then recursively construct the tree.  
  

#  **Solution 1** :

Binary search

 **Python,`O(N^2)`**

    
    
         def bstFromPreorder(self, A):
            if not A: return None
            root = TreeNode(A[0])
            i = bisect.bisect(A, A[0])
            root.left = self.bstFromPreorder(A[1:i])
            root.right = self.bstFromPreorder(A[i:])
            return root
    

**Python,`O(NlogN)`**

    
    
        def bstFromPreorder(self, A):
            def helper( i, j):
                if i == j: return None
                root = TreeNode(A[i])
                mid = bisect.bisect(A, A[i], i + 1, j)
                root.left = helper(i + 1, mid)
                root.right = helper(mid, j)
                return root
            return helper(0, len(A))
    

  

# **Solution 2**

Give the function a bound the maximum number it will handle.  
The left recursion will take the elements smaller than `node.val`  
The right recursion will take the remaining elements smaller than `bound`

**Complexity**  
`bstFromPreorder` is called exactly N times.  
It's same as a preorder traversal.  
Time: `O(N)`

**Java**

    
    
         int i = 0;
        public TreeNode bstFromPreorder(int[] A) {
            return bstFromPreorder(A, Integer.MAX_VALUE);
        }
    
        public TreeNode bstFromPreorder(int[] A, int bound) {
            if (i == A.length || A[i] > bound) return null;
            TreeNode root = new TreeNode(A[i++]);
            root.left = bstFromPreorder(A, root.val);
            root.right = bstFromPreorder(A, bound);
            return root;
        }
    

**C++**

    
    
         int i = 0;
        TreeNode* bstFromPreorder(vector<int>& A, int bound = INT_MAX) {
            if (i == A.size() || A[i] > bound) return NULL;
            TreeNode* root = new TreeNode(A[i++]);
            root->left = bstFromPreorder(A, root->val);
            root->right = bstFromPreorder(A, bound);
            return root;
        }
    

**Python**

    
    
        i =  0
        def bstFromPreorder(self, A, bound=float('inf')):
            if self.i == len(A) or A[self.i] > bound:
                return None
            root = TreeNode(A[self.i])
            self.i += 1
            root.left = self.bstFromPreorder(A, root.val)
            root.right = self.bstFromPreorder(A, bound)
            return root
    


### Solution 2
  * Idea is simple.
  * First item in preorder list is the root to be considered.
  * For next item in preorder list, there are 2 cases to consider:
    * If value is less than last item in stack, it is the left child of last item.
    * If value is greater than last item in stack, pop it.
      * The last popped item will be the parent and the item will be the right child of the parent.

    
    
    class Solution:
        def bstFromPreorder(self, preorder: List[int]) -> TreeNode:
            root = TreeNode(preorder[0])
            stack = [root]
            for value in preorder[1:]:
                if value < stack[-1].val:
                    stack[-1].left = TreeNode(value)
                    stack.append(stack[-1].left)
                else:
                    while stack and stack[-1].val < value:
                        last = stack.pop()
                    last.right = TreeNode(value)
                    stack.append(last.right)
            return root
    


### Solution 3
The first element `p[0]` in the array corresponds to the root. Then, we find
the split point `i` such as `p[i] >= p[0]`. Subarray `[1, i - 1]` corresponds
to the left subtree, `[i, n - 1]` \- to the right one.

    
    
    TreeNode* bstFromPreorder(vector<int> &p, int first = 0, int last = 0) {
        if (last == 0) last = p.size();
        if (first == last) return nullptr;
        auto split = find_if(begin(p) + first, begin(p) + last, [&](int val) { return val > p[first]; });
        auto root = new TreeNode(p[first]);
        root->left = bstFromPreorder(p, first + 1, split - begin(p));
        root->right = bstFromPreorder(p, split - begin(p), last);
        return root;
    }
    

## Complexity Analysis

Runtime: _O(n * n)_ worst case, _O(n log n)_ average case. For each node ( _n_
), we search for the split point ( _log n_ average, _n_ worst case).  
Memory: _O(n)_ worst case, _O(h)_ average case for the stack, where _h_ is the
height of the tree.



