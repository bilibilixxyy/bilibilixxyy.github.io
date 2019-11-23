---
layout: post
title: 930. All Possible Full Binary Trees
---
### Question
A _full binary tree_  is a binary tree where each node has exactly 0 or 2
children.

Return a list of all possible full binary trees with `N` nodes.  Each element
of the answer is the root node of one possible tree.

Each `node` of each tree in the answer **must** have `node.val = 0`.

You may return the final list of trees in any order.



 **Example 1:**

    
    
     **Input:** 7
    **Output:** [[0,0,0,null,null,0,0,null,null,0,0],[0,0,0,null,null,0,0,0,0],[0,0,0,0,0,0,0],[0,0,0,0,0,null,null,null,null,0,0],[0,0,0,0,0,null,null,0,0]]
    **Explanation:**
    ![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/08/22/fivetrees.png)
    



 **Note:**

  * `1 <= N <= 20`

### Solution 1
I recursively found the possible children of each node at each level. At every
level I subtracted 1 from N since this node counts as a node. (N is given as 1
to 50) If N==1 I returned a node with no children. for every i where i is the
number of left children I called allPossibleFBT(i) and allPossibleFBT(N-i) for
the remaining children on the opposite side. Then I iterate through all
possible combinations of children setting the current node's left and right
children, and add it to the result list.

    
    
    class Solution {
        public List<TreeNode> allPossibleFBT(int N) {
            List<TreeNode> res = new ArrayList<>();
            if(N==1){
                res.add(new TreeNode(0));
                return res;
            }
            N=N-1;
            for(int i=1; i<N;i+=2){
                List<TreeNode> left = allPossibleFBT(i);
                List<TreeNode> right = allPossibleFBT(N-i);
                for(TreeNode nl: left){
                    for(TreeNode nr:right){
                        TreeNode cur = new TreeNode(0);
                        cur.left=nl;
                        cur.right=nr;
                        res.add(cur);
                    }
                }
            }
            return res;
        }
    }
    

