---
layout: post
title: 508. Most Frequent Subtree Sum
---
### Question
Given the root of a tree, you are asked to find the most frequent subtree sum.
The subtree sum of a node is defined as the sum of all the node values formed
by the subtree rooted at that node (including the node itself). So what is the
most frequent subtree sum value? If there is a tie, return all the values with
the highest frequency in any order.

 **Examples 1**  
Input:

    
    
      5
     /  \
    2   -3
    

return [2, -3, 4], since all the values happen only once, return all of them
in any order.

**Examples 2**  
Input:

    
    
      5
     /  \
    2   -5
    

return [2], since 2 happens twice, however -5 only occur once.

 **Note:** You may assume the sum of values in any subtree is in the range of
32-bit signed integer.

### Solution 1
Use a hashMap `count` to count the subtree sum occurrence.

A sub function `dfs(TreeNode node)` will  
travel through a tree, recursively calculate the sum of subtree,  
increment the `count`, and finally return the sum of the sub tree.

 **Java**

    
    
        Map< Integer, Integer> count = new HashMap<Integer, Integer>();
        int maxCount = 0;
    
        public int[] findFrequentTreeSum(TreeNode root) {
            dfs(root);
            List<Integer> res = new ArrayList<>();
            for (int s : count.keySet()) {
                if (count.get(s) == maxCount)
                    res.add(s);
            }
            return res.stream().mapToInt(i->i).toArray();
        }
    
        private int dfs(TreeNode root) {
            if (root == null) return 0;
            int s = dfs(root.left) + dfs(root.right) + root.val;
            count.put(s, count.getOrDefault(s, 0) + 1);
            maxCount = Math.max(maxCount, count.get(s));
            return s;
        }
    

**C++**

    
    
         unordered_map<int, int> count;
        int maxCount = 0;
        vector<int> findFrequentTreeSum(TreeNode* root) {
            dfs(root);
            vector<int> res;
            for (auto & e: count)
                if (e.second == maxCount)
                    res.push_back(e.first);
            return res;
        }
    
    
        int dfs(TreeNode* root) {
            if (root == NULL) return 0;
            int s = dfs(root->left) + dfs(root->right) + root->val;
            maxCount = max(maxCount, ++count[s]);
            return s;
        }
    

**Python**

    
    
        def findFrequentTreeSum(self, root):
            if root is None: return []
    
            def dfs( node):
                if node is None: return 0
                s = node.val + dfs(node.left) + dfs(node.right)
                count[s] += 1
                return s
    
            count = collections.Counter()
            dfs(root)
            maxCount = max(count.values())
            return [s for s in count if count[s] == maxCount]
    

**Update**  
I have changed a little to make the variable name shorter.


### Solution 2
For sake of saving time during contest, can't write so concise solution :)  
Idea is `post-order` traverse the tree and get sum of every sub-tree, put
`sum` to `count` mapping to a HashMap. Then generate result based on the
HashMap.

    
    
    public class Solution {
        Map<Integer, Integer> sumToCount;
        int maxCount;
        
        public int[] findFrequentTreeSum(TreeNode root) {
            maxCount = 0;
            sumToCount = new HashMap<Integer, Integer>();
            
            postOrder(root);
            
            List<Integer> res = new ArrayList<>();
            for (int key : sumToCount.keySet()) {
                if (sumToCount.get(key) == maxCount) {
                    res.add(key);
                }
            }
            
            int[] result = new int[res.size()];
            for (int i = 0; i < res.size(); i++) {
                result[i] = res.get(i);
            }
            return result;
        }
        
        private int postOrder(TreeNode root) {
            if (root == null) return 0;
            
            int left = postOrder(root.left);
            int right = postOrder(root.right);
            
            int sum = left + right + root.val;
            int count = sumToCount.getOrDefault(sum, 0) + 1;
            sumToCount.put(sum, count);
            
            maxCount = Math.max(maxCount, count);
            
            return sum;
        }
    }
    


### Solution 3
    
    
    class Solution {
    public:
        vector<int> findFrequentTreeSum(TreeNode* root) {
            unordered_map<int,int> counts;
            int maxCount = 0;
            countSubtreeSums(root, counts, maxCount);
            
            
            vector<int> maxSums;
            for(const auto& x :  counts){
                if(x.second == maxCount) maxSums.push_back(x.first);
            }
            return maxSums;
        }
        
        int countSubtreeSums(TreeNode *r, unordered_map<int,int> &counts, int& maxCount){
            if(r == nullptr) return 0;
            
            int sum = r->val;
            sum += countSubtreeSums(r->left, counts, maxCount);
            sum += countSubtreeSums(r->right, counts, maxCount);
            ++counts[sum];
            maxCount = max(maxCount, counts[sum]);
            return sum;
        }
    };



