---
layout: post
title: 776. N-ary Tree Postorder Traversal
---
### Question
Given an n-ary tree, return the _postorder_ traversal of its nodes' values.

For example, given a `3-ary` tree:



![](https://assets.leetcode.com/uploads/2018/10/12/narytreeexample.png)



Return its postorder traversal as: `[5,6,3,2,4,1]`.



**Note:**

Recursive solution is trivial, could you do it iteratively?

### Solution 1
Iterative

    
    
    class Solution {
        public List<Integer> postorder(Node root) {
            List<Integer> list = new ArrayList<>();
            if (root == null) return list;
            
            Stack<Node> stack = new Stack<>();
            stack.add(root);
            
            while(!stack.isEmpty()) {
                root = stack.pop();
                list.add(root.val);
                for(Node node: root.children)
                    stack.add(node);
            }
            Collections.reverse(list);
            return list;
        }
    }
    

Recursive

    
    
    class Solution {
        List<Integer> list = new ArrayList<>();
        public List<Integer> postorder(Node root) {
            if (root == null)
                return list;
            
            for(Node node: root.children)
                postorder(node);
            
            list.add(root.val);
            
            return list;
        }
    }
    


### Solution 2
    
    
    vector<int> postorder(Node* root) {
        if(root==NULL) return {};
        vector<int> res;
        stack<Node*> stk;
        stk.push(root);
        while(!stk.empty())
        {
            Node* temp=stk.top();
            stk.pop();
            for(int i=0;i<temp->children.size();i++) stk.push(temp->children[i]);
            res.push_back(temp->val);
        }
        reverse(res.begin(), res.end());
        return res;
    }


### Solution 3
 **Recursion** is easy to implement and understand by definition
<https://en.wikipedia.org/wiki/Tree_traversal#Post-order_(LRN)>.

    
    
     def postorder(self, root):
            """
            :type root: Node
            :rtype: List[int]
            """
            res = []
            if root == None: return res
    
            def recursion(root, res):
                for child in root.children:
                    recursion(child, res)
                res.append(root.val)
    
            recursion(root, res)
            return res
    

**Iteration** is basically pre-order traversal but rather than go left, go
right first then reverse its result.

    
    
    def postorder(self, root):
             res = []
            if root == None: return res
    
            stack = [root]
            while stack:
                curr = stack.pop()
                res.append(curr.val)
                stack.extend(curr.children)
    
            return res[::-1]
    



