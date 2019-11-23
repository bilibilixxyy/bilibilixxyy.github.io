---
layout: post
title: 925. Construct Binary Tree from Preorder and Postorder Traversal
---
### Question
Return any binary tree that matches the given preorder and postorder
traversals.

Values in the traversals `pre` and `post` are distinct positive integers.



 **Example 1:**

    
    
     **Input:** pre = [1,2,4,5,3,6,7], post = [4,5,2,6,7,3,1]
    **Output:** [1,2,3,4,5,6,7]
    



 **Note:**

  * `1 <= pre.length == post.length <= 30`
  * `pre[]` and `post[]` are both permutations of `1, 2, ..., pre.length`.
  * It is guaranteed an answer exists. If there exists multiple answers, you can return any of them.

### Solution 1
I see a lot of solution saying `O(N)`, but actually not.  
If it takes already `O(N)` time to find left part and right right, it could
not be `O(N)`.  
If it is recursive solution, it should use a hashmap to reduce complexity,
otherwise in most cases it has at least average `O(NlogN)`.

Here I share my iterative solution.  
We will **preorder** generate TreeNodes, push them to `stack` and
**postorder** pop them out.

  1. Loop on `pre` array and construct node one by one.
  2. `stack` save the current path of tree.
  3. `node = new TreeNode(pre[i])`, if not left child, add node to the left. otherwise add it to the right.
  4. If we meet a same value in the pre and post, it means we complete the construction for current subtree. We pop it from `stack`.

 **Complexity** :  
O(N) Time O(N) Space

 **C++:**

    
    
        TreeNode* construct FromPrePost(vector<int> pre, vector<int> post) {
            vector<TreeNode*> s;
            s.push_back(new TreeNode(pre[0]));
            for (int i = 1, j = 0; i < pre.size(); ++i) {
                TreeNode* node = new TreeNode(pre[i]);
                while (s.back()->val == post[j])
                    s.pop_back(), j++;
                if (s.back()->left == NULL) s.back()->left = node;
                else s.back()->right = node;
                s.push_back(node);
            }
            return s[0];
        }
    

**Java:**

    
    
        public TreeNode construct FromPrePost(int[] pre, int[] post) {
            Deque<TreeNode> s = new ArrayDeque<>();
            s.offer(new TreeNode(pre[0]));
            for (int i = 1, j = 0; i < pre.length; ++i) {
                TreeNode node = new TreeNode(pre[i]);
                while (s.getLast().val == post[j]) {
                    s.pollLast(); j++;
                }
                if (s.getLast().left == null) s.getLast().left = node;
                else s.getLast().right = node;
                s.offer(node);
            }
            return s.getFirst();
        }
    

**Python:**

    
    
        def constructFromPrePost(self, pre, post):
            stack = [TreeNode(pre[ 0])]
            j = 0
            for v in pre[1:]:
                node = TreeNode(v)
                while stack[-1].val == post[j]:
                    stack.pop()
                    j += 1
                if not stack[-1].left:
                    stack[-1].left = node
                else:
                    stack[-1].right = node
                stack.append(node)
            return stack[0]
    


### Solution 2
The problem is easier to solve if we decrease it into subproblems using
**Divide and Conquer**.

    
    
    e.g.   Given preorder :  1 2 4 5 3 6;     postorder: 4 5 2 6 3 1.
    We see it as preorder : 1 (2 4 5) (3 6); postorder: (4 5 2) (6 3) 1 [to be explained afterwards]
    That can be decreased to subproblems A, B, C: 
    A. preorder : 1; postorder: 1 =>
     1
    B. preorder : (2 4 5); postorder: (4 5 2) => 
       2
      / \
     4   5
    C. preorder : (3 6); postorder: (6 3) => 
       3
      / 
     6     or
       3
        \
         6
    * Then we conquer the subproblems => A.left = B; A.right = C;
       1
      / \
     2   3
    / \  /
    4  5 6
    

If we observe parameters in each recursion above:

    
    
    preStart: 0, preEnd: 5, postStart: 0, postEnd: 5
    preStart: 1, preEnd: 3, postStart: 0, postEnd: 2
    preStart: 4, preEnd: 5, postStart: 3, postEnd: 4
    

For the commented, `[to be explained afterwards]`, how do we decrease a
problem?  
That is, 1 is root and 2 is its left child. Since 2 is the root of the left
subtree, all elements in front of 2 in `post[]` must be in the left subtree
also.  
We recursively follow the above approach.  
Please note that `pre[preStart + 1] may also be the root of the right subtree`
if there is no left subtree at all in the orginal tree. Since we are asked to
generate one possible original tree, I assume `pre[preStart + 1]` to be the
left subtree root always.

