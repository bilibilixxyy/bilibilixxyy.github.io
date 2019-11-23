---
layout: post
title: 1183. Statistics from a Large Sample
---
### Question
We sampled integers between `0` and `255`, and stored the results in an array
`count`:  `count[k]` is the number of integers we sampled equal to `k`.

Return the minimum, maximum, mean, median, and mode of the sample
respectively, as an array of **floating point numbers**.   The mode is
guaranteed to be unique.

 _(Recall that the median of a sample is:_

  * _The middle element, if the elements of the sample were sorted and the number of elements is odd;_
  * _The average of the middle two elements, if the elements of the sample were sorted and the number of elements is even.)_



 **Example 1:**

    
    
     **Input:** count = [0,1,3,4,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]
    **Output:** [1.00000,3.00000,2.37500,2.50000,3.00000]
    

**Example 2:**

    
    
    **Input:** count = [0,4,3,2,2,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]
    **Output:** [1.00000,4.00000,2.18182,2.00000,1.00000]
    



 **Constraints:**

  1. `count.length == 256`
  2. `1 <= sum(count) <= 10^9`
  3. The mode of the sample that count represents is unique.
  4. Answers within `10^-5` of the true value will be accepted as correct.

### Solution 1
 **Python:**

    
    
        def sampleStats(self,  count):
            n = sum(count)
            mi = next(i for i in xrange(256) if count[i]) * 1.0
            ma = next(i for i in xrange(255, -1, -1) if count[i]) * 1.0
            mean = sum(i * v for i, v in enumerate(count)) * 1.0 / n
            mode = count.index(max(count)) * 1.0
            for i in xrange(255):
                count[i + 1] += count[i]
            median1 = bisect.bisect(count, (n - 1) / 2)
            median2 = bisect.bisect(count, n / 2)
            median = (median1 + median2) / 2.0
            return [mi, ma, mean, median, mode]
    


### Solution 2
> This is my least favorite type of a problem - pretty straightforward, but
takes a lot of time to write correctly during the contest.  
> ...unless you know Python...

First, figure out how many total elements we have (`t_cnt`). Then, we can
determine the position of our median (`m1` and `m2`, `m1 == m2` if we have odd
number of elements).

Then, we can go through the array one more time and determine all statistics.

    
    
    vector<double> sampleStats(vector<int>& cnts) {
      double res[5] = { 255, 0, 0, 0, 0 };
      int t_cnt = accumulate(begin(cnts), end(cnts), 0), m_cnt = 0;
      int m1 = (t_cnt + 1) / 2, m2 = m1 + (t_cnt % 2 == 0 ? 1 : 0);
      for (auto i = 0, cnt = 0; i < cnts.size(); cnt += cnts[i++]) {
        res[0] = cnts[i] ? min((int)res[0], i) : res[0];
        res[1] = cnts[i] ? i : res[1];
        res[2] += (double)i * cnts[i] / t_cnt;
          
        if (cnt < m1 && cnt + cnts[i] >= m1) res[3] += (double)i / 2;
        if (cnt < m2 && cnt + cnts[i] >= m2) res[3] += (double)i / 2;
          
        res[4] = m_cnt < cnts[i] ? i : res[4];
        m_cnt = max(m_cnt, cnts[i]);
      }
      return vector<double>(begin(res), end(res));
    }
    


### Solution 3
First pass find minimum, maximum, mean, sum, total number, and mode of the
samples.  
Second pass find the medians.

* * *

 **Q & A:**  
 **Q1** :Can you please explain the logic of the Median part?  
 **A1:**  
If `total` is odd:

    
    
    m1 = (total + 1) / 2 = total / 2 + 1 = m2
    

They both refer to same single median item.  
if `total` is even:

    
    
    m1 = (total + 1) / 2 = total / 2, 
    m1 + 1 = m2
    

They refer to the two median items, separately.

In the for loop of the median part, `cnt` is used to accumulate `count[i]` in
order to check if we just reach the m1-th item, the first median; `if (cnt <
m1 && cnt + count[i] >= m1)`, means m1-th item is within `count[i] i's`, hence
`i` is the first median, add half of it to the `median`; do similar operation
for the m2-th item, the second median.

Therefore, no matter `total` is odd or even, we get the median value of the
`total` samples.

**Q2** : Can you explain minimum and maximum?  
**A2** :

  1. Each index of the `count` is the number in the sample.
  2. When we traverse from 0 to 255,  
a. the first positive`count[i]` is the count of minimum and `i` is the
minimum, and the `min` will NOT update once it changes from -1 to non-
negative;  
b. the last positive`count[i]` is the count of maximum and `i` is the maximum.

    
    
        public double[] sampleStats(int[] count) {
            int total = 0, mode = 0;
            double median = 0, min = -1, max = 0, avg = 0, sum = 0;
            for (int i = 0; i < 256; ++i) {
                if (count[i] > 0) {
                    total += count[i];
                    if (min < 0) min = i;
                    max = i;
                    sum += i * count[i];
                    if (count[i] > count[mode]) mode = i;
                }
            }
            avg = sum / total;
            if (total == 1) median = sum; // single element.
            int m1 = (total + 1) / 2, m2 = total / 2 + 1; // m1-th and m2-th items are medians.
            for (int i = 0, cnt = 0; total > 1 && i < 256; ++i) { // more than 1 elements.
                if (cnt < m1 && cnt + count[i] >= m1) // find m1-th item.
                    median += i / 2.0d; // add its half.
                if (cnt < m2 && cnt + count[i] >= m2) // find m2-th item.
                    median += i / 2.0d; // add its half.
                cnt += count[i];
            }
            return new double[]{min, max, avg, median, mode};
        }
    



