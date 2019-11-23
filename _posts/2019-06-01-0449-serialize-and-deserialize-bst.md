---
layout: post
title: 449. Serialize and Deserialize BST
---
### Question
Serialization is the process of converting a data structure or object into a
sequence of bits so that it can be stored in a file or memory buffer, or
transmitted across a network connection link to be reconstructed later in the
same or another computer environment.

Design an algorithm to serialize and deserialize a **binary search tree**.
There is no restriction on how your serialization/deserialization algorithm
should work. You just need to ensure that a binary search tree can be
serialized to a string and this string can be deserialized to the original
tree structure.

**The encoded string should be as compact as possible.**

**Note:** Do not use class member/global/static variables to store states.
Your serialize and deserialize algorithms should be stateless.

### Solution 1
Hi all, I think my solution is pretty straightforward and easy to understand,
not that efficient though. And the serialized tree is compact.  
Pre order traversal of BST will output root node first, then left children,
then right.

    
    
    root left1 left2 leftX right1 rightX
    

If we look at the value of the pre-order traversal we get this:

    
    
    rootValue (<rootValue) (<rootValue) (<rootValue) |separate line| (>rootValue) (>rootValue)
    

Because of BST's property: before the |separate line| all the node values are
**less than root value** , all the node values after |separate line| are
**greater than root value**. We will utilize this to build left and right
tree.

Pre-order traversal is BST's serialized string. I am doing it iteratively.  
To deserialized it, use a queue to recursively get root node, left subtree and
right subtree.

I think time complexity is O(NlogN).  
Errr, my bad, as @ray050899 put below, worst case complexity should be O(N^2),
when the tree is really unbalanced.

My implementation

    
    
    public class Codec {
        private static final String SEP = ",";
        private static final String NULL = "null";
        // Encodes a tree to a single string.
        public String serialize(TreeNode root) {
            StringBuilder sb = new StringBuilder();
            if (root == null) return NULL;
            //traverse it recursively if you want to, I am doing it iteratively here
            Stack<TreeNode> st = new Stack<>();
            st.push(root);
            while (!st.empty()) {
                root = st.pop();
                sb.append(root.val).append(SEP);
                if (root.right != null) st.push(root.right);
                if (root.left != null) st.push(root.left);
            }
            return sb.toString();
        }
    
        // Decodes your encoded data to tree.
        // pre-order traversal
        public TreeNode deserialize(String data) {
            if (data.equals(NULL)) return null;
            String[] strs = data.split(SEP);
            Queue<Integer> q = new LinkedList<>();
            for (String e : strs) {
                q.offer(Integer.parseInt(e));
            }
            return getNode(q);
        }
        
        // some notes:
        //   5
        //  3 6
        // 2   7
        private TreeNode getNode(Queue<Integer> q) { //q: 5,3,2,6,7
            if (q.isEmpty()) return null;
            TreeNode root = new TreeNode(q.poll());//root (5)
            Queue<Integer> samllerQueue = new LinkedList<>();
            while (!q.isEmpty() && q.peek() < root.val) {
                samllerQueue.offer(q.poll());
            }
            //smallerQueue : 3,2   storing elements smaller than 5 (root)
            root.left = getNode(samllerQueue);
            //q: 6,7   storing elements bigger than 5 (root)
            root.right = getNode(q);
            return root;
        }
    }
    


### Solution 2
Sharing my solution which doesn't have to parse string for comma at all!

The encoding schema is preorder of BST, and to decode this we can use the same
preorder traversal to do it in one pass with recursion in O(n) time.

To minimize the memory, I used binary format instead of ascii format for each
integer, just burn those int into 4 chars will save you a lot!!!

