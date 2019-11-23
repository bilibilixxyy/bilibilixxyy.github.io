---
layout: post
title: 406. Queue Reconstruction by Height
---
### Question
Suppose you have a random list of people standing in a queue. Each person is
described by a pair of integers `(h, k)`, where `h` is the height of the
person and `k` is the number of people in front of this person who have a
height greater than or equal to `h`. Write an algorithm to reconstruct the
queue.

 **Note:**  
The number of people is less than 1,100.

  

 **Example**

    
    
    Input:
    [[7,0], [4,4], [7,1], [5,0], [6,1], [5,2]]
    
    Output:
    [[5,0], [7,0], [5,2], [6,1], [4,4], [7,1]]
    

### Solution 1
  1.  **Pick out tallest group of people and sort them** in a subarray (S). Since there's no other groups of people taller than them, therefore **each guy's index will be just as same as his k value**.
  2. For 2nd tallest group (and the rest), insert each one of them into (S) by k value. So on and so forth.

E.g.  
input: [[7,0], [4,4], [7,1], [5,0], [6,1], [5,2]]  
subarray after step 1: [ **[7,0], [7,1]** ]  
subarray after step 2: [[7,0], **[6,1]** , [7,1]]  
...

It's not the most concise code, but I think it well explained the concept.

    
    
    class Solution(object):
        def reconstructQueue(self, people):
            if not people: return []
    
            # obtain everyone's info
            # key=height, value=k-value, index in original array
            peopledct, height, res = {}, [], []
            
            for i in xrange(len(people)):
                p = people[i]
                if p[0] in peopledct:
                    peopledct[p[0]] += (p[1], i),
                else:
                    peopledct[p[0]] = [(p[1], i)]
                    height += p[0],
    
            height.sort()      # here are different heights we have
    
            # sort from the tallest group
            for h in height[::-1]:
                peopledct[h].sort()
                for p in peopledct[h]:
                    res.insert(p[0], people[p[1]])
    
            return res
    
    

**EDIT:**  
Please also check:  
@tlhuang 's concise Python code.  
@wsurvi 's 4 lines Python code.  
@tonygogogo 's 8 lines C++ solution.  
@zeller2 's Java version.  
@hotpro 's Java 8 solution.


### Solution 2
Below is my explanation of the following neat solution where we sort people
from tall to short (and by increasing k-value) and then just insert them into
the queue using their k-value as the queue index:

    
    
    def reconstructQueue(self, people):
        people.sort(key=lambda (h, k): (-h, k))
        queue = []
        for p in people:
            queue.insert(p[1], p)
        return queue
    

I didn't come up with that myself, but here's my own explanation of it, as I
haven't seen anybody explain it (and was asked to explain it):

People are only counting (in their k-value) taller or equal-height others
standing in front of them. So a smallest person is **completely irrelevant**
for all taller ones. And of all smallest people, the one standing most in the
back is even completely irrelevant for **everybody** else. Nobody is counting
that person. So we can first arrange everybody else, ignoring that one person.
And then just insert that person appropriately. Now note that while this
person is irrelevant for everybody else, everybody else is relevant for this
person - this person counts exactly everybody in front of them. So their
count-value tells you exactly the index they must be standing.

So you can first solve the sub-problem with all but that one person and then
just insert that person appropriately. And you can solve that sub-problem the
same way, first solving the sub-sub-problem with all but the last-smallest
person of the subproblem. And so on. The base case is when you have the
sub-...-sub-problem of zero people. You're then inserting the people in the
reverse order, i.e., that overall last-smallest person in the very end and
thus the first-tallest person in the very beginning. That's what the above
solution does, Sorting the people from the first-tallest to the last-smallest,
and inserting them one by one as appropriate.

Now that's **my** explanation. If you have a different one, I'm interested to
see it :-)


### Solution 3
Is reconstructing a queue a keyword in programming? I am not familiar.

Thanks.