* * *

**Java**

    
    
         public TreeNode constructFromPrePost(int[] pre, int[] post) {
            return constructFromPrePost(pre, 0, pre.length - 1, post, 0, pre.length - 1);
        }
        
        private TreeNode constructFromPrePost(int[] pre, int preStart, int preEnd, int[] post, int postStart, int postEnd) {
            // Base cases.
            if (preStart > preEnd) {
                return null;
            }
            if (preStart == preEnd) {
                return new TreeNode(pre[preStart]);
            }
            
            // Build root.
            TreeNode root = new TreeNode(pre[preStart]);
            
            // Locate left subtree.
            int leftSubRootInPre = preStart + 1; 
            int leftSubRootInPost = findLeftSubRootInPost(pre[leftSubRootInPre], post, postStart, postEnd);
            int leftSubEndInPre = leftSubRootInPre + (leftSubRootInPost - postStart);
            
            // Divide.
            TreeNode leftSubRoot = constructFromPrePost(pre, leftSubRootInPre, leftSubEndInPre, 
                                                        post, postStart, leftSubRootInPost);  
            TreeNode rightSubRoot = constructFromPrePost(pre, leftSubEndInPre + 1, preEnd, 
                                                         post, leftSubRootInPost + 1, postEnd - 1);
            
            // Conquer.      
            root.left = leftSubRoot;
            root.right = rightSubRoot;
            
            return root;
        }
        
        private int findLeftSubRootInPost(int leftSubRootVal, int[] post, int postStart, int postEnd) {
            for (int i = postStart; i <= postEnd; i++) {
                if (post[i] == leftSubRootVal) {
                    return i;
                }
            }
            
            throw new IllegalArgumentException();
        }
    
    

**Python**

    
    
         def constructFromPrePost(self, pre, post):
            return self.constructFromPrePostRecurUtil(
                pre, 0, len(pre) - 1, post, 0, len(post) - 1)
            
        def constructFromPrePostRecurUtil(
                self, 
                pre, 
                preStart, 
                preEnd, 
                post, 
                postStart, 
                postEnd):
            # Base case.
            if (preStart > preEnd):
                return None
            if (preStart == preEnd):
                return TreeNode(pre[preStart])
            # Recursive case.
            root = TreeNode(pre[preStart])
            leftRootIndexInPre = preStart + 1
            leftRootIndexInPost = self.getIndexInPost(
                post, pre[leftRootIndexInPre])
            leftEndIndexInPre = leftRootIndexInPre + \
                (leftRootIndexInPost - postStart)
            root.left = self.constructFromPrePostRecurUtil(
                pre, 
                leftRootIndexInPre, 
                leftEndIndexInPre, 
                post, 
                postStart, 
                leftRootIndexInPost)
            root.right = self.constructFromPrePostRecurUtil(
                pre, 
                leftEndIndexInPre + 1, 
                preEnd, 
                post, 
                leftRootIndexInPost + 1, 
                postEnd - 1)
            return root
            
        def getIndexInPost(self, post, target):
            for i, v in enumerate(post):
                if v == target:
                    return i
            return -1   # to optimize
    

**(人 •͈ᴗ•͈)** Thanks for voting!


### Solution 3
For two subarrays `pre[a,b]` and `post[c,d]`, if we want to reconstruct a tree
from them, we know that pre[a]==post[d] is the root node.

    
    
    [root][......left......][...right..]  ---pre
    [......left......][...right..][root]  ---post
    

`pre[a+1]` is the root node of the left subtree.  
Find the index of `pre[a+1]` in `post`, then we know the left subtree should
be constructed from `pre[a+1, a+idx-c+1]` and `post[c, idx]`.

Here is my code:

    
    
    class Solution {
    public:
        unordered_map<int, int> m; // value->index
        TreeNode* constructFromPrePost(vector<int>& pre, vector<int>& post) {
            int len = post.size();
            for (int i = 0; i < len; i++) m[post[i]] = i;
            return construct(pre, post, 0, len - 1, 0, len - 1);
        }
        
        TreeNode* construct(vector<int>& pre, vector<int>& post, int a, int b, int c, int d) {
            TreeNode* n = new TreeNode(pre[a]);
            if (a == b) return n;
            int t = pre[a + 1];
            int idx = m[t];
            int len = idx - c + 1;
            n->left = construct(pre, post, a + 1, a + len, c, c + len - 1);
            if (idx + 1 == d) return n;
            n->right = construct(pre, post, a + len + 1, b, idx + 1, d - 1);
            return n;
        }
    };
    



