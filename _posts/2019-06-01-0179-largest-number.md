---
layout: post
title: 179. Largest Number
---
### Question
Given a list of non negative integers, arrange them such that they form the
largest number.

 **Example 1:**

    
    
     **Input:** [10,2]
    **Output:** "210"

**Example 2:**

    
    
    **Input:** [3,30,34,5,9]
    **Output:** "9534330"
    

**Note:** The result may be very large, so you need to return a string instead
of an integer.

### Solution 1
The idea here is basically implement a String comparator to decide which
String should come first during concatenation. Because when you have 2 numbers
(let's convert them into String), you'll face only 2 cases:  
For example:

    
    
    String s1 = "9";
    String s2 = "31";
    
    String case1 =  s1 + s2; // 931
    String case2 = s2 + s1; // 319
    
    

Apparently, case1 is greater than case2 in terms of value.  
So, we should always put s1 in front of s2.

I have received many good suggestions from you in this discussion. Below is
the modified version of codes based on your suggestions:

    
    
    public class Solution {
         public String largestNumber(int[] num) {
    		if(num == null || num.length == 0)
    		    return "";
    		
    		// Convert int array to String array, so we can sort later on
    		String[] s_num = new String[num.length];
    		for(int i = 0; i < num.length; i++)
    		    s_num[i] = String.valueOf(num[i]);
    			
    		// Comparator to decide which string should come first in concatenation
    		Comparator<String> comp = new Comparator<String>(){
    		    @Override
    		    public int compare(String str1, String str2){
    		        String s1 = str1 + str2;
    			String s2 = str2 + str1;
    			return s2.compareTo(s1); // reverse order here, so we can do append() later
    		    }
    	        };
    		
    		Arrays.sort(s_num, comp);
                    // An extreme edge case by lc, say you have only a bunch of 0 in your int array
                    if(s_num[0].charAt(0) == '0')
                        return "0";
                
    		StringBuilder sb = new StringBuilder();
    		for(String s: s_num)
    	            sb.append(s);
    		
    		return sb.toString();
    		
    	}
    }
    

In terms of Time and Space Complexity:  
Let's assume:  
the length of input array is n,  
average length of Strings in s_num is k,  
Then, compare 2 strings will take O(k).  
Sorting will take O(nlgn)  
Appending to StringBuilder takes O(n).  
So total will be O(n _k_ lgnk) + O(n) = O(n _k_ lgnk).

Space is pretty straight forward: O(n).


### Solution 2
    
    
    class Solution {
    public:
        string largestNumber(vector<int> &num) {
            vector<string> arr;
            for(auto i:num)
                arr.push_back(to_string(i));
            sort(begin(arr), end(arr), [](string &s1, string &s2){ return s1+s2>s2+s1; });
            string res;
            for(auto s:arr)
                res+=s;
            while(res[0]=='0' && res.length()>1)
                res.erase(0,1);
            return  res;
        }
    };


### Solution 3
The logic is pretty straightforward. Just compare number by convert it to
string.

Thanks for Java 8, it makes code beautiful.

Java:

    
    
    public class Solution {
        public String largestNumber(int[] num) {
            String[] array = Arrays.stream(num).mapToObj(String::valueOf).toArray(String[]::new);
            Arrays.sort(array, (String s1, String s2) -> (s2 + s1).compareTo(s1 + s2));
            return Arrays.stream(array).reduce((x, y) -> x.equals("0") ? y : x + y).get();
        }
    }
    

Python:

    
    
    class Solution:
        # @param num, a list of integers
        # @return a string
        def largestNumber(self, num):
            num = [str(x) for x in num]
            num.sort(cmp=lambda x, y: cmp(y+x, x+y))
            return ''.join(num).lstrip('0') or '0'



