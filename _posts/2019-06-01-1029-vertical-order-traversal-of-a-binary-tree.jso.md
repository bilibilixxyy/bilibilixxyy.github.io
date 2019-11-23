---
layout: post
title: 1029. Vertical Order Traversal of a Binary Tree
---
### Question
Given a binary tree, return the _vertical order_ traversal of its nodes
values.

For each node at position `(X, Y)`, its left and right children respectively
will be at positions `(X-1, Y-1)` and `(X+1, Y-1)`.

Running a vertical line from `X = -infinity` to `X = +infinity`, whenever the
vertical line touches some nodes, we report the values of the nodes in order
from top to bottom (decreasing `Y` coordinates).

If two nodes have the same position, then the value of the node that is
reported first is the value that is smaller.

Return an list of non-empty reports in order of `X` coordinate.  Every report
will have a list of values of nodes.



 **Example 1:**

![](https://assets.leetcode.com/uploads/2019/01/31/1236_example_1.PNG)

    
    
     **Input:** [3,9,20,null,null,15,7]
    **Output:** [[9],[3,15],[20],[7]]
    **Explanation:**
    Without loss of generality, we can assume the root node is at position (0, 0):
    Then, the node with value 9 occurs at position (-1, -1);
    The nodes with values 3 and 15 occur at positions (0, 0) and (0, -2);
    The node with value 20 occurs at position (1, -1);
    The node with value 7 occurs at position (2, -2).
    

**Example 2:**

**![](https://assets.leetcode.com/uploads/2019/01/31/tree2.png)**

    
    
    **Input:** [1,2,3,4,5,6,7]
    **Output:** [[4],[2],[1,5,6],[3],[7]]
    **Explanation:**
    The node with value 5 and the node with value 6 have the same position according to the given scheme.
    However, in the report "[1,5,6]", the node value of 5 comes first since 5 is smaller than 6.
    



 **Note:**

  1. The tree will have between 1 and `1000` nodes.
  2. Each node's value will be between `0` and `1000`.

### Solution 1
    
    
    class Solution {
        public List<List<Integer>> verticalTraversal(TreeNode root) {
            TreeMap<Integer, TreeMap<Integer, PriorityQueue<Integer>>> map = new TreeMap<>();
            dfs(root, 0, 0, map);
            List<List<Integer>> list = new ArrayList<>();
            for (TreeMap<Integer, PriorityQueue<Integer>> ys : map.values()) {
                list.add(new ArrayList<>());
                for (PriorityQueue<Integer> nodes : ys.values()) {
                    while (!nodes.isEmpty()) {
                        list.get(list.size() - 1).add(nodes.poll());
                    }
                }
            }
            return list;
        }
        private void dfs(TreeNode root, int x, int y, TreeMap<Integer, TreeMap<Integer, PriorityQueue<Integer>>> map) {
            if (root == null) {
                return;
            }
            if (!map.containsKey(x)) {
                map.put(x, new TreeMap<>());
            }
            if (!map.get(x).containsKey(y)) {
                map.get(x).put(y, new PriorityQueue<>());
            }
            map.get(x).get(y).offer(root.val);
            dfs(root.left, x - 1, y + 1, map);
            dfs(root.right, x + 1, y + 1, map);
        }
    }
    


### Solution 2
    
    
    class Point{
        int x,y,val;
        Point(int x,int y,int val){
            this.x = x;
            this.y = y;
            this.val = val;
        }
    }
    public class Solution {
        public List<List<Integer>> verticalTraversal(TreeNode root) {
            List<List<Integer>> res = new ArrayList<List<Integer>>();
            
            PriorityQueue<Point> pq = new PriorityQueue<Point>(1005,new Comparator<Point>(){
                public int compare(Point p1,Point p2){
                    if(p1.x < p2.x) return -1;
                    if(p2.x < p1.x) return 1;
                    if(p1.y > p2.y) return -1;
                    if(p1.y < p2.y) return 1;
                    return p1.val - p2.val;
                }
            });
            
            verticalTraversalHelper(root,0,0,pq);
            Point prev = null;        
            List<Integer> l = new ArrayList<>();
            while(!pq.isEmpty()){
                Point p = pq.poll();
                if(prev == null || p.x != prev.x){
                    if(prev != null) res.add(l);
                    l = new ArrayList<>();
                }
                l.add(p.val);
                prev = p;
            }
            
            if(res.size() > 0) res.add(l);
            return res;
        }
        
        private void verticalTraversalHelper(TreeNode root,int x,int y,PriorityQueue<Point> pq){
            if(root == null) return;
            pq.offer(new Point(x,y,root.val));
            verticalTraversalHelper(root.left,x-1,y-1,pq);
            verticalTraversalHelper(root.right,x+1,y-1,pq);
        }
    }
    


### Solution 3
"If two nodes have the same position, then the value of the node that is
reported first is the value that is smaller."

This statment is ambiguous which make people think just order from small to
large for each position.

From test case, the real requirement is:  
If two nodes have the same position,

  1. check the layer, the node on higher level(close to root) goes first
  2. if they also in the same level, order from small to large

provide one solution here, but recommand  
<https://leetcode.com/problems/vertical-order-traversal-of-a-binary-
tree/discuss/231125/Java-HashMap-and-TreeMap-and-PriorityQueue-Solution>  
which use `Map<Integer, TreeMap<Integer, PriorityQueue<Integer>>> map = new
HashMap<>();` to store the result. So, we can use any traversal method BFS,
DFS(inorder, preorder, postorder, customized order etc)

    
    
    class Solution {
        int min=0, max=0;
        Map<Integer, List<Integer>> map = new HashMap();
        public List<List<Integer>> verticalTraversal(TreeNode root) {
            List<List<Integer>> res = new ArrayList();
            if(root==null) return res;
            Queue<TreeNode> qt = new LinkedList();
            Queue<Integer> qi = new LinkedList();
            qt.add(root);
            qi.add(0);//not root.val
            while(!qt.isEmpty()){
                int size = qt.size();
                Map<Integer,List<Integer>> tmp = new HashMap();
                for(int i=0;i<size;i++){
                    TreeNode cur = qt.poll();
                    int idx = qi.poll();
                    if(!tmp.containsKey(idx)) tmp.put(idx, new ArrayList<Integer>());
                    tmp.get(idx).add(cur.val);
                    if(idx<min)  min = idx;
                    if(idx>max)  max = idx;
                    if(cur.left!=null){
                        qt.add(cur.left);
                        qi.add(idx-1);
                    }
                    if(cur.right!=null){
                        qt.add(cur.right);
                        qi.add(idx+1);
                    } 
                }
                for(int key : tmp.keySet()){
                    if(!map.containsKey(key)) map.put(key, new ArrayList<Integer>());
                    List<Integer> list = tmp.get(key);
                    Collections.sort(list);
                    map.get(key).addAll(list);
                }
                
            }
            for (int i=min; i<=max; i++){
                List<Integer> list = map.get(i);
                res.add(list);
            }
            return res;
        }
    }
    



