---
layout: post
title: 421. Maximum XOR of Two Numbers in an Array
---
### Question
Given a **non-empty** array of numbers, a 0, a1, a2, … , an-1, where 0 ≤ ai <
231.

Find the maximum result of ai XOR aj, where 0 ≤ _i_ , _j_ < _n_.

Could you do this in O( _n_ ) runtime?

 **Example:**

    
    
     **Input:** [3, 10, 5, 25, 2, 8]
    
    **Output:** 28
    
    **Explanation:** The maximum result is **5** ^ **25** = 28.
    

### Solution 1
    
    
     public class Solution {
        public int findMaximumXOR(int[] nums) {
            int max = 0, mask = 0;
            for(int i = 31; i >= 0; i--){
                mask = mask | (1 << i);
                Set<Integer> set = new HashSet<>();
                for(int num : nums){
                    set.add(num & mask);
                }
                int tmp = max | (1 << i);
                for(int prefix : set){
                    if(set.contains(tmp ^ prefix)) {
                        max = tmp;
                        break;
                    }
                }
            }
            return max;
        }
    }
    


### Solution 2
对于`[3,10,5,25,2,8]`测试用例，如果使用O(n^2)算法，选取3与剩下的数进行异或。这个过程中对于5,10,8的`二进制`前28位异或结果是一样的，对于10,8而言前30位异或结果是一致的。很容易想到对数组中的数的二进制，构建前缀树。相同前缀的计算结果可复用，就能提升效率。

####  **建树**

因为二进制元素只有0,1。考虑使用二叉树来构建前缀树。

  * 根节点为符号位0
  * 从高位到低位依次构建
  * 左子树为1节点，又子树为0节点  
`[3,10,5,25,2,8]` 按照以上规则构建如下图（省略高位0）

![image](https://s3-lc-
upload.s3.amazonaws.com/users/lydoo/image_1526111467.png)

那么这颗树包含了数组中所有的数字，从根节点到叶子节点的一条路径表示数组中的一个十进制数的二进制编码。

####  **找最大异或值**

对于`25` (`0000 0000 0000 0000 0000 0000 0001 1001`)
而言，从第31-6位（都是正数，不考虑符号位），都为0,，且数组中的数字31-6位都为0，因此最大异或结果31-6位只能为0。  
当前使得异或结果最大的数`x`为`0000 0000 0000 0000 0000 0000 000? ????`  
当前指针`curNode`指向第图中根节点。  
从第5位开始：  
`5 1` `x`的第5位为0，则结果最大。应选择右分支，`curNode = curNode->right`  
`4 1` `x`的第4位为0，则结果最大。应选择右分支，`curNode = curNode->right`  
`3 0` `x`的第3位为1，则结果最大。应选择左分支，`curNode = curNode->left`  
`2 0` `x`的第2位为1，则结果最大。应选择左分支，但树中左分支为null，只能选择右分支`curNode = curNode->right`
于是`x`的第2位为0。  
`1 1` `x`的第1位为0，则结果最大。应选择右分支，但树中右分支为null，只能选择左分支`curNode = curNode->left`
于是`x`的第1位为1。  
找到的x为5（00101）。

####  **Code**

    
    
     struct TrieNode{
        int val;
        TrieNode *left;
        TrieNode *right;
        TrieNode(int x) : val(x), left(NULL), right(NULL) {}
    };
    class Solution {
    public:
    
        int findMaximumXOR(vector<int>& nums) {
            TrieNode* root = new TrieNode(0);
    
    
            //建树
            TrieNode* curNode = root;
            for(int i = 0; i < nums.size(); i++){
                for(int j = 31; j >= 0; j--) {
                    int tmp = nums[i] & (1 << j);
                    if(tmp == 0){
                        if(!curNode->right){
                            curNode->right = new TrieNode(0);
                        }
                        curNode = curNode->right;
                    }else{
                        if(!curNode->left){
                            curNode->left = new TrieNode(1);
                        }
                        curNode = curNode->left;
                    }
                    //cout << curNode->val;
                }
                //cout << endl;
                curNode = root;
            }
    
            //匹配最大异或值
            int max = 0;
            for(int i = 0; i < nums.size(); i++){
                int res = 0;
                for(int j = 31; j >= 0; j--){
                    int tmp = nums[i] & (1 << j);
                    //cout << (1 << j) << "	" << tmp << endl;
                    if(curNode->left && curNode->right){
                        if(tmp == 0){
                            curNode = curNode->left;
                        }else {
                            curNode = curNode->right;
                        }    
                    }else {
                        curNode = curNode->left == NULL ? curNode->right:curNode->left;
                    }
                    res += tmp ^ (curNode->val << j);
                    //cout << curNode->val;
                }
                curNode = root;
                //cout << endl;
                max = max > res?max:res;
            }
    
            return max;
        }
    };
    

可以再优化一下，不需要多次移位。


### Solution 3
    
    
        class Trie {
            Trie[] children;
            public Trie() {
                children = new Trie[2];
            }
        }
        
        public int findMaximumXOR(int[] nums) {
            if(nums == null || nums.length == 0) {
                return 0;
            }
            // Init Trie.
            Trie root = new Trie();
            for(int num: nums) {
                Trie curNode = root;
                for(int i = 31; i >= 0; i --) {
                    int curBit = (num >>> i) & 1;
                    if(curNode.children[curBit] == null) {
                        curNode.children[curBit] = new Trie();
                    }
                    curNode = curNode.children[curBit];
                }
            }
            int max = Integer.MIN_VALUE;
            for(int num: nums) {
                Trie curNode = root;
                int curSum = 0;
                for(int i = 31; i >= 0; i --) {
                    int curBit = (num >>> i) & 1;
                    if(curNode.children[curBit ^ 1] != null) {
                        curSum += (1 << i);
                        curNode = curNode.children[curBit ^ 1];
                    }else {
                        curNode = curNode.children[curBit];
                    }
                }
                max = Math.max(curSum, max);
            }
            return max;
        }
    



