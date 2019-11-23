---
layout: post
title: 1205. Defanging an IP Address
---
### Question
Given a valid (IPv4) IP `address`, return a defanged version of that IP
address.

A _defanged  IP address_ replaces every period `"."` with `"[.]"`.



 **Example 1:**

    
    
     **Input:** address = "1.1.1.1"
    **Output:** "1[.]1[.]1[.]1"
    

**Example 2:**

    
    
    **Input:** address = "255.100.50.0"
    **Output:** "255[.]100[.]50[.]0"
    



 **Constraints:**

  * The given `address` is a valid IPv4 address.

### Solution 1
这题目真是 **图样图森破 上台拿衣服**


### Solution 2
    
    
         public String defangIPaddr(String address) {
            return address.replace(".", "[.]");
        }
    
    
    
        def defangIPaddr(self, address: str) -> str:
            return address.replace('.', '[.]')
    


### Solution 3
var defangIPaddr = function(address) {  
return address.split('.').join('[.]');  
};



