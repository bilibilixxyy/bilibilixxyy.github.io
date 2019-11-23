---
layout: post
title: 165. Compare Version Numbers
---
### Question
Compare two version numbers _version1_ and _version2_.  
If `_version1_ > _version2_` return `1;` if `_version1_ < _version2_` return
`-1;`otherwise return `0`.

You may assume that the version strings are non-empty and contain only digits
and the `.` character.

The `.` character does not represent a decimal point and is used to separate
number sequences.

For instance, `2.5` is not "two and a half" or "half way to version three", it
is the fifth second-level revision of the second first-level revision.

You may assume the default revision number for each level of a version number
to be `0`. For example, version number `3.4` has a revision number of `3` and
`4` for its first and second level revision number. Its third and fourth level
revision number are both `0`.



 **Example 1:**

    
    
     **Input:** _version1_ = "0.1", _version2_ = "1.1"
    **Output:** -1

**Example 2:**

    
    
    **Input:** _version1_ = "1.0.1", _version2_ = "1"
    **Output:** 1

**Example 3:**

    
    
    **Input:** _version1_ = "7.5.2.4", _version2_ = "7.5.3"
    **Output:** -1

**Example 4:**

    
    
    **Input:** _version1_ = "1.01", _version2_ = "1.001"
    **Output:** 0
    **Explanation:** Ignoring leading zeroes, both “01” and “001" represent the same number “1”

**Example 5:**

    
    
    **Input:** _version1_ = "1.0", _version2_ = "1.0.0"
    **Output:** 0
    **Explanation:** The first version number does not have a third level revision number, which means its third level revision number is default to "0"



 **Note:**

  1. Version strings are composed of numeric strings separated by dots `.` and this numeric strings **may** have leading zeroes. 
  2. Version strings do not start or end with dots, and they will not be two consecutive dots.

### Solution 1
This code assumes that next level is zero if no mo levels in shorter version
number. And than compare levels.

    
    
    public int compareVersion(String version1, String version2) {
        String[] levels1 = version1.split("\.");
        String[] levels2 = version2.split("\.");
        
        int length = Math.max(levels1.length, levels2.length);
        for (int i=0; i<length; i++) {
        	Integer v1 = i < levels1.length ? Integer.parseInt(levels1[i]) : 0;
        	Integer v2 = i < levels2.length ? Integer.parseInt(levels2[i]) : 0;
        	int compare = v1.compareTo(v2);
        	if (compare != 0) {
        		return compare;
        	}
        }
        
        return 0;
    }


### Solution 2
    
    
    int compareVersion(string version1, string version2) {
        int i = 0; 
        int j = 0;
        int n1 = version1.size(); 
        int n2 = version2.size();
        
        int num1 = 0;
        int num2 = 0;
        while(i<n1 || j<n2)
        {
            while(i<n1 && version1[i]!='.'){
                num1 = num1*10+(version1[i]-'0');
                i++;
            }
            
            while(j<n2 && version2[j]!='.'){
                num2 = num2*10+(version2[j]-'0');;
                j++;
            }
            
            if(num1>num2) return 1;
            else if(num1 < num2) return -1;
            
            num1 = 0;
            num2 = 0;
            i++;
            j++;
        }
        
        return 0;
    }


### Solution 3
Why would anyone have a version number of:  
"19.8.3.17.5.01.0.0.4.0.0.0.0.0.0.0.0.0.0.0.0.0.00.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.000000.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.000000"

This guy must be bored to hell.



