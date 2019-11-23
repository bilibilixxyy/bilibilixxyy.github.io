---
layout: post
title: 94. Binary Tree Inorder Traversal
---
### Question
Given a binary tree, return the _inorder_ traversal of its nodes' values.

 **Example:**

    
    
     **Input:** [1,null,2,3]
       1
        \
         2
        /
       3
    
    **Output:** [1,3,2]

 **Follow up:** Recursive solution is trivial, could you do it iteratively?

### Solution 1
    
    
     public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<Integer>();
    
        Stack<TreeNode> stack = new Stack<TreeNode>();
        TreeNode cur = root;
    
        while(cur!=null || !stack.empty()){
            while(cur!=null){
                stack.add(cur);
                cur = cur.left;
            }
            cur = stack.pop();
            list.add(cur.val);
            cur = cur.right;
        }
    
        return list;
    }


### Solution 2
    
    
    # recursively
    def inorderTraversal1(self, root):
        res = []
        self.helper(root, res)
        return res
        
    def helper(self, root, res):
        if root:
            self.helper(root.left, res)
            res.append(root.val)
            self.helper(root.right, res)
     
    # iteratively       
    def inorderTraversal(self, root):
        res, stack = [], []
        while True:
            while root:
                stack.append(root)
                root = root.left
            if not stack:
                return res
            node = stack.pop()
            res.append(node.val)
            root = node.right


### Solution 3
Method 1: Using one stack and the binary tree node will be changed. Easy ,not
Practical

    
    
    class Solution {
    public:
        vector<int> inorderTraversal(TreeNode *root) {
            vector<int> vector;
            if(!root)
            return vector;
            stack<TreeNode *> stack;
            stack.push(root);
            while(!stack.empty())
            {
                TreeNode *pNode = stack.top();
                if(pNode->left)
                {
                    stack.push(pNode->left);
                    pNode->left = NULL;
                }
                else
                {
                    vector.push_back(pNode->val);
                    stack.pop();
                    if(pNode->right)
                    stack.push(pNode->right);
                }
            }
            return vector;
        }
    };
    

Method 2: Using one stack and one unordered_map, this will not changed the
node. Better

    
    
    class Solution {
    public:
        vector<int> inorderTraversal(TreeNode *root) {
            vector<int> vector;
            if(!root)
            return vector;
            unordered_map<TreeNode *, bool> map;//left child has been visited:true.
            stack<TreeNode *> stack;
            stack.push(root);
            while(!stack.empty())
            {
                TreeNode *pNode = stack.top();
                if(pNode->left && !map[pNode])
                {
                    stack.push(pNode->left);
                    map[pNode] = true;
                }
                else
                {
                    vector.push_back(pNode->val);
                    stack.pop();
                    if(pNode->right)
                    stack.push(pNode->right);
                }
            }
            return vector;
        }
    };
    

Method 3: Using one stack and will not changed the node. Best(at least in this
three solutions)

    
    
    class Solution {
    public:
        vector<int> inorderTraversal(TreeNode *root) {
            vector<int> vector;
            stack<TreeNode *> stack;
            TreeNode *pCurrent = root;
            
            while(!stack.empty() || pCurrent)
            {
                if(pCurrent)
                {
                    stack.push(pCurrent);
                    pCurrent = pCurrent->left;
                }
                else
                {
                    TreeNode *pNode = stack.top();
                    vector.push_back(pNode->val);
                    stack.pop();
                    pCurrent = pNode->right;
                }
            }
            return vector;
        }
    };



