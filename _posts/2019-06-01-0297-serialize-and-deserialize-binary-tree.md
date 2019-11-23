---
layout: post
title: 297. Serialize and Deserialize Binary Tree
---
### Question
Serialization is the process of converting a data structure or object into a
sequence of bits so that it can be stored in a file or memory buffer, or
transmitted across a network connection link to be reconstructed later in the
same or another computer environment.

Design an algorithm to serialize and deserialize a binary tree. There is no
restriction on how your serialization/deserialization algorithm should work.
You just need to ensure that a binary tree can be serialized to a string and
this string can be deserialized to the original tree structure.

 **Example:  **

    
    
    You may serialize the following tree:
    
        1
       / \
      2   3
         / \
        4   5
    
    as "[1,2,3,null,null,4,5]"
    

**Clarification:** The above format is the same as [how LeetCode serializes a
binary tree](/faq/#binary-tree). You do not necessarily need to follow this
format, so please be creative and come up with different approaches yourself.

 **Note:  **Do not use class member/global/static variables to store states.
Your serialize and deserialize algorithms should be stateless.

### Solution 1
The idea is simple: print the tree in pre-order traversal and use "X" to
denote null node and split node with ",". We can use a StringBuilder for
building the string on the fly. For deserializing, we use a Queue to store the
pre-order traversal and since we have "X" as null node, we know exactly how to
where to end building subtress.

    
    
    public class Codec {
        private static final String spliter = ",";
        private static final String NN = "X";
    
        // Encodes a tree to a single string.
        public String serialize(TreeNode root) {
            StringBuilder sb = new StringBuilder();
            buildString(root, sb);
            return sb.toString();
        }
    
        private void buildString(TreeNode node, StringBuilder sb) {
            if (node == null) {
                sb.append(NN).append(spliter);
            } else {
                sb.append(node.val).append(spliter);
                buildString(node.left, sb);
                buildString(node.right,sb);
            }
        }
        // Decodes your encoded data to tree.
        public TreeNode deserialize(String data) {
            Deque<String> nodes = new LinkedList<>();
            nodes.addAll(Arrays.asList(data.split(spliter)));
            return buildTree(nodes);
        }
        
        private TreeNode buildTree(Deque<String> nodes) {
            String val = nodes.remove();
            if (val.equals(NN)) return null;
            else {
                TreeNode node = new TreeNode(Integer.valueOf(val));
                node.left = buildTree(nodes);
                node.right = buildTree(nodes);
                return node;
            }
        }
    }


### Solution 2
 **Python**

    
    
    class Codec:
    
        def serialize(self, root):
            def doit( node):
                if node:
                    vals.append(str(node.val))
                    doit(node.left)
                    doit(node.right)
                else:
                    vals.append('#')
            vals = []
            doit(root)
            return ' '.join(vals)
    
        def deserialize(self, data):
            def doit():
                val = next(vals)
                if val == '#':
                    return None
                node = TreeNode(int(val))
                node.left = doit()
                node.right = doit()
                return node
            vals = iter(data.split())
            return doit()
    

* * *

**C++**

    
    
     class Codec {
    public:
    
        string serialize(TreeNode* root) {
            ostringstream out;
            serialize(root, out);
            return out.str();
        }
    
        TreeNode* deserialize(string data) {
            istringstream in(data);
            return deserialize(in);
        }
    
    private:
    
        void serialize(TreeNode* root, ostringstream& out) {
            if (root) {
                out << root->val << ' ';
                serialize(root->left, out);
                serialize(root->right, out);
            } else {
                out << "# ";
            }
        }
    
        TreeNode* deserialize(istringstream& in) {
            string val;
            in >> val;
            if (val == "#")
                return nullptr;
            TreeNode* root = new TreeNode(stoi(val));
            root->left = deserialize(in);
            root->right = deserialize(in);
            return root;
        }
    };


### Solution 3
Here I use typical BFS method to handle a binary tree. I use string `n` to
represent null values. The string of the binary tree in the example will be
`"1 2 3 n n 4 5 n n n n "`.

When deserialize the string, I assign left and right child for each not-null
node, and add the not-null children to the queue, waiting to be handled later.

    
    
    public class Codec {
        public String serialize(TreeNode root) {
            if (root == null) return "";
            Queue<TreeNode> q = new LinkedList<>();
            StringBuilder res = new StringBuilder();
            q.add(root);
            while (!q.isEmpty()) {
                TreeNode node = q.poll();
                if (node == null) {
                    res.append("n ");
                    continue;
                }
                res.append(node.val + " ");
                q.add(node.left);
                q.add(node.right);
            }
            return res.toString();
        }
    
        public TreeNode deserialize(String data) {
            if (data == "") return null;
            Queue<TreeNode> q = new LinkedList<>();
            String[] values = data.split(" ");
            TreeNode root = new TreeNode(Integer.parseInt(values[0]));
            q.add(root);
            for (int i = 1; i < values.length; i++) {
                TreeNode parent = q.poll();
                if (!values[i].equals("n")) {
                    TreeNode left = new TreeNode(Integer.parseInt(values[i]));
                    parent.left = left;
                    q.add(left);
                }
                if (!values[++i].equals("n")) {
                    TreeNode right = new TreeNode(Integer.parseInt(values[i]));
                    parent.right = right;
                    q.add(right);
                }
            }
            return root;
        }
    }



