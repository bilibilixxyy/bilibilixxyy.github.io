---
layout: post
title: 965. Unique Email Addresses
---
### Question
Every email consists of a local name and a domain name, separated by the @
sign.

For example, in `alice@leetcode.com`, `alice` is the local name, and
`leetcode.com` is the domain name.

Besides lowercase letters, these emails may contain `'.'`s or `'+'`s.

If you add periods (`'.'`) between some characters in the **local name** part
of an email address, mail sent there will be forwarded to the same address
without dots in the local name.   For example, `"alice.z@leetcode.com"` and
`"alicez@leetcode.com"` forward to the same email address.  (Note that this
rule does not apply for domain names.)

If you add a plus (`'+'`) in the **local name** , everything after the first
plus sign will be  **ignored**. This allows certain emails to be filtered, for
example  `m.y+name@email.com` will be forwarded to `my@email.com`.  (Again,
this rule does not apply for domain names.)

It is possible to use both of these rules at the same time.

Given a list of `emails`, we send one email to each address in the list.  How
many different addresses actually receive mails?



 **Example 1:**

    
    
     **Input:** ["test.email+alex@leetcode.com","test.e.mail+bob.cathy@leetcode.com","testemail+david@lee.tcode.com"]
    **Output:** 2
    **Explanation:**  "testemail@leetcode.com" and "testemail@lee.tcode.com" actually receive mails
    



 **Note:**

  * `1 <= emails[i].length <= 100`
  * `1 <= emails.length <= 100`
  * Each `emails[i]` contains exactly one `'@'` character.
  * All local and domain names are non-empty.
  * Local names do not start with a `'+'` character.

### Solution 1
    
    
         public int numUniqueEmails(String[] emails) {
            Set<String> normalized = new HashSet<>(); // used to save simplified email address, cost O(n) sapce.
            for (String email : emails) {
                String[] parts = email.split("@"); // split into local and domain parts.
                String[] local = parts[0].split("\+"); // split local by '+'.
                normalized.add(local[0].replace(".", "") + "@" + parts[1]); // remove all '.', and concatenate '@' and domain.        
            }
            return normalized.size();
        }
    

**Analysis**  
Let `n` be the total characters in the input array `emails`. The HashSet
`normalized` and the for loop both cost `O(n)`, in terms of space and time,
respectively.

 **Time & space: O(n).**

# \--------------------

 **Q & A**

 **Q1** : What is Java metacharacter?  
 **A1** : A metacharacter — a character with special meaning interpreted by
the Java regular expression engine / matcher.  
<https://en.wikipedia.org/wiki/Metacharacter>  
<https://docs.oracle.com/javase/tutorial/essential/regex/literals.html>.

 **Q2** : Why does Java regular expression use `\\`, instead of `\`, to escape
metacharacter such as `+, ., *`, etc?

 **A2** : I guess the reason is that the backslash character is an escape
character in Java String literals already.

Correct me if I am wrong.


### Solution 2
    
    
    class Solution:
        def numUniqueEmails(self, emails):
            """
            :type emails: List[str]
            :rtype: int
            """
            email_set = set()
            for email in emails:
                local_name,domain_name = email.split("@")
                local_name ="".join(local_name.split('+')[0].split('.'))
                email = local_name +'@' + domain_name
                email_set.add(email)
            return len(email_set)
    


### Solution 3
Please add this or something similar to the test cases:  
 _["a@leetcode.com","b@leetcode.com","c@leetcode.com"]_

Many of the fastest accepted solutions look like this:

    
    
    Set<String> seen = new HashSet();
            for (String email: emails) {
                int i = email.indexOf('@');
                String rest = email.substring(i);
                seen.add(rest);
            }
            return seen.size();
    

The fact that this is an accepted solution shows a gap in the test cases.
E.g., the proposed test case fails with the above code even though the code is
accepted.



