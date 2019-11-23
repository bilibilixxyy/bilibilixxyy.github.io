---
layout: post
title: 1081. Video Stitching
---
### Question
You are given a series of video clips from a sporting event that lasted `T`
seconds.  These video clips can be overlapping with each other and have varied
lengths.

Each video clip `clips[i]` is an interval: it starts at time `clips[i][0]` and
ends at time `clips[i][1]`.  We can cut these clips into segments freely: for
example, a clip `[0, 7]` can be cut into segments `[0, 1] + [1, 3] + [3, 7]`.

Return the minimum number of clips needed so that we can cut the clips into
segments that cover the entire sporting event (`[0, T]`).  If the task is
impossible, return `-1`.



 **Example 1:**

    
    
     **Input:** clips = [[0,2],[4,6],[8,10],[1,9],[1,5],[5,9]], T = 10
    **Output:** 3
    **Explanation:**
    We take the clips [0,2], [8,10], [1,9]; a total of 3 clips.
    Then, we can reconstruct the sporting event as follows:
    We cut [1,9] into segments [1,2] + [2,8] + [8,9].
    Now we have segments [0,2] + [2,8] + [8,10] which cover the sporting event [0, 10].
    

**Example 2:**

    
    
    **Input:** clips = [[0,1],[1,2]], T = 5
    **Output:** -1
    **Explanation:**
    We can't cover [0,5] with only [0,1] and [0,2].
    

**Example 3:**

    
    
    **Input:** clips = [[0,1],[6,8],[0,2],[5,6],[0,4],[0,3],[6,7],[1,3],[4,7],[1,4],[2,5],[2,6],[3,4],[4,5],[5,7],[6,9]], T = 9
    **Output:** 3
    **Explanation:**
    We can take clips [0,4], [4,7], and [6,9].
    

**Example 4:**

    
    
    **Input:** clips = [[0,4],[2,8]], T = 5
    **Output:** 2
    **Explanation:**
    Notice you can have extra video after the event ends.
    



 **Note:**

  1. `1 <= clips.length <= 100`
  2. `0 <= clips[i][0], clips[i][1] <= 100`
  3. `0 <= T <= 100`

### Solution 1
## Solution 1: Sort

Time `O(NlogN)`, Space `O(1)`  
 **Java**

    
    
         public int videoStitching(int[][] clips, int T) {
            int res = 0;
            Arrays.sort(clips, (a,b) ->  a[0] - b[0]);
            for (int i = 0, st = 0, end = 0; st < T; st = end, ++res) {
                for (; i < clips.length && clips[i][0] <= st; ++i)
                    end = Math.max(end, clips[i][1]);
                if (st == end) return -1;
            }
            return res;
        }
    

**C++**

    
    
         int videoStitching(vector<vector<int>>& clips, int T) {
            sort(begin(clips), end(clips));
            int res = 0;
            for (auto i = 0, st = 0, end = 0; st < T; st = end, ++res) {
                for (; i < clips.size() && clips[i][0] <= st; ++i)
                    end = max(end, clips[i][1]);
                if (st == end) return -1;
            }
            return res;
        }
    

**Python:**

    
    
        def videoStitching(self, clips, T):
             end, end2, res = -1, 0, 0
            for i, j in sorted(clips):
                if end2 >= T or i > end2:
                    break
                elif end < i <= end2:
                    res, end = res + 1, end2
                end2 = max(end2, j)
            return res if end2 >= T else -1
    

  

## Solution 2: Sort + DP

Sort clips first.  
Then for each clip, update `dp[clip[0]] ~ dp[clip[1]]`.

Time `O(NlogN + NT)`, Space `O(T)`

**C++**

    
    
         int videoStitching(vector<vector<int>>& clips, int T) {
            sort(clips.begin(), clips.end());
            vector<int> dp(101, 101);
            dp[0] = 0;
            for (auto& c : clips)
                for (int i = c[0] + 1; i <= c[1]; i++)
                    dp[i] = min(dp[i], dp[c[0]] + 1);
            return dp[T] >= 100 ? -1 : dp[T];
        }
    

  

## Solution 3: DP

Loop on i form `0` to `T`,  
loop on all `clips`,  
If `clip[0] <= i <= clip[1]`, we update `dp[i]`

Time `O(NT)`, Space `O(T)`

**Java**

    
    
         public int videoStitching(int[][] clips, int T) {
            int[] dp = new int[T + 1];
            Arrays.fill(dp, T + 1);
            dp[0] = 0;
            for (int i = 0; i <= T && dp[i - 1] < T; i++) {
                for (int[] c : clips) {
                    if (c[0] <= i && i <= c[1])
                        dp[i] = Math.min(dp[i], dp[c[0]] + 1);
                }
            }
            return dp[T] == T + 1 ? -1 : dp[T];
        }
    


### Solution 2
[https://www.youtube.com/watch?v=sO1OVZFA_fE&feature=youtu.be](https://www.youtube.com/watch?v=sO1OVZFA_fE&feature=youtu.be)

After weeks of bad performance, I finally gain some confidence this time.
Thanks


### Solution 3
# Intuition

We track our current stitching position (`st`). For each iteration, we check
all overlapping clips, and pick the one that advances our stitching position
the furthest.

# Solution

We sort our clips by the starting point. Since clips are sorted, we need to
only analyze each clip once. For each round, we check all overlapping clips
(`clips[i][0] <= st`) and advance our stitching position as far as we can
(`end = max(end, clips[i][1])`).

Return `-1` if we cannot advance our stitching position (`st == end`).

    
    
    int videoStitching(vector<vector<int>>& clips, int T, int res = 0) {
      sort(begin(clips), end(clips));
      for (auto i = 0, st = 0, end = 0; st < T; st = end, ++res) {
        while (i < clips.size() && clips[i][0] <= st) end = max(end, clips[i++][1]);
        if (st == end) return -1;
      }
      return res;
    }
    

Java version:

    
    
    public int videoStitching(int[][] clips, int T) {
      int res = 0;
      Arrays.sort(clips, new Comparator<int[]>() {
        public int compare(int[] a, int[] b) { return a[0] - b[0]; }
      });
      for (int i = 0, st = 0, end = 0; st < T; st = end, ++res) {
        for (; i < clips.length && clips[i][0] <= st; ++i)
          end = Math.max(end, clips[i][1]);
        if (st == end) return -1;
      }
      return res;
    }
    

# Complexity Analysis

Runtime: _O(n log n)_ , where n is the number of clips. We sort all clips, and
then processing each clip only once.  
Memory: _O(1)_.



