---
layout: post
title: 986. Largest Time for Given Digits
---
### Question
Given an array of 4 digits, return the largest 24 hour time that can be made.

The smallest 24 hour time is 00:00, and the largest is 23:59.  Starting from
00:00, a time is larger if more time has elapsed since midnight.

Return the answer as a string of length 5.  If no valid time can be made,
return an empty string.



 **Example 1:**

    
    
     **Input:** [1,2,3,4]
    **Output:** "23:41"
    

**Example 2:**

    
    
    **Input:** [5,5,5,5]
    **Output:** ""
    



 **Note:**

  1. `A.length == 4`
  2. `0 <= A[i] <= 9`

### Solution 1
Check All Valid Permutation, O(24)

    
    
        def largestTimeFromDigits(self, A):
            return max(["%d%d:%d%d" % t for t in itertools.permutations(A) if t[:2] < (2, 4) and t[2] < 6] or [""])
    


### Solution 2
The inner most loop at most iterates 4 * 4 * 4 = 64 times.  
A[i], A[j], A[k], & A[l] are the 4 elements of A, where i, j, k & l are the
permutation of 0, 1, 2, & 3\. Therefore, since i + j + k + l = 0 + 1 + 2 + 3 =
6, we have l = 6 - i - j - k.

    
    
        public String largestTimeFromDigits(int[] A) {
            String ans = "";
            for (int i = 0; i < 4; ++i) {
                for (int j = 0; j < 4; ++j) {
                    for (int k = 0; k < 4; ++k) {
                        if (i == j || i == k || j == k) continue; // avoid duplicate among i, j & k.
                        String h = "" + A[i] + A[j], m = "" + A[k] + A[6 - i - j - k], t = h + ":" + m; // hour, minutes, & time.
                        if (h.compareTo("24") < 0 && m.compareTo("60") < 0 && ans.compareTo(t) < 0) ans = t; // hour < 24; minute < 60; update result.
                    }
                }
            }
            return ans;
        }
    


### Solution 3
    
    
        /**
         * 4个数字全排列总共有24种可能，判断每一种可能是否能组成合法时间值，如果能，再和当前保存的最大值进行比较；
         * 最大值是一个int值，用来表示分钟数；
         */
        public String largestTimeFromDigits(int[] array) {
            int largestTime = -1;
    
            // 暴力枚举出每种可能
            for (int i = 0; i < 4; i++) {
                for (int j = 0; j < 4; j++) {
                    if (j != i) {
                        for (int k = 0; k < 4; k++) {
                            if (k != i && k != j) {
                                // 0,1,2,3 总和为6，故剩下的index为6-
                                int l = 6 - i - j - k;
    
                                int result = largestTimeHelper(array[i], array[j], array[k], array[l]);
    
                                largestTime = Math.max(result, largestTime);
                            }
                        }
                    }
                }
            }
            if (largestTime==-1){
                return "";
            }
            return String.format("%2d:%2d",largestTime/60,largestTime%60);
        }
    
        /**
         * 判断输入的四个数字按照输入顺序组成的时间是否合法，如果合法，返回分钟数；
         */
        public int largestTimeHelper(int a, int b, int c, int d) {
            int hours = a * 10 + b;
            int min = c * 10 + d;
    
            if (hours < 24 && min < 60) {
                // 返回分钟数
                return hours * 60 + min;
            }
            return -1;
        }
    
    



