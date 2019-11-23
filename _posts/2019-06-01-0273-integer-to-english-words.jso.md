---
layout: post
title: 273. Integer to English Words
---
### Question
Convert a non-negative integer to its english words representation. Given
input is guaranteed to be less than 231 \- 1.

 **Example 1:**

    
    
     **Input:** 123
    **Output:** "One Hundred Twenty Three"
    

**Example 2:**

    
    
    **Input:** 12345
    **Output:** "Twelve Thousand Three Hundred Forty Five"

**Example 3:**

    
    
    **Input:** 1234567
    **Output:** "One Million Two Hundred Thirty Four Thousand Five Hundred Sixty Seven"
    

**Example 4:**

    
    
    **Input:** 1234567891
    **Output:** "One Billion Two Hundred Thirty Four Million Five Hundred Sixty Seven Thousand Eight Hundred Ninety One"
    

### Solution 1
    
    
    private final String[] LESS_THAN_20 = {"", "One", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine", "Ten", "Eleven", "Twelve", "Thirteen", "Fourteen", "Fifteen", "Sixteen", "Seventeen", "Eighteen", "Nineteen"};
    private final String[] TENS = {"", "Ten", "Twenty", "Thirty", "Forty", "Fifty", "Sixty", "Seventy", "Eighty", "Ninety"};
    private final String[] THOUSANDS = {"", "Thousand", "Million", "Billion"};
    
    public String numberToWords(int num) {
        if (num == 0) return "Zero";
    
        int i = 0;
        String words = "";
        
        while (num > 0) {
            if (num % 1000 != 0)
        	    words = helper(num % 1000) +THOUSANDS[i] + " " + words;
        	num /= 1000;
        	i++;
        }
        
        return words.trim();
    }
    
    private String helper(int num) {
        if (num == 0)
            return "";
        else if (num < 20)
            return LESS_THAN_20[num] + " ";
        else if (num < 100)
            return TENS[num / 10] + " " + helper(num % 10);
        else
            return LESS_THAN_20[num / 100] + " Hundred " + helper(num % 100);
    }


### Solution 2
    
    
    public class Solution {
        private final String[] belowTen = new String[] {"", "One", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine"};
        private final String[] belowTwenty = new String[] {"Ten", "Eleven", "Twelve", "Thirteen", "Fourteen", "Fifteen", "Sixteen", "Seventeen", "Eighteen", "Nineteen"};
        private final String[] belowHundred = new String[] {"", "Ten", "Twenty", "Thirty", "Forty", "Fifty", "Sixty", "Seventy", "Eighty", "Ninety"};
        
        public String numberToWords(int num) {
            if (num == 0) return "Zero";
            return helper(num); 
        }
        
        private String helper(int num) {
            String result = new String();
            if (num < 10) result = belowTen[num];
            else if (num < 20) result = belowTwenty[num -10];
            else if (num < 100) result = belowHundred[num/10] + " " + helper(num % 10);
            else if (num < 1000) result = helper(num/100) + " Hundred " +  helper(num % 100);
            else if (num < 1000000) result = helper(num/1000) + " Thousand " +  helper(num % 1000);
            else if (num < 1000000000) result = helper(num/1000000) + " Million " +  helper(num % 1000000);
            else result = helper(num/1000000000) + " Billion " + helper(num % 1000000000);
            return result.trim();
        }
    }


### Solution 3
    
    
    def numberToWords(self, num):
        to19 = 'One Two Three Four Five Six Seven Eight Nine Ten Eleven Twelve ' \
               'Thirteen Fourteen Fifteen Sixteen Seventeen Eighteen Nineteen'.split()
        tens = 'Twenty Thirty Forty Fifty Sixty Seventy Eighty Ninety'.split()
        def words(n):
            if n < 20:
                return to19[n-1:n]
            if n < 100:
                return [tens[n/10-2]] + words(n%10)
            if n < 1000:
                return [to19[n/100-1]] + ['Hundred'] + words(n%100)
            for p, w in enumerate(('Thousand', 'Million', 'Billion'), 1):
                if n < 1000**(p+1):
                    return words(n/1000**p) + [w] + words(n%1000**p)
        return ' '.join(words(num)) or 'Zero'



