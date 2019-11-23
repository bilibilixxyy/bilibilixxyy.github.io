---
layout: post
title: 974. Reorder Log Files
---
### Question
You have an array of `logs`.  Each log is a space delimited string of words.

For each log, the first word in each log is an alphanumeric _identifier_.
Then, either:

  * Each word after the identifier will consist only of lowercase letters, or;
  * Each word after the identifier will consist only of digits.

We will call these two varieties of logs _letter-logs_ and _digit-logs_.   It
is guaranteed that each log has at least one word after its identifier.

Reorder the logs so that all of the letter-logs come before any digit-log.
The letter-logs are ordered lexicographically ignoring identifier, with the
identifier used in case of ties.  The digit-logs should be put in their
original order.

Return the final order of the logs.



 **Example 1:**

    
    
     **Input:** ["a1 9 2 3 1","g1 act car","zo4 4 7","ab1 off key dog","a8 act zoo"]
    **Output:** ["g1 act car","a8 act zoo","ab1 off key dog","a1 9 2 3 1","zo4 4 7"]
    



 **Note:**

  1. `0 <= logs.length <= 100`
  2. `3 <= logs[i].length <= 100`
  3. `logs[i]` is guaranteed to have an identifier, and a word after the identifier.

### Solution 1
my solution takes advantage of what we're guaranteed in the problem:

  1. guaranteed to have a word following an identifier (allows me to use indexOf ' ' freely).
  2. letter logs need to be ordered lexicographically, so we can use built in compare function when we know we have two.
  3. number logs need to be sorted naturally, so we just say they're all "equal" to eachother and trust java's built in sort feature to be stable.
  4. number logs need to be after letter logs, so once we find out they're different, we return one of the other and short-circuit.

    
    
    public String[] reorderLogFiles(String[] logs) {
            
            Comparator<String> myComp = new Comparator<String>() {
                @Override
                public int compare(String s1, String s2) {
                    int s1si = s1.indexOf(' ');
                    int s2si = s2.indexOf(' ');
                    char s1fc = s1.charAt(s1si+1);
                    char s2fc = s2.charAt(s2si+1);
                    
                    if (s1fc <= '9') {
                        if (s2fc <= '9') return 0;
                        else return 1;
                    }
                    if (s2fc <= '9') return -1;
                    
                    int preCompute = s1.substring(s1si+1).compareTo(s2.substring(s2si+1));
                    if (preCompute == 0) return s1.substring(0,s1si).compareTo(s2.substring(0,s2si));
                    return preCompute;
                }
            };
            
            Arrays.sort(logs, myComp);
            return logs;
        }
    


### Solution 2
    
    
    bool myCompare(string a, string b){
        int i = a.find(' ');
        int j = b.find(' ');
        if(!isdigit(a[i + 1]) && !isdigit(b[j + 1]))
            return a.substr(i + 1) < b.substr(j + 1);
        else{
            if(!isdigit(a[i + 1])) return true;
            return false;
        }
    }
    class Solution {
    public:
        vector<string> reorderLogFiles(vector<string>& logs) {
            stable_sort(logs.begin(), logs.end(), myCompare);
            return logs;
        }
    };
    


### Solution 3
    
    
    class Solution:
        def reorderLogFiles(self, logs):
            """
            :type logs: List[str]
            :rtype: List[str]
            """
            digits = []
            letters = []
    		# divide logs into two parts, one is digit logs, the other is letter logs
            for log in logs:
                if log.split()[1].isdigit():
                    digits.append(log)
                else:
                    letters.append(log)
                    
            letters.sort(key = lambda x: x.split()[1])            #when suffix is tie, sort by identifier
    		letters.sort(key = lambda x: x.split()[1:])           #sort by suffix
            result = letters + digits                                        #put digit logs after letter logs
            return result
    
    



