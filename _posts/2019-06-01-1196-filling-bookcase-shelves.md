---
layout: post
title: 1196. Filling Bookcase Shelves
---
### Question
We have a sequence of `books`: the `i`-th book has thickness `books[i][0]` and
height `books[i][1]`.

We want to place these books **in order**  onto bookcase shelves that have
total width `shelf_width`.

We choose some of the books to place on this shelf (such that the sum of their
thickness is `<= shelf_width`), then build another level of shelf of the
bookcase so that the total height of the bookcase has increased by the maximum
height of the books we just put down.  We repeat this process until there are
no more books to place.

Note again that at each step of the above process, _the order of the books we
place is the same order as the given sequence of books_.   For example, if we
have an ordered list of 5 books, we might place the first and second book onto
the first shelf, the third book on the second shelf, and the fourth and fifth
book on the last shelf.

Return the minimum possible height that the total bookshelf can be after
placing shelves in this manner.



 **Example 1:**

![](https://assets.leetcode.com/uploads/2019/06/24/shelves.png)

    
    
     **Input:** books = [[1,1],[2,3],[2,3],[1,1],[1,1],[1,1],[1,2]], shelf_width = 4
    **Output:** 6
    **Explanation:**
    The sum of the heights of the 3 shelves are 1 + 3 + 2 = 6.
    Notice that book number 2 does not have to be on the first shelf.
    



 **Constraints:**

  * `1 <= books.length <= 1000`
  * `1 <= books[i][0] <= shelf_width <= 1000`
  * `1 <= books[i][1] <= 1000`

### Solution 1
`dp[i]`: the min height for placing first books `i - 1` on shelves  
For `dp[i+1]`,  
either place book `i` on a new shelve => `dp[i] + height[i]`,  
or grab previous books together with book `i` and move to next level together,
utlitzing the sub problem `dp[j]` => `min(dp[j] + max(height[j+1] ..
height[i]))`, where `sum(width[j+1] + ... + sum(width[i]) <= shelve_width`

    
    
    class Solution {
        public int minHeightShelves(int[][] books, int shelf_width) {
            int[] dp = new int[books.length + 1];
            
            dp[0] = 0;
            
            for (int i = 1; i <= books.length; ++i) {
                int width = books[i-1][0];
                int height = books[i-1][1];
                dp[i] = dp[i-1] + height;
                for (int j = i - 1; j > 0 && width + books[j-1][0] <= shelf_width; --j) {
                    height = Math.max(height, books[j-1][1]);
                    width += books[j-1][0];
                    dp[i] = Math.min(dp[i], dp[j-1] + height);
                }
            }
            return dp[books.length];
        }
    }
    


### Solution 2
题意： 给`n`本书，每本书有一个厚度和高度，摆在书架上。  
书架每一层最大宽度是`shelf_width`，摆不下时可以摆在下一层。  
每层的高度为当前层所有书的最大高度，问书的总高度最小时多少。

思路：经典的动态规划题。  
定义`dp[i]`为前`i`本书能够到达的最小高度。  
则对于第`i+1`本书，有若干选择。  
如自己单独一层，则状态转移为`dp[i+1] = dp[i] + h[i+1]`  
如果和前面的书放在一起，则状态转移方程式`dp[i+1] = min(dp[j] + max[h[j+1] ~ h[i+1]))`,
其中需要满足`sum(w[j+1] ~ w[i+1]) <=
shelf_width`，含义是前`j`本书组成若干层，第`j+1`到第`i+1`本书组成一层。  
对于这些选择，取最小值。

    
    
    class Solution {
    public:
        int minHeightShelves(vector<vector<int>>& books, int shelf_width) {
            vector<int> dp(books.size()+1, 1000*1000);
            dp[0] = 0;
            for(int i=1;i<=books.size(); i++){
                auto& b = books[i-1];
                int w = b[0];
                int h = b[1];
                dp[i] =  dp[i-1] + h;
                for(int j=i-1;j>0;j--){
                    w += books[j-1][0];
                    h = max(h, books[j-1][1]);
                    if(w > shelf_width)break;
                    dp[i] = min(dp[i], dp[j-1] + h);
                }
            }
            return dp[books.size()];
        }
    };
    


### Solution 3
# Intuition

For each book, we need to explore two cases: add to the shelf and start a new
shelf. We can use DFS and use memoisation to prune (you'll get TLE without the
memoisation).

# Solution

The basic DFS logic is straightforward, but for me it was tricky to come up
with a memoisation strategy: for each book (`i`), and for each position on the
shelf (`w`) in the shelf, store the minimum height for the remaining books.

In other words, the position `m[i][w]` contains the minimum height for books
`[i...n]`, so we do not need to care what the current height is.

    
    
    int m[1001][1001] = {};
    int minHeightShelves(vector<vector<int>>& b, int max_w, int i = 0, int w = 0, int h = 0) {
      if (i >= b.size()) return h;
      if (m[i][w] != 0) return m[i][w];
      return m[i][w] = min(h + minHeightShelves(b, max_w, i + 1, b[i][0], b[i][1]),
        w + b[i][0] > max_w ? INT_MAX : minHeightShelves(b, max_w, i + 1, w + b[i][0], max(h, b[i][1])));
    }
    

# Complexity Analysis

Runtime:

  * Without memoisation, _O(2 ^ n)_ , where n is the number of books.
  * With memoisation, _O(n * m)_ , where m is the maximum width.

Memory: _O(n * m)_ , where m is the maximum width.

# Bottom-Up Solution

    
    
    int minHeightShelves(vector<vector<int>>& b, int sh_w) {
      vector<int> dp(b.size() + 1);
      for (int p = 1; p <= b.size(); ++p) {
        for (int i = p, w = 0, h = 0; i > 0 && w + b[i - 1][0] <= sh_w; --i) {
          w += b[i - 1][0];
          h = max(h, b[i - 1][1]);
          dp[p] = min(dp[p] == 0 ? INT_MAX : dp[p], dp[i - 1] + h);
        }
      }
      return dp.back();
    }
    