We can also add a cache so we don't repeat N values, and return an empty set
if N is even because we can't make a full binary tree with an even number of
nodes. (Don't have to create a copy, we can use the same list).

    
    
    class Solution {
        Map<Integer,List<TreeNode>> cache= new HashMap<>();
        public List<TreeNode> allPossibleFBT(int N) {
            List<TreeNode> res = new ArrayList<>();
            if(N%2==0){
                return res;
            }
            if(cache.containsKey(N)){
                return cache.get(N);
            }
            if(N==1){
                res.add(new TreeNode(0));
                return res;
            }
            
            N=N-1;
            for(int i=1; i<N;i+=2){
                List<TreeNode> left = allPossibleFBT(i);
                List<TreeNode> right = allPossibleFBT(N-i);
                for(TreeNode nl: left){
                    for(TreeNode nr:right){
                        TreeNode cur = new TreeNode(0);
                        cur.left=nl;
                        cur.right=nr;
                        res.add(cur);
                    }
                }
            }
            cache.put(N,res);
            return res;
        }
    }
    


### Solution 2
# TL;DR

## Recursive

    
    
        TreeNode* clone(TreeNode* root) {
          TreeNode* new_root = new TreeNode(0);
          new_root->left = (root->left) ? clone(root->left) : nullptr;
          new_root->right = (root->right) ? clone(root->right) : nullptr; 
          return new_root;
        } 
    
        vector<TreeNode*> allPossibleFBT(int N) {
          std::vector<TreeNode*> ret;
          if (1 == N) {
            ret.emplace_back(new TreeNode(0));
          } else if (N % 2) {
            for (int i = 2; i <= N; i += 2) {
              auto left = allPossibleFBT(i - 1);
              auto right = allPossibleFBT(N - i);
              for (int l_idx = 0; l_idx < left.size(); ++l_idx) {
                for (int r_idx = 0; r_idx < right.size(); ++r_idx) {
                  ret.emplace_back(new TreeNode(0));
                  
                  // If we're using the last right branch, then this will be the last time this left branch is used and can hence
                  // be shallow copied, otherwise the tree will have to be cloned
                  ret.back()->left = (r_idx == right.size() - 1) ? left[l_idx] : clone(left[l_idx]);
                  
                  // If we're using the last left branch, then this will be the last time this right branch is uesd and can hence
                  // be shallow copied, otherwise the tree will have to be cloned
                  ret.back()->right = (l_idx == left.size() - 1) ? right[r_idx] : clone(right[r_idx]);
                }
              }
            }
          }
          return ret;
        }
    

## Iterative with cache

    
    
        TreeNode* clone(TreeNode* root) {
          TreeNode* new_root = new TreeNode(0);
          new_root->left = (root->left) ? clone(root->left) : nullptr;
          new_root->right = (root->right) ? clone(root->right) : nullptr; 
          return new_root;
        } 
    
        vector<TreeNode*> allPossibleFBT(int N) {
          std::vector<TreeNode*> ret;
          
          if (0 == N % 2) {
            return ret;
          }
          
          if (1 == N) {
            return std::vector<TreeNode*>(1, new TreeNode(0));
          }
          
          // For each level, holds all the patterns for all the full BST
          std::vector<std::vector<std::unique_ptr<TreeNode>>> patterns;
          patterns.emplace_back();
          patterns.back().emplace_back(new TreeNode(0));
    
          // Build up a pattern for all the balanaced trees using n - 2 nodes. Branches that form the patterns are 
          // deliberately linked together for efficency. It is not safe to return this, a full clone of
          // each pattern will be required
          for (int lvl = 1; lvl <= N / 2 - 1; ++lvl) {
            patterns.emplace_back();
            for (int r = 0; r < lvl; ++r) {
              for (auto& left : patterns[r]) {
                for (auto& right : patterns[lvl - r - 1]) {
                  patterns.back().emplace_back(new TreeNode(0));
                  patterns.back().back()->left = left.get();
                  patterns.back().back()->right = right.get();
                }
              }
            }
          }
        
          // Final level should actually create a tree that can be returned from the patterns generate above
          for (int r = 0; r < N / 2; ++r) {
            for (auto& left : patterns[r]) {
              for (auto& right : patterns[N / 2 - r - 1]) {
                ret.emplace_back(new TreeNode(0));
                ret.back()->left = clone(left.get());
                ret.back()->right = clone(right.get());
              }
            }
          }
    
          return ret;
        }
    

# Details

Similar to [unique binary search trees](https://leetcode.com/problems/unique-
binary-search-trees-ii/discuss/167055/c++-recursive-and-iterative-
solutions.-Beats-100-and-doesn't-create-Frankenstein-trees) I see a lot of
peoples solutions either return a list of strange Frankenstein trees that are
_connected_ to each other and sharing branches or just straight up leak memory
all over the place.

Frankenstein trees are bad because you have this weird and I dare say
unexpected list of trees that are actually linked to each other. The nightmare
of deleting these trees is not something I would want to deal with.

Memory leaks are also obviously a bad thing.

My solutions deal with both of these problems in different ways, so I will
describe that below and instead focus on the basic idea of my solution.

For starters a full BST must have an even number of nodes. So any even _N_ is
trivial empty return.

For the odd _N_ solution, I felt it is easier to imagine the nodes have actual
values from 1 to _N_. To see why, consider the list of node values [1, 2, 3,
4, 5], if we pick a root node of _3_ , then we can see things break down
quickly. Because the left branch must be build from [1, 2] while the right
must use [4, 5], but neither branch can be a full BST because there is an even
number of nodes to use. If you follow this logic you can see that root node
must be even, and further that each branch's root must also be even. Infact
all internal nodes must be even, while all leaf nodes must be odd.

With this knowledge, we can see that all our example trees must either have
_2_ or _4_ as the root node. Starting with _2_ , that makes the left branch
easy as there is only the _1_ node, while the right branch must use [3, 4, 5].
[3, 4, 5] is also easy since we know the _4_ must be the internal, right root,
node. It follows that we can solve for _N_ if we know all the possible trees
using from _1_ to _N - 2_ nodes. Hence the answer to _N_ can be built up by
calculating the answers for all odd numbers less than _N_. We have something
we can recurse on.

## Recursive solution

Rather that build up from 1 to _N_ , to recurse we build up an answer by
working backwards from _N_ to 1. The problem is that for any root there could
be multiple ways of filling out the left branch and multiple ways of filling
out the right branch, with each selection being a new unique tree. For
example, if _N_ is 11 and we've chosen the middle node as our root, there are
5 nodes to use on each branch. 5 nodes can form a full BST in two ways. We
therefore have 2 possible left branches and 2 possible right branches or a
total of 2 x 2 = 4 posibilites.

Each left branch posibility is used twice (one for each right branch choice),
but we only have a single copy of it, so we can either doubly linked to it and
end up with Frankenstein trees or clone it. The trick is that we don't want
always clone it, because we will end up hanging copy that is never deallocated
and hence memory leak. The solution is to clone for each use except the last
one wheer we will actually use it.

## Iterative solution

When doing an iterative solution, I used a cache to hold all the trees for
each valid _N_ value up to our target _N_. Since the cache was internal I
actually embraced the Frankenstein linking, only allocating the root node of
each tree and linking the left and right branches to existing trees.

When it comes time to actually create the return, I cloned the patterns
generated and stored in the cache. The cache itself is automatically
deallcoated using `unique_ptr`, which is easy because for each level, only the
root nodes are allocated and hence need to be stored in `unique_ptr`, the
branch nodes will get cleaned up as the rest of the levels unwind.

 **If this helps, please don't forget to leave a thumbs up**


### Solution 3
For all combinations of left and right number of children

  1. Recurse into left and right subtree;
  2. Combine results

Note that there is no valid answer for even number of nodes, so we may add an
early exit there.

    
    
     public List<TreeNode> allPossibleFBT(int N) {
            List<TreeNode> list = new ArrayList<>();
            if (N % 2 == 0) return list;
            if (N == 1) { 
                list.add(new TreeNode(0));
                return list;
            }
            for (int leftNum = 1; leftNum <= N-1; leftNum += 2) {
                List<TreeNode> fLeft = allPossibleFBT(leftNum);
                List<TreeNode> fRight = allPossibleFBT(N-leftNum-1);
                for (TreeNode left: fLeft) {
                    for (TreeNode right: fRight) {
                        TreeNode node = new TreeNode(0);
                        node.left = left;
                        node.right = right;
                        list.add(node);
                    }
                }
            }
            
            return list;
        }
    