Really if using ASCII numbers you are paying a lot of penalty memory for
integers over 4 digit long and parsing comma is just as painful.

    
    
    class Codec {
    public:
    
        // Encodes a tree to a single string.
        string serialize(TreeNode* root) {
            string order;
            inorderDFS(root, order);
            return order;
        }
        
        inline void inorderDFS(TreeNode* root, string& order) {
            if (!root) return;
            char buf[4];
            memcpy(buf, &(root->val), sizeof(int)); //burn the int into 4 chars
            for (int i=0; i<4; i++) order.push_back(buf[i]);
            inorderDFS(root->left, order);
            inorderDFS(root->right, order);
        }
    
        // Decodes your encoded data to tree.
        TreeNode* deserialize(string data) {
            int pos = 0;
            return reconstruct(data, pos, INT_MIN, INT_MAX);
        }
        
        inline TreeNode* reconstruct(const string& buffer, int& pos, int minValue, int maxValue) {
            if (pos >= buffer.size()) return NULL; //using pos to check whether buffer ends is better than using char* directly.
            
            int value;
            memcpy(&value, &buffer[pos], sizeof(int));
            if (value < minValue || value > maxValue) return NULL;
            
            TreeNode* node = new TreeNode(value);
            pos += sizeof(int);
            node->left = reconstruct(buffer, pos, minValue, value);
            node->right = reconstruct(buffer, pos, value, maxValue);
            return node;
        }
    };
    


### Solution 3
### BST

use upper and lower boundaries to check whether we should add `null`

    
    
    /**
     * Definition for a binary tree node.
     * public class TreeNode {
     *     int val;
     *     TreeNode left;
     *     TreeNode right;
     *     TreeNode(int x) { val = x; }
     * }
     */
    public class Codec {
    
        // Encodes a tree to a single string.
        public String serialize(TreeNode root) {
            StringBuilder sb = new StringBuilder();
            serialize(root, sb);
            return sb.toString();
        }
        
        public void serialize(TreeNode root, StringBuilder sb) {
            if (root == null) return;
            sb.append(root.val).append(",");
            serialize(root.left, sb);
            serialize(root.right, sb);
        }
    
        // Decodes your encoded data to tree.
        public TreeNode deserialize(String data) {
            if (data.isEmpty()) return null;
            Queue<String> q = new LinkedList<>(Arrays.asList(data.split(",")));
            return deserialize(q, Integer.MIN_VALUE, Integer.MAX_VALUE);
        }
        
        public TreeNode deserialize(Queue<String> q, int lower, int upper) {
            if (q.isEmpty()) return null;
            String s = q.peek();
            int val = Integer.parseInt(s);
            if (val < lower || val > upper) return null;
            q.poll();
            TreeNode root = new TreeNode(val);
            root.left = deserialize(q, lower, val);
            root.right = deserialize(q, val, upper);
            return root;
        }
    }
    
    // Your Codec object will be instantiated and called as such:
    // Codec codec = new Codec();
    // codec.deserialize(codec.serialize(root));
    

### Binary Tree

use `#` whether we should add `null`

    
    
    /**
     * Definition for a binary tree node.
     * public class TreeNode {
     *     int val;
     *     TreeNode left;
     *     TreeNode right;
     *     TreeNode(int x) { val = x; }
     * }
     */
    public class Codec {
    
        // Encodes a tree to a single string.
        public String serialize(TreeNode root) {
            StringBuilder sb = new StringBuilder();
            serialize(root, sb);
            return sb.toString();
        }
        
        public void serialize(TreeNode root, StringBuilder sb) {
            if (root == null) {
                sb.append("#").append(",");
            } else {
                sb.append(root.val).append(",");
                serialize(root.left, sb);
                serialize(root.right, sb);
            }
        }
    
        // Decodes your encoded data to tree.
        public TreeNode deserialize(String data) {
            Queue<String> q = new LinkedList<>(Arrays.asList(data.split(",")));
            return deserialize(q);
        }
        
        public TreeNode deserialize(Queue<String> q) {
            String s = q.poll();
            if (s.equals("#")) return null;
            TreeNode root = new TreeNode(Integer.parseInt(s));
            root.left = deserialize(q);
            root.right = deserialize(q);
            return root;
        }
        
    }
    
    // Your Codec object will be instantiated and called as such:
    // Codec codec = new Codec();
    // codec.deserialize(codec.serialize(root));
    



