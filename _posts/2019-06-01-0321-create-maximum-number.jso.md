---
layout: post
title: 321. Create Maximum Number
---
### Question
Given two arrays of length `m` and `n` with digits `0-9` representing two
numbers. Create the maximum number of length `k <= m + n` from digits of the
two. The relative order of the digits from the same array must be preserved.
Return an array of the `k` digits.

 **Note:** You should try to optimize your time and space complexity.

 **Example 1:**

    
    
     **Input:**
    nums1 = [3, 4, 6, 5]
    nums2 = [9, 1, 2, 5, 8, 3]
    k = 5
    **Output:**
    [9, 8, 6, 5, 3]

**Example 2:**

    
    
    **Input:**
    nums1 = [6, 7]
    nums2 = [6, 0, 4]
    k = 5
    **Output:**
    [6, 7, 6, 0, 4]

**Example 3:**

    
    
    **Input:**
    nums1 = [3, 9]
    nums2 = [8, 9]
    k = 3
    **Output:**
    [9, 8, 9]
    

### Solution 1
Many of the posts have the same algorithm. In short we can first solve 2
simpler problem

  1. Create the maximum number of one array
  2. Create the maximum number of two array using all of their digits.

For an long and detailed explanation see my blog
[here](http://algobox.org/create-maximum-number/).

The algorithm is O((m+n)^3) in the worst case. It runs in 22 ms.

 **Java**

    
    
    public int[] maxNumber(int[] nums1, int[] nums2, int k) {
        int n = nums1. length;
        int m = nums2.length;
        int[] ans = new int[k];
        for (int i = Math.max(0, k - m); i <= k && i <= n; ++i) {
            int[] candidate = merge(maxArray(nums1, i), maxArray(nums2, k - i), k);
            if (greater(candidate, 0, ans, 0)) ans = candidate;
        }
        return ans;
    }
    private int[] merge(int[] nums1, int[] nums2, int k) {
        int[] ans = new int[k];
        for (int i = 0, j = 0, r = 0; r < k; ++r)
            ans[r] = greater(nums1, i, nums2, j) ? nums1[i++] : nums2[j++];
        return ans;
    }
    public boolean greater(int[] nums1, int i, int[] nums2, int j) {
        while (i < nums1.length && j < nums2.length && nums1[i] == nums2[j]) {
            i++;
            j++;
        }
        return j == nums2.length || (i < nums1.length && nums1[i] > nums2[j]);
    }
    public int[] maxArray(int[] nums, int k) {
        int n = nums.length;
        int[] ans = new int[k];
        for (int i = 0, j = 0; i < n; ++i) {
            while (n - i + j > k && j > 0 && ans[j - 1] < nums[i]) j--;
            if (j < k) ans[j++] = nums[i];
        }
        return ans;
    }
    


### Solution 2
The basic idea:

To create max number of length k from two arrays, you need to create max
number of length i from array one and max number of length k-i from array two,
then combine them together. After trying all possible i, you will get the max
number created from two arrays.

Optimization:

  1. Suppose nums1 = [3, 4, 6, 5], nums2 = [9, 1, 2, 5, 8, 3], the maximum number you can create from nums1 is [6, 5] with length 2. For nums2, it's [9, 8, 3] with length 3. Merging the two sequence, we have [9, 8, 6, 5, 3], which is the max number we can create from two arrays without length constraint. If the required length k<=5, we can simply trim the result to required length from front. For instance, if k=3, then [9, 8, 6] is the result.

  2. Suppose we need to create max number with length 2 from num = [4, 5, 3, 2, 1, 6, 0, 8]. The simple way is to use a stack, first we push 4 and have stack [4], then comes 5 > 4, we pop 4 and push 5, stack becomes [5], 3 < 5, we push 3, stack becomes [5, 3]. Now we have the required length 2, but we need to keep going through the array in case a larger number comes, 2 < 3, we discard it instead of pushing it because the stack already grows to required size 2. 1 < 3, we discard it. 6 > 3, we pop 3, since 6 > 5 and there are still elements left, we can continue to pop 5 and push 6, the stack becomes [6], since 0 < 6, we push 0, the stack becomes [6, 0], the stack grows to required length again. Since 8 > 0, we pop 0, although 8 > 6, we can't continue to pop 6 since there is only one number, which is 8, left, if we pop 6 and push 8, we can't get to length 2, so we push 8 directly, the stack becomes [6, 8].

  3. In the basic idea, we mentioned trying all possible length i. If we create max number for different i from scratch each time, that would be a waste of time. Suppose num = [4, 9, 3, 2, 1, 8, 7, 6], we need to create max number with length from 1 to 8. For i==8, result is the original array. For i==7, we need to drop 1 number from array, since 9 > 4, we drop 4, the result is [9, 3, 2, 1, 8, 7, 6]. For i==6, we need to drop 1 more number, 3 < 9, skip, 2 < 3, skip, 1 < 2, skip, 8 > 1, we drop 1, the result is [9, 3, 2, 8, 7, 6]. For i==5, we need to drop 1 more, but this time, we needn't check from beginning, during last scan, we already know [9, 3, 2] is monotonically non-increasing, so we check 8 directly, since 8 > 2, we drop 2, the result is [9, 3, 8, 7, 6]. For i==4, we start with 8, 8 > 3, we drop 3, the result is [9, 8, 7, 6]. For i==3, we start with 8, 8 < 9, skip, 7 < 8, skip, 6 < 7, skip, by now, we've got maximum number we can create from num without length constraint. So from now on, we can drop a number from the end each time. The result is [9, 8, 7], For i==2, we drop last number 7 and have [9, 8]. For i==1, we drop last number 8 and have [9].

#

    
    
    class Solution {
    public:
        #define MIN(a,b) (a<b?a:b)
        #define MAX(a,b) (a>b?a:b)
        // create max number of length t from single non-empty vector
        void getMax(int* num, int& len, int* result, int& t, int& sortedLen)
        {
        	int n, top = 0;
        	result[0] = num[0];
        	const int need2drop = len - t;
        	for (int i = 1; i < len; ++i){
        		n = num[i];
        		while (top >= 0 && result[top] < n && (i - top) <= need2drop) --top; // i - top means already dropped i - top numbers
        		if (i - top > need2drop){
        			sortedLen = MAX(1,top);
        			while (++top < t) result[top] = num[i++];
        			return;
        		}
        		if (++top < t) result[top] = n;
        		else top = t - 1;
        	}
        }
        // create max number of different length from single vector
        void dp(int *num, int len, int&sortedLen, int& minL, int& maxL, int *res, int &k){
        	int  j, *head, *prevhead = res;
        	const int soi = sizeof(int);
        	getMax(num, len, res, maxL,sortedLen);
        	for (int l = maxL; l > MAX(minL,1); --l){
        		head = prevhead + k;
        		memcpy(head, prevhead, l*soi);
        		for (j = sortedLen; j < l; ++j){
        			if (head[j] > head[j - 1]){
        				sortedLen = MAX(1, j - 1);
        				memcpy(head + j - 1, prevhead + j, soi*(l - j));
        				break;
        			}
        		}
        		if (j == l) sortedLen = l;
        		prevhead = head;
        	}
        }
        // merge max number created from single vector
        void merge(int* num1,int len1,int* num2,int len2,int* result,int& resSize){
        	int i = 0, j = 0, k = 0;
        	while (i < resSize){
        		if (j < len1 && k < len2){
        			if (num1[j] > num2[k])
        				result[i++] = num1[j++];
        			else if (num1[j] < num2[k])
        				result[i++] = num2[k++];
        			else{
        				int remaining1 = len1 - j, remaining2 = len2 - k, tmp = num1[j];
        				int flag = memcmp(num1 + j, num2 + k, sizeof(int) * MIN(remaining1, remaining2));
        				flag = (flag == 0 ? (remaining1>remaining2 ? 1 : -1) : flag);
        				int * num = flag > 0 ? num1 : num2;
        				int & cnt = flag > 0 ? j : k;
        				int len = flag > 0 ? len1 : len2;
        				while (num[cnt]==tmp && cnt < len && i<resSize) result[i++] = num[cnt++];
        			}
        		}
        		else if (j < len1) result[i++] = num1[j++];
        		else result[i++] = num2[k++];
        	}
        }
        
        vector<int> maxNumber(vector<int>& nums1, vector<int>& nums2, int k){
        	int soi = sizeof(int), len1 = nums1.size(), len2 = nums2.size(), step = k*soi;
        	int minL1 = MAX(0, k - len2), maxL1 = MIN(k, len1), minL2 = k - maxL1, maxL2 = k - minL1, range = maxL1 - minL1 + 1;
        	int * res = new int[range * k * 2 + 2 * k], *dp1 = res + k, *dp2 = res + range*k+k, *tmp=res+range*2*k+k;
        	memset(res, 0, step);
        	int sortedLen1 = 1, sortedLen2 = 1;
        	if (len1 == 0 && len2 > 0) getMax(&nums2[0], len2, res, k, sortedLen2);
        	else if (len1 > 0 && len2 == 0) getMax(&nums1[0], len1, res, k, sortedLen2);
        	else if (len1 > 0 && len2 > 0){
        		dp(&nums1[0], len1, sortedLen1, minL1, maxL1, dp1,k);
        		dp(&nums2[0], len2, sortedLen2, minL2, maxL2, dp2,k);
        		if (sortedLen1 + sortedLen2 > k){
        			merge(dp1 + k*(maxL1 - sortedLen1), sortedLen1, dp2 + k*(maxL2 - sortedLen2), sortedLen2, tmp, k);
        			vector<int> resv(tmp, tmp + k);
        			delete[] res;
        			return resv;
        		}
        		for (int i = minL1; i <= maxL1; ++i){
        			merge(dp1+k*(maxL1-i), i, dp2+k*(maxL2-k+i), (k-i), tmp,k);
        			if (memcmp(res, tmp, step) < 0) memcpy(res, tmp, step);
        		}
        	}
        	vector<int> resv(res, res + k);
        	delete[] res;
        	return resv;
        }
    };


### Solution 3
 **Python**

    
    
    def maxNumber(self, nums1, nums2, k):
    
        def prep(nums, k):
             drop = len(nums) - k
            out = []
            for num in nums:
                while drop and out and out[-1] < num:
                    out.pop()
                    drop -= 1
                out.append(num)
            return out[:k]
    
        def merge(a, b):
            return [max(a, b).pop(0) for _ in a+b]
    
        return max(merge(prep(nums1, i), prep(nums2, k-i))
                   for i in range(k+1)
                   if i <= len(nums1) and k-i <= len(nums2))
    

Solved it on my own but now I see others already posted this idea. Oh well, at
least it's short, particularly my `merge` function.

The last two lines can be combined, but I find it rather ugly and not worth
it:  
`for i in range(max(k-len(nums2), 0), min(k, len(nums1))+1))`

* * *

**Ruby**

    
    
    def prep(nums, k)
      drop = nums. size - k
      out = [9]
      nums.each do |num|
        while drop > 0 && out[-1] < num
          out.pop
          drop -= 1
        end
        out << num
      end
      out[1..k]
    end
    
    def max_number(nums1, nums2, k)
      ([k-nums2.size, 0].max .. [nums1.size, k].min).map { |k1|
        parts = [prep(nums1, k1), prep(nums2, k-k1)]
        (1..k).map { parts.max.shift }
      }.max
    end
    

* * *

**C++**

Translated it to C++ as well now. Not as short anymore, but still decent. And
C++ allows different functions with the same name, so I chose to do that here
to show how nicely the `maxNumber(nums1, nums2, k)` problem can be based on
the problems `maxNumber(nums, k)` and `maxNumber(nums1, nums2)`, which would
make fine problems on their own.

    
    
    vector<int> maxNumber(vector<int>& nums1, vector<int>& nums2, int k) {
        int n1 = nums1.size(), n2 = nums2.size();
        vector<int> best;
        for (int k1=max(k-n2, 0); k1<=min(k, n1); ++k1)
            best = max(best, maxNumber(maxNumber(nums1, k1),
                                       maxNumber(nums2, k-k1)));
        return best;
    }
    
    vector<int> maxNumber(vector<int> nums, int k) {
        int drop = nums.size() - k;
        vector<int> out;
        for (int num : nums) {
            while (drop && out.size() && out.back() < num) {
                out.pop_back();
                drop--;
            }
            out.push_back(num);
        }
        out.resize(k);
        return out;
    }
    
    vector<int> maxNumber(vector<int> nums1, vector<int> nums2) {
        vector<int> out;
        while (nums1.size() + nums2.size()) {
            vector<int>& now = nums1 > nums2 ? nums1 : nums2;
            out.push_back(now[0]);
            now.erase(now.begin());
        }
        return out;
    }
    

An alternative for `maxNumber(nums1, nums2)`:

    
    
    vector<int> maxNumber(vector<int> nums1, vector<int> nums2) {
        vector<int> out;
        auto i1 = nums1.begin(), end1 = nums1.end();
        auto i2 = nums2.begin(), end2 = nums2.end();
        while (i1 != end1 || i2 != end2)
            out.push_back(lexicographical_compare(i1, end1, i2, end2) ? *i2++ : *i1++);
        return out;
    }



