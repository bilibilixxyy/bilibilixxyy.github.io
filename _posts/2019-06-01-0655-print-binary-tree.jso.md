---
layout: post
title: 655. Print Binary Tree
---
### Question
Print a binary tree in an m*n 2D string array following these rules:

  1. The row number `m` should be equal to the height of the given binary tree.
  2. The column number `n` should always be an odd number.
  3. The root node's value (in string format) should be put in the exactly middle of the first row it can be put. The column and the row where the root node belongs will separate the rest space into two parts ( **left-bottom part and right-bottom part** ). You should print the left subtree in the left-bottom part and print the right subtree in the right-bottom part. The left-bottom part and the right-bottom part should have the same size. Even if one subtree is none while the other is not, you don't need to print anything for the none subtree but still need to leave the space as large as that for the other subtree. However, if two subtrees are none, then you don't need to leave space for both of them. 
  4. Each unused space should contain an empty string `""`.
  5. Print the subtrees following the same rules.

 **Example 1:**  

    
    
     **Input:**
         1
        /
       2
    **Output:**
    [["", "1", ""],
     ["2", "", ""]]
    

**Example 2:**  

    
    
    **Input:**
         1
        / \
       2   3
        \
         4
    **Output:**
    [["", "", "", "1", "", "", ""],
     ["", "2", "", "", "", "3", ""],
     ["", "", "4", "", "", "", ""]]
    

**Example 3:**  

    
    
    **Input:**
          1
         / \
        2   5
       / 
      3 
     / 
    4 
    **Output:**
    
    [["",  "",  "", "",  "", "", "", "1", "",  "",  "",  "",  "", "", ""]
     ["",  "",  "", "2", "", "", "", "",  "",  "",  "",  "5", "", "", ""]
     ["",  "3", "", "",  "", "", "", "",  "",  "",  "",  "",  "", "", ""]
     ["4", "",  "", "",  "", "", "", "",  "",  "",  "",  "",  "", "", ""]]
    

**Note:** The height of binary tree is in the range of [1, 10].

### Solution 1
    
    
    class Solution(object):
        def printTree(self, root):
            """
            :type root: TreeNode
            :rtype: List[List[str]]
            """
            def get_height(node):
                return 0 if not node else 1 + max(get_height(node.left), get_height(node.right))
            
            def update_output(node, row, left, right):
                if not node:
                    return
                mid = (left + right) / 2
                self.output[row][mid] = str(node.val)
                update_output(node.left, row + 1 , left, mid - 1)
                update_output(node.right, row + 1 , mid + 1, right)
                
            height = get_height(root)
            width = 2 ** height - 1
            self.output = [[''] * width for i in xrange(height)]
            update_output(node=root, row=0, left=0, right=width - 1)
            return self.output
    


### Solution 2
    
    
    public List<List<String>> printTree(TreeNode root) {
        List<List<String>> res = new LinkedList<>();
        int height = root == null ? 1 : getHeight(root);
        int rows = height, columns = (int) (Math.pow(2, height) - 1);
        List<String> row = new ArrayList<>();
        for(int i = 0; i < columns; i++)  row.add("");
        for(int i = 0; i < rows; i++)  res.add(new ArrayList<>(row));
        populateRes(root, res, 0, rows, 0, columns - 1);
        return res;
    }
    
    public void populateRes(TreeNode root, List<List<String>> res, int row, int totalRows, int i, int j) {
        if (row == totalRows || root == null) return;
        res.get(row).set((i+j)/2, Integer.toString(root.val));
        populateRes(root.left, res, row+1, totalRows, i, (i+j)/2 - 1);
        populateRes(root.right, res, row+1, totalRows, (i+j)/2+1, j);
    }
    
    public int getHeight(TreeNode root) {
         if (root == null) return 0;
         return 1 + Math.max(getHeight(root.left), getHeight(root.right));
    }
    


### Solution 3
It could be fairly easy when we made our first observation on the problem. For
the output matrix, the number of rows is **height of the tree**. What about
the number of columns?

  * `row = 1 => col = 1 = 2^1 - 1`
  * `row = 2 => col = 3 = 2^2 - 1`
  * `row = 3 => col = 7 = 2^3 - 1`
  * `row = 4 => col = 15 = 2^4 - 1`  
`...`

  * `row = m => col = 2^m - 1`

This can be derived from the number of leaves of a full tree (i.e `2^(height -
1)`) with spaces joined (i.e `2^(height - 1) - 1`).

Then we can fill the node in level by level. Another observation is **we
always print a node at the center of its subtree index range**. What I mean is
for the left or right child of a `node`, the subtree rooted at the child will
use half of the indices of the `node`.

  * `root` is at the center of `left` and `right`, say `mid`
  * `root.left` (if not null) is at the center of `left` and `mid - 1`
  * `root.right` (if not null) is at the center of `mid + 1` and `right`

Then we can easily have our solution as we always keep track of the `left` and
`right` of the node.

    
    
    public class Solution {
        public List<List<String>> printTree(TreeNode root) {
            List<List<String>> res = new ArrayList<>();
            if (root == null) {
                return res;
            }
            
            int rows = getHeight(root);
            int cols = (int)Math.pow(2, rows) - 1;
            for (int i = 0; i < rows; i++) {
                List<String> row = new ArrayList<>();
                for (int j = 0; j < cols; j++) {
                    row.add("");
                }
                res.add(row);
            }
            
            Queue<TreeNode> queue = new LinkedList<>();
            Queue<int[]> indexQ = new LinkedList<>();
            queue.offer(root);
            indexQ.offer(new int[] { 0, cols - 1 });
            int row = -1;
            while (!queue.isEmpty()) {
                row++;
                int size = queue.size();
                for (int i = 0; i < size; i++) {
                    TreeNode cur = queue.poll();
                    int[] indices = indexQ.poll();
                    
                    if (cur == null) {
                        continue;
                    }
                    
                    int left = indices[0];
                    int right = indices[1];
                    int mid = left + (right - left) / 2;
                    res.get(row).set(mid, String.valueOf(cur.val));
                    
                    queue.offer(cur.left);
                    queue.offer(cur.right);
                    indexQ.offer(new int[] { left, mid - 1 });
                    indexQ.offer(new int[] { mid + 1, right });
                }
            }
            
            return res;
        }
        
        private int getHeight(TreeNode root) {
            if (root == null) {
                return 0;
            }
            return Math.max(getHeight(root.left), getHeight(root.right)) + 1;
        }
    }
    



