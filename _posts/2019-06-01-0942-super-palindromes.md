---
layout: post
title: 942. Super Palindromes
---
### Question
Let's say a positive integer is a  _superpalindrome_  if it is a palindrome,
and it is also the square of a palindrome.

Now, given two positive integers `L` and `R` (represented as strings), return
the number of superpalindromes in the inclusive range `[L, R]`.



 **Example 1:**

    
    
     **Input:** L = "4", R = "1000"
    **Output:** 4
    **Explanation** : 4, 9, 121, and 484 are superpalindromes.
    Note that 676 is not a superpalindrome: 26 * 26 = 676, but 26 is not a palindrome.



 **Note:**

  1. `1 <= len(L) <= 18`
  2. `1 <= len(R) <= 18`
  3. `L` and `R` are strings representing integers in the range `[1, 10^18)`.
  4. `int(L) <= int(R)`

### Solution 1
Although the func `nextP` could be improved.

    
    
    class Solution {
        public int superpalindromesInRange(String L, String R) {
            Long l = Long.valueOf(L), r = Long.valueOf(R);
            int result = 0;
            for (long i = (long)Math.sqrt(l); i * i <= r;) {
                long p = nextP(i);
                if (p * p <= r && isP(p * p)) {
                    result++;
                }
                i = p + 1;
            }
            return result;
        }
        
        private long nextP(long l) {
            String s = "" + l;
            int len = s.length();
            List<Long> cands = new LinkedList<>();
            cands.add((long)Math.pow(10, len) - 1);
            String half = s.substring(0, (len + 1) / 2);
            String nextHalf = "" + (Long.valueOf(half) + 1);
            String reverse = new StringBuilder(half.substring(0, len / 2)).reverse().toString();
            String nextReverse = new StringBuilder(nextHalf.substring(0, len / 2)).reverse().toString();
            cands.add(Long.valueOf(half + reverse));
            cands.add(Long.valueOf(nextHalf + nextReverse));
            long result = Long.MAX_VALUE;
            for (long i : cands) {
                if (i >= l) {
                    result = Math.min(result, i);
                }
            }
            return result;
        }
        
        private boolean isP(long l) {
            String s = "" + l;
            int i = 0, j = s.length() - 1;
            while (i < j) {
                if (s.charAt(i++) != s.charAt(j--)) {
                    return false;
                }
            }
            return true;
        }
    }
    


### Solution 2
it is good for leetcode, but not for contest!

OEIS A002779

    
    
    vector<uint64_t> value {
        0, 1, 4, 9, 121, 484, 676, 10201, 12321, 14641, 40804, 44944, 69696, 94249, 698896, 1002001, 1234321, 
        4008004, 5221225, 6948496, 100020001, 102030201, 104060401, 121242121, 123454321, 125686521, 400080004, 
        404090404, 522808225, 617323716, 942060249, 10000200001, 10221412201, 12102420121, 12345654321, 
        40000800004, 637832238736, 1000002000001, 1002003002001, 1004006004001, 1020304030201, 1022325232201, 
        1024348434201, 1086078706801, 1210024200121, 1212225222121, 1214428244121, 1230127210321, 1232346432321, 
        1234567654321, 1615108015161, 4000008000004, 4004009004004, 4051154511504, 5265533355625, 9420645460249, 
        100000020000001, 100220141022001, 102012040210201, 102234363432201, 121000242000121, 121242363242121, 
        123212464212321, 123456787654321, 123862676268321, 144678292876441, 165551171155561, 400000080000004, 
        900075181570009, 4099923883299904, 10000000200000001, 10002000300020001, 10004000600040001, 10020210401202001, 
        10022212521222001, 10024214841242001, 10201020402010201, 10203040504030201, 10205060806050201, 
        10221432623412201, 10223454745432201, 12100002420000121, 12102202520220121, 12104402820440121, 
        12120030703002121, 12122232623222121, 12124434743442121, 12321024642012321, 12323244744232321, 
        12341234943214321, 12343456865434321, 12345678987654321, 40000000800000004, 40004000900040004, 94206450305460249,
    };
    
    class Solution {
    public:
        int superpalindromesInRange(string L, string R) {
            auto l = lower_bound(value.begin(), value.end(), stoull(L));
            auto r = upper_bound(value.begin(), value.end(), stoull(R));
            int res = 0;
            for (;l != r; ++l) {
                int64_t v = *l;
                int64_t root = round(sqrt(v));
                string s1 = to_string(root);
                string s2 = s1;
                reverse(s2.begin(), s2.end());
                if (s2 == s1) ++res;
            }
            return res;
        }
    };    


### Solution 3
we can construct in two steps:

  1. Get all palindrome number < 10^9 in res(because the input < 10^18)
  2. Traverse each number in res,determin whether the square is a palindrome

The first question is how to approach the first step, apparently we cannot
traverse all number because 10^9 is too large. The key is **'construct it'**
instead of **'judge it one by one'**.

Every palindrome number can be divided into 3 part, I call it `left part`,
`middle part` and `right part`. For instance, `12121` can be divided into
`'12'`, `'1'`, `'21'`, corresponding `left part`, `middle part`, `right part`.
As for `123321`, it can be divided into `'123'`, `''`, `'321'`.

For `each single number x`, we can construct `11 palindrome number`, which are
`xx,x0x,x1x,..,x9x`, and that is what we need.

    
    
    res = [1,2,3,4,5,6,7,8,9] # initial
    for i in range(1,10000): # we only need at most four digits to consturct nine digits
        s1 = str(i) + str(i)[::-1]
        res.append(s1)
        for j in range(10):
            s2 = str(i) + str(j) + str(i)[::-1]
            res.append(s2)
    

so, the total number in res is 11*10000 + 9. Apparently we can easily
implement the second step by traversing the res!

    
    
    def isPalin(s):
        return s == s[::-1]
    
    res = list(map(int, res))
    res.sort()
    ans = []
    for val in res:
        s = str(val**2)
        if isPalin(s):
            ans.append(int(s))
    print(ans)
    



