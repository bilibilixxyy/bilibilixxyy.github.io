---
layout: post
title: 1169. Largest Values From Labels
---
### Question
We have a set of items: the `i`-th item has value `values[i]` and label
`labels[i]`.

Then, we choose a subset `S` of these items, such that:

  * `|S| <= num_wanted`
  * For every label `L`, the number of items in `S` with label `L` is `<= use_limit`.

Return the largest possible sum of the subset `S`.



 **Example 1:**

    
    
     **Input:** values = [5,4,3,2,1], labels = [1,1,2,2,3], num_wanted = 3, use_limit = 1
    **Output:** 9
    **Explanation:** The subset chosen is the first, third, and fifth item.
    

**Example 2:**

    
    
    **Input:** values = [5,4,3,2,1], labels = [1,3,3,3,2], num_wanted = 3, use_limit = 2
    **Output:** 12
    **Explanation:** The subset chosen is the first, second, and third item.
    

**Example 3:**

    
    
    **Input:** values = [9,8,8,7,6], labels = [0,0,0,1,1], num_wanted = 3, use_limit = 1
    **Output:**  16
    **Explanation:** The subset chosen is the first and fourth item.
    

**Example 4:**

    
    
    **Input:** values = [9,8,8,7,6], labels = [0,0,0,1,1], num_wanted = 3, use_limit = 2
    **Output:** 24
    **Explanation:** The subset chosen is the first, second, and fourth item.
    



 **Note:**

  1. `1 <= values.length == labels.length <= 20000`
  2. `0 <= values[i], labels[i] <= 20000`
  3. `1 <= num_wanted, use_limit <= values.length`

### Solution 1
what a fucccccccccccccck!

make me verrrrrry angry!


### Solution 2
Lets understand the question  
Here, we need to find a subset where, the length of the subset will be less
than equal to num_wanted.

 **Example** : values = [5,4,3,2,1], labels = [1,1,2,2,3], num_wanted = 3,
use_limit = 1

In this example, subset size <= 3 (as num_wanted = 3)  
Listing members in each label  
Label 1 -> [5,4]  
Label 2 -> [3,2]  
Label 3 -> [1]

Now from each label we can use only 1 element because use_limit for each label
is 1.  
The question asks us to find the largest possible sum of the subset, so we
take from each label group, the largest element available.  
So in this example,  
from label 1 : we take 5,  
from label 2: we take 3  
from label 3: we take 1 (since label 1 has only one element)  
5+3+1=9

 **Example**  
We can see in given example :  
Input: values = [9,8,8,7,6], labels = [0,0,0,1,1], num_wanted = 3, use_limit =
1  
Output: 16  
Explanation: The subset chosen is the first and fourth item.  
Listing members in each label  
Label 0 -> [9,8,8]  
Label 1 -> [7,6]  
Here we are allowed to take 3 elements for the subset(num_wanted=3) but we
cannot take more than two because the use limit for each label group is
ONE(use_limit = 1) and there are only two label groups. Hence we take maximum
element from each label group i.e. - 9+7 = 16

 **Example**  
Input: values = [5,4,3,2,1], labels = [1,3,3,3,2], num_wanted = 3, use_limit =
2  
Output: 12  
Listing members in each label  
Label 1 -> [5]  
Label 3 -> [4,3,2]  
Label 2 -> [1]

Here use_limit is 2 hence we are allowed to take two elements from each label
group,  
Hence we take 5 from label 1 and [4, 3] from label 2 that satisfies our need
for the subset of length <= 3

 **Approach** :

Hashmap that will mantain the count of use of each label, where key will be
label and value will be count of that label used.  
Taking 2D array to store value and its corresponding label, Sorting it based
on values in descending order.  
Traversing each element of the array and adding to the subset and updating the
label count for that element in the hashmap.

Below is the **working solution in Java** :

    
    
     class Solution {
        public int largestValsFromLabels(int[] values, int[] labels, int num_wanted, int use_limit) {
            HashMap<Integer, Integer> hash_label = new HashMap<Integer, Integer>();
    
            // create a hashmap for maintaining the use_limit of each label group
    		for (int i : labels) {
    			hash_label.put(i, 0);
    		}
    
    		int size = values.length;
    		int[][] val_lab = new int[size][2];
    
            // creating a 2D array which has values and labels corresponding to the values
    		for (int i = 0; i < size; i++) {
    			val_lab[i][0] = values[i];
    			val_lab[i][1] = labels[i];
    		}
    
            // sorting the array in descending order based on the values from column 0
    		Arrays.sort(val_lab, new Comparator<int[]>() {
    			public int compare(final int[] entry1, final int[] entry2) {
    
    				if (entry1[0] < entry2[0])
    					return 1;
    				else
    					return -1;
    			}
    		});
    		int sum = 0;
    
    		for (int i = 0; i < size; i++) {
    			int val = val_lab[i][0];
    			int lab = val_lab[i][1];
            // if label usage less than use_limit and subset size is less than num_wanted, include array item in the subset
    			if (num_wanted > 0 && hash_label.get(lab) < use_limit) {
    
    				sum += val;
    				hash_label.put(lab, hash_label.get(lab) + 1);
    				num_wanted--;
    			}
    		}
    
    		return sum;
        }
    }
    

Correct me if I am wrong somewhere


### Solution 3
Sort all labels by value. Then, start with the largest value, greedily pick
labels. Track how many labels we have used in `m`, and do not pick that label
if we reached the limit.

    
    
    int largestValsFromLabels(vector<int>& vs, vector<int>& ls, int wanted, int limit, int res = 0) {
      multimap<int, int> s;
      unordered_map<int, int> m;
      for (auto i = 0; i < vs.size(); ++i) s.insert({vs[i], ls[i]});
      for (auto it = rbegin(s); it != rend(s) && wanted > 0; ++it) {
        if (++m[it->second] <= limit) {
          res += it->first;
          --wanted;
        }
      }
      return res;
    }
    



