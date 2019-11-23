---
layout: post
title: 621. Task Scheduler
---
### Question
Given a char array representing tasks CPU need to do. It contains capital
letters A to Z where different letters represent different tasks. Tasks could
be done without original order. Each task could be done in one interval. For
each interval, CPU could finish one task or just be idle.

However, there is a non-negative cooling interval **n** that means between two
**same tasks** , there must be at least n intervals that CPU are doing
different tasks or just be idle.

You need to return the **least** number of intervals the CPU will take to
finish all the given tasks.



 **Example:**

    
    
     **Input:** tasks = ["A","A","A","B","B","B"], n = 2
    **Output:** 8
    **Explanation:** A - > B -> idle -> A -> B -> idle -> A -> B.
    



 **Note:**

  1. The number of tasks is in the range [1, 10000].
  2. The integer n is in the range [0, 100].

### Solution 1
The key is to find out how many idles do we need.  
Let's first look at how to arrange them. it's not hard to figure out that we
can do a "greedy arrangement": always arrange task with most frequency first.  
E.g. we have following tasks : 3 A, 2 B, 1 C. and we have n = 2. According to
what we have above, we should first arrange A, and then B and C. Imagine there
are "slots" and we need to arrange tasks by putting them into "slots". Then A
should be put into slot 0, 3, 6 since we need to have at least n = 2 other
tasks between two A. After A put into slots, it looks like this:

A ? ? A ? ? A  
"?" is "empty" slots.

Now we can use the same way to arrange B and C. The finished schedule should
look like this:

A B C A B # A  
"#" is idle

Now we have a way to arrange tasks. But the problem only asks for number of
CPU intervals, so we don't need actually arrange them. Instead we only need to
get the total idles we need and the answer to problem is just number of idles
+ number of tasks.  
Same example: 3 A, 2 B, 1 C, n = 2. After arranging A, we have:  
A ? ? A ? ? A  
We can see that A separated slots into (count(A) - 1) = 2 parts, each part has
length n. With the fact that A is the task with most frequency, it should need
more idles than any other tasks. **In this case if we can get how many idles
we need to arrange A, we will also get number of idles needed to arrange all
tasks.** Calculating this is not hard, we first get number of parts separated
by A: partCount = count(A) - 1; then we can know number of empty slots:
emptySlots = partCount * n; we can also get how many tasks we have to put into
those slots: availableTasks = tasks.length - count(A). Now if we have
emptySlots  > availableTasks which means we have not enough tasks available to
fill all empty slots, we must fill them with idles. Thus we have **idles =
max(0, emptySlots - availableTasks);**  
Almost done. One special case: what if there are more than one task with most
frequency? OK, let's look at another example: 3 A 3 B 2 C 1 D, n = 3  
Similarly we arrange A first:  
A ? ? ? A ? ? ? A  
Now it's time to arrange B, we find that we have to arrange B like this:  
A B ? ? A B ? ? A B  
we need to put every B right after each A. Let's look at this in another way,
think of sequence "A B" as a special task "X", then we got:  
X ? ? X ? ? X  
Comparing to what we have after arranging A:  
A ? ? ? A ? ? ? A  
The only changes are length of each parts (from 3 to 2) and available tasks.
By this we can get more general equations:  
 **partCount = count(A) - 1  
emptySlots = partCount * (n - (count of tasks with most frequency - 1))  
availableTasks = tasks.length - count(A) * count of tasks with most frenquency  
idles = max(0, emptySlots - availableTasks)  
result = tasks.length + idles**

What if we have more than n tasks with most frequency and we got emptySlot
negative? Like 3A, 3B, 3C, 3D, 3E, n = 2. In this case seems like we can't put
all B C S inside slots since we only have n = 2.  
Well partCount is actually the "minimum" length of each part required for
arranging A. You can always make the length of part longer.  
E.g. 3A, 3B, 3C, 3D, 2E, n = 2.  
You can always first arrange A, B, C, D as:  
A B C D | A B C D | A B C D  
in this case you have already met the "minimum" length requirement for each
part (n = 2), you can always put more tasks in each part if you like:  
e.g.  
A B C D E | A B C D E | A B C D

emptySlots < 0 means you have already got enough tasks to fill in each part to
make arranged tasks valid. But as I said you can always put more tasks in each
part once you met the "minimum" requirement.

To get count(A) and count of tasks with most frequency, we need to go through
inputs and calculate counts for each distinct char. This is O(n) time and
O(26) space since we only handle upper case letters.  
All other operations are O(1) time O(1) space which give us total time
complexity of O(n) and space O(1)

    
    
    public class Solution {
        public int leastInterval(char[] tasks, int n) {
            int[] counter = new int[26];
            int max = 0;
            int maxCount = 0;
            for(char task : tasks) {
                counter[task - 'A']++;
                if(max == counter[task - 'A']) {
                    maxCount++;
                }
                else if(max < counter[task - 'A']) {
                    max = counter[task - 'A'];
                    maxCount = 1;
                }
            }
            
            int partCount = max - 1;
            int partLength = n - (maxCount - 1);
            int emptySlots = partCount * partLength;
            int availableTasks = tasks.length - max * maxCount;
            int idles = Math.max(0, emptySlots - availableTasks);
            
            return tasks.length + idles;
        }
    }
    


