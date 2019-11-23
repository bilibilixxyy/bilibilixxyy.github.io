---
layout: post
title: 828. Chalkboard XOR Game
---
### Question
We are given non-negative integers nums[i] which are written on a chalkboard.
Alice and Bob take turns erasing exactly one number from the chalkboard, with
Alice starting first.  If erasing a number causes the bitwise XOR of all the
elements of the chalkboard to become 0, then that player loses.  (Also, we'll
say the bitwise XOR of one element is that element itself, and the bitwise XOR
of no elements is 0.)

Also, if any player starts their turn with the bitwise XOR of all the elements
of the chalkboard equal to 0, then that player wins.

Return True if and only if Alice wins the game, assuming both players play
optimally.

    
    
     **Example:**
    **Input:** nums = [1, 1, 2]
    **Output:** false
    **Explanation:** 
    Alice has two choices: erase 1 or erase 2. 
    If she erases 1, the nums array becomes [1, 2]. The bitwise XOR of all the elements of the chalkboard is 1 XOR 2 = 3. Now Bob can remove any element he wants, because Alice will be the one to erase the last element and she will lose. 
    If Alice erases 2 first, now nums becomes [1, 1]. The bitwise XOR of all the elements of the chalkboard is 1 XOR 1 = 0. Alice will lose.
    
    

**Notes:**

  * `1 <= N <= 1000`. 
  * `0 <= nums[i] <= 2^16`.

### Solution 1
 **Should not be Hard problem**  
The solution can be only 3 lines and really simple.  
It should be a medium problem.  
`Hard` is kind of misleading.

 **Why`[1, 2, 3]` expect `true`**  
If `xor == 0` at first for Alice, it is considered as Bob lose already!  
I find this stupid idea just after contest and I think it doesn't make any
sense.  
It should complete this condition in the description.

 **Let's discuss it if we add this condition.**  
If `xor == 0`, Alice win directly.  
If `xor != 0` and length of numbers is even, Alice will win.

Beacause:  
All numbers won't be the same. Otherwise `xor` will be equal to `0`  
If all numbers are not the same, It means there are at least 2 different
numbers.  
Alice can always erase a number different from current `xor`.  
So Alice won't never lose this turn at this situation.

 **If we don't have the condition**  
Just return `nums are not all 0` and `length of nums is even`

C++:

    
    
        bool xorGame(vector<int>& nums) {
            int xo = 0;
            for (int i: nums) xo ^= i;
            return xo == 0 || nums.size() % 2 == 0;
        }
    

Java:

    
    
        public boolean xorGame(int[] nums) {
            int xor = 0;
            for (int i: nums) xor ^= i;
            return xor == 0 || nums.length % 2 == 0;
        }
    

Python

    
    
        def xorGame(self, nums):
            xor = 0
            for i in nums: xor ^= i
            return xor == 0 or len(nums) % 2 == 0
    
    


### Solution 2
Why [1,2,3] returns true?  
How can Alice win? No matter what she erase, Bob can erase any number and
Alice will loss.


### Solution 3
Clearly if the xor of all the numbers is 0 to start, then Alice wins off the
bat!

Claim: If it is Alice's turn with an even number of elements left, Alice can
NEVER lose. Equivalently, this means that Bob will always lose, or that Alice
will always win.

If you believe this claim, then Alice can only win if there is an even number
of elements left (otherwise after Alice's turn, Bob has an even number of
elements left and will win). Thus the check `return len(nums) % 2 == 0` is
sufficient. Now the only thing that remains is proving the claim:

To get an intuition about why this claim is true, we first need to look at the
properties of XOR and how it pertains to this problem. Recall that for any
number A, A XOR A = 0. Also recall that A is the ONLY number that can XOR A to
0 (i.e. given A, the solution for A XOR B = 0 is uniquly B=A). Also remember
that the XOR operation is commutative (A XOR B = B XOR A). Why is all this
important? Consider what happens in the game:

Let the n_i's be the numbers on the chalkboard  
n_1, n_2, n_3, ...

These n_i's XOR to some value -- call it N. (N = n_1 XOR n_2 XOR n_3 ...).

What happens to N when you "erase" a number (say n_i)? Well the new N (call it
N_new) is N_new = n_1 XOR n_2 ... n_(i-1) XOR n_(i+1) XOR ...  
In other words, it is the XOR of all the elements EXCEPT n_i. **The big catch
here is to realize that N_new = N XOR n_i.** This is because:

N XOR n_i = (N_new XOR n_i) XOR n_i = N_new XOR (n_i XOR n_i) = N_new XOR 0 =
N_new

So "erasing" an element (n_i) causes the total XOR of all the numbers (N) to
change to N XOR n_i. This is important because this means that **Alice can
only LOSE if she chooses a number n_i such that N XOR n_i = 0**

So when does N XOR n_i = 0? The only way is if n_i = N. Meaning that **Alice
can only LOSE if she picks a number that equals to the XOR of all the
elements**!

If Alice can always avoid picking N, then she will never lose! The only case
where Alice CAN'T avoid picking N (i.e. the only case where Alice can lose),
is if all the n_i's = N. This is where we get to proving our claim from
before. Say Alice has an even number of elements left -- then if there is an
element that is not N, she'll pick it and will not lose. If every element is
N, then will Alice lose? NO! If there are an even number of elements left, and
they are ALL the same, then the XOR of all those elements is 0 -- therefore
Alice wins on the spot!

We just showed that **Alice can always pick an item to NOT lose the game,
given that there are an even number of elements remaining**. If Bob survives
to the next round, Alice will again have an even number of elements remaining,
and can again pick an item to NOT lose the game. Eventually Bob will run out
of items, and Alice will win.

Clearly this argument is symmetric for Bob (replace Bob with Alice), so if
Alice starts off with an odd number of elements, then either she makes a move
and loses, or she makes a move and then Bob will have an even number of
elements remaining -- in which case Bob wins, and Alice still loses.

    
    
    class Solution(object):
        def xorGame(self, nums):
            """
            :type nums: List[int]
            :rtype: bool
            """
            
            r_xor = 0
            for num in nums:
                r_xor = r_xor ^ num
            
            if r_xor == 0:
                # if xor of all elements is 0, then Alice wins
                return True
            
            # Alice wins if and only if there is an even number of numbers
            return len(nums) % 2 == 0
    



