---
layout: post
title: 775. N-ary Tree Preorder Traversal
---
### Question
Given an n-ary tree, return the _preorder_ traversal of its nodes' values.

For example, given a `3-ary` tree:



![](https://assets.leetcode.com/uploads/2018/10/12/narytreeexample.png)



Return its preorder traversal as: `[1,3,5,6,2,4]`.



**Note:**

Recursive solution is trivial, could you do it iteratively?

### Solution 1
Iterative Solution

    
    
    class Solution {
        public List<Integer> preorder(Node root) {
            List<Integer> list = new ArrayList<>();
            if (root == null) return list;
            
            Stack<Node> stack = new Stack<>();
            stack.add(root);
            
            while (!stack.empty()) {
                root = stack.pop();
                list.add(root.val);
                for (int i = root.children.size() - 1; i >= 0; i--)
                    stack.add(root.children.get(i));
            }
            
            return list;
        }
    }
    

Recursive Solution

    
    
    class Solution {
        public List<Integer> list = new ArrayList<>();
        public List<Integer> preorder(Node root) {
            if (root == null)
                return list;
            
            list.add(root.val);
            for(Node node: root.children)
                preorder(node);
                    
            return list;
        }
    }
    


### Solution 2
    
    
    class Solution(object):
        def preorder(self, root):
            ret, q = [], root and [root]
            while q:
                node = q.pop()
                ret.append(node.val)
                q += [child for child in node.children[::-1] if child]
            return ret
    


### Solution 3
recursive

    
    
    class Solution {
    private:
        void travel(Node* root, vector<int>& result) {
            if (root == nullptr) {
                return;
            }
            
            result.push_back(root -> val);
            for (Node* child : root -> children) {
                travel(child, result);
            }
        }
    public:
        vector<int> preorder(Node* root) {
            vector<int> result;
            travel(root, result);
            return result;
        }
    };
    

iterative:  
Added an example to elaborate how stack works in the preorder traversal.  
Let's have a tree like below:

    
    
              1
         /        \
       2            3
     /   \         /   \
    4     5       6     7
    

The right preorder sequence will be:

    
    
    1  2  4  5  3  6  7
    

From the code we use a stack to simulate the process:

  1. we push 1 to the stack.
  2. we pop 1 out, add 1 into result; Add the children of 1 into stack. The value in the stack will be 3, 2 and 2 at the top position;
  3. we pop 2 out and add it to result; Then we add children of 2 into stack. So the stack will be like 3, 5, 4 and with 4 at the top.
  4. we pop 4 and 5 out of stack since they are leaf node. Currently result will be like 1, 2, 4, 5.
  5. we pop 3 out and add its children into stack. The stack is like 7, 6 with 6 at the top.
  6. we pop 6 and 7 out and the stack becomes empty.  
So the final result will be 1, 2, 4, 5, 3, 6, 7

    
    
    class Solution {
    public:
        vector<int> preorder(Node* root) {
            vector<int> result;
            if (root == nullptr) {
                return result;
            }
            
            stack<Node*> stk;
            stk.push(root);
            while (!stk.empty()) {
                Node* cur = stk.top();
                stk.pop();
                result.push_back(cur -> val);
                for (int i = cur -> children.size() - 1; i >= 0; i--) {
                    if (cur -> children[i] != nullptr) {
                        stk.push(cur -> children[i]);
                    }
                }
            }
            return result;
        }
    };
    