### Solution 2
    
    
    // (c[25] - 1) * (n + 1) + 25 - i  is frame size
    // when inserting chars, the frame might be "burst", then tasks.length takes precedence
    // when 25 - i > n, the frame is already full at construction, the following is still valid.
    public class Solution {
        public int leastInterval(char[] tasks, int n) {
    
            int[] c = new int[26];
            for(char t : tasks){
                c[t - 'A']++;
            }
            Arrays.sort(c);
            int i = 25;
            while(i >= 0 && c[i] == c[25]) i--;
    
            return Math.max(tasks.length, (c[25] - 1) * (n + 1) + 25 - i);
        }
    }
    

First consider the most frequent characters, we can determine their relative
positions first and use them as a frame to insert the remaining less frequent
characters. Here is a proof by construction:

Let F be the set of most frequent chars with frequency k.  
We can create k chunks, each chunk is identical and is a string consists of
chars in F in a specific fixed order.  
Let the heads of these chunks to be H_i; then H_2 should be at least n chars
away from H_1, and so on so forth; then we insert the less frequent chars into
the gaps between these chunks sequentially one by one ordered by frequency in
a decreasing order and try to fill the k-1 gaps as full or evenly as possible
each time you insert a character. **In summary, append the less frequent
characters to the end of each chunk of the first k-1 chunks sequentially and
round and round, then join the chunks and keep their heads' relative distance
from each other to be at least n**.

Examples:

AAAABBBEEFFGG 3

here X represents a space gap:

    
    
    Frame: "AXXXAXXXAXXXA"
    insert 'B': "ABXXABXXABXXA" <--- 'B' has higher frequency than the other characters, insert it first.
    insert 'E': "ABEXABEXABXXA"
    insert 'F': "ABEFABEXABFXA" <--- each time try to fill the k-1 gaps as full or evenly as possible.
    insert 'G': "ABEFABEGABFGA"
    

AACCCBEEE 2

    
    
    3 identical chunks "CE", "CE CE CE" <-- this is a frame
    insert 'A' among the gaps of chunks since it has higher frequency than 'B' ---> "CEACEACE"
    insert 'B' ---> "CEABCEACE" <----- result is tasks.length;
    

AACCCDDEEE 3

    
    
    3 identical chunks "CE", "CE CE CE" <--- this is a frame.
    Begin to insert 'A'->"CEA CEA CE"
    Begin to insert 'B'->"CEABCEABCE" <---- result is tasks.length;
    

ACCCEEE 2

    
    
    3 identical chunks "CE", "CE CE CE" <-- this is a frame
    Begin to insert 'A' --> "CEACE CE" <-- result is (c[25] - 1) * (n + 1) + 25 -i = 2 * 3 + 2 = 8


### Solution 3
The idea used here is similar to - <https://leetcode.com/problems/rearrange-
string-k-distance-apart>  
We need to arrange the characters in string such that each same character is K
distance apart, where distance in this problems is time b/w two similar task
execution.

Idea is to add them to a priority Q and sort based on the highest frequency.  
And pick the task in each round of 'n' with highest frequency. As you pick the
task, decrease the frequency, and put them back after the round.

    
    
    public int leastInterval(char[] tasks, int n) {
         Map<Character, Integer> map = new HashMap<>();
        for (int i = 0; i < tasks.length; i++) {
            map.put(tasks[i], map.getOrDefault(tasks[i], 0) + 1); // map key is TaskName, and value is number of times to be executed.
        }
        PriorityQueue<Map.Entry<Character, Integer>> q = new PriorityQueue<>( //frequency sort
                (a,b) -> a.getValue() != b.getValue() ? b.getValue() - a.getValue() : a.getKey() - b.getKey());
    
        q.addAll(map.entrySet());
    
        int count = 0;
        while (!q.isEmpty()) {
            int k = n + 1;
            List<Map.Entry> tempList = new ArrayList<>();
            while (k > 0 && !q.isEmpty()) {
                Map.Entry<Character, Integer> top = q.poll(); // most frequency task
                top.setValue(top.getValue() - 1); // decrease frequency, meaning it got executed
                tempList.add(top); // collect task to add back to queue
                k--;
                count++; //successfully executed task
            }
    
            for (Map.Entry<Character, Integer> e : tempList) {
                if (e.getValue() > 0) q.add(e); // add valid tasks 
            }
    
            if (q.isEmpty()) break;
            count = count + k; // if k > 0, then it means we need to be idle
        }
        return count;
    }



