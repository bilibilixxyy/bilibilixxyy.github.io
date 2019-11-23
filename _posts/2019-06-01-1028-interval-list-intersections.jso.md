---
layout: post
title: 1028. Interval List Intersections
---
### Question
Given two lists of **closed** intervals, each list of intervals is pairwise
disjoint and in sorted order.

Return the intersection of these two interval lists.

 _(Formally, a closed interval`[a, b]` (with `a <= b`) denotes the set of real
numbers `x` with `a <= x <= b`.  The intersection of two closed intervals is a
set of real numbers that is either empty, or can be represented as a closed
interval.  For example, the intersection of [1, 3] and [2, 4] is [2, 3].)_



 **Example 1:**

 **![](https://assets.leetcode.com/uploads/2019/01/30/interval1.png)**

    
    
     **Input:** A = [[0,2],[5,10],[13,23],[24,25]], B = [[1,5],[8,12],[15,24],[25,26]]
    **Output:** [[1,2],[5,5],[8,10],[15,23],[24,24],[25,25]]
    **Reminder:** The inputs and the desired output are lists of Interval objects, and not arrays or lists.
    



 **Note:**

  1. `0 <= A.length < 1000`
  2. `0 <= B.length < 1000`
  3. `0 <= A[i].start, A[i].end, B[i].start, B[i].end < 10^9`

 **NOTE:**  input types have been changed on April 15, 2019. Please reset to
default code definition to get new method signature.

### Solution 1
Like in the "merge" phase of the merge sort, we use two pointers to combine
sorted sequences. Nothing tricky here - just check all conditions carefully.

    
    
    vector<Interval> intervalIntersection(vector<Interval>& A, vector<Interval>& B) {
      vector<Interval> res;
      for (auto i = 0, j = 0; i < A.size() && j < B.size(); ) {
        if (A[i].end < B[j].start) ++i;
        else if (B[j].end < A[i].start) ++j;
        else {
          res.push_back({ max(A[i].start, B[j].start), min(A[i].end, B[j].end) });
          if (A[i].end < B[j].end) ++i;
          else ++j;
        }
      }
      return res;
    }
    


### Solution 2
    
    
    class Solution {
        public Interval[] intervalIntersection(Interval[] A, Interval[] B) {
            if (A == null || A.length == 0 || B == null || B.length == 0) {
                return new Interval[] {};
            }
            
            int m = A.length, n = B.length;
            int i = 0, j = 0;
            List<Interval> res = new ArrayList<>();
            while (i < m && j < n) {
                Interval a = A[i];
                Interval b = B[j];
    
                // find the overlap... if there is any...
                int startMax = Math.max(a.start, b.start);
                int endMin = Math.min(a.end, b.end);
                
                if (endMin >= startMax) {
                    res.add(new Interval(startMax, endMin));
                }
                
                //update the pointer with smaller end value...
                if (a.end == endMin) { i++; }
                if (b.end == endMin) { j++; }
            }
            
            //thanks EOAndersson for the concice expression of converting a list to an array
    		//thanks Sithis for the explaination of using toArray (new T[0]) intead fo toArray newT[size])
            return res.toArray(new Interval[0]);
        }
    }
            
    //        int size = res.size();
    //        Interval[] list = new Interval[size];
    //        for (int k = 0; k < size; k++) {
    //            list[k] = res.get(k);
    //        }
    //        return list;   
    
    


### Solution 3
We use 2 pointers i and j. T  
he steps are:

  1. if no overlap: increment the index of the lesser interval
  2. if overlap: overlap interval = bigger start index + lower end index.  
increment the index of the lesser interval

    
    
    def intervalIntersection(self, a: 'List[Interval]', b: 'List[Interval]') -> 'List[Interval]':
            i,j, res = 0, 0, []
            while i < len(a) and j < len(b):
                if a[i].end < b[j].start:
                    i += 1
                elif b[j].end < a[i].start:
                    j += 1
                else:
                    res.append(Interval(max(a[i].start, b[j].start), min(a[i].end, b[j].end))) 
                    if a[i].end > b[j].end:
                        j += 1
                    else:
                        i += 1
            return res
    



