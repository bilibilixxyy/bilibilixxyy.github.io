---
layout: post
title: 876. Hand of Straights
---
### Question
Alice has a `hand` of cards, given as an array of integers.

Now she wants to rearrange the cards into groups so that each group is size
`W`, and consists of `W` consecutive cards.

Return `true` if and only if she can.



 **Example 1:**

    
    
     **Input:** hand = [1,2,3,6,2,3,4,7,8], W = 3
    **Output:** true
    **Explanation:** Alice's hand can be rearranged as [1,2,3],[2,3,4],[6,7,8].

**Example 2:**

    
    
    **Input:** hand = [1,2,3,4,5], W = 4
    **Output:** false
    **Explanation:** Alice's hand can't be rearranged into groups of 4.



 **Note:**

  1. `1 <= hand.length <= 10000`
  2. `0 <= hand[i] <= 10^9`
  3. `1 <= W <= hand.length`

### Solution 1
 **Intuition** :

  1. Count number of different cards to a map `c`
  2. Loop from the smallest card number.
  3. Everytime we meet a new card `i`, we cut off `i` \- `i + W - 1` from the counter.

 **Time Complexity** :  
`O(MlogM + MW)`, where `M` is the number of different cards.

 **C++:**

    
    
         bool isNStraightHand(vector<int> hand, int W) {
            map<int, int> c;
            for (int i : hand) c[i]++;
            for (auto it : c)
                if (c[it.first] > 0)
                    for (int i = W - 1; i >= 0; --i)
                        if ((c[it.first + i] -= c[it.first]) < 0)
                            return false;
            return true;
        }
    

**Java:**

    
    
        public  boolean isNStraightHand(int[] hand, int W) {
            Map<Integer, Integer> c = new TreeMap<>();
            for (int i : hand) c.put(i, c.getOrDefault(i, 0)+1);
            for (int it : c.keySet())
                if (c.get(it) > 0)
                    for (int i = W - 1; i >= 0; --i) {
                        if (c.getOrDefault(it + i, 0) < c.get(it)) return false;
                        c.put(it + i, c.get(it + i) - c.get(it));
                    }
            return true;
        }
    

**Python:**

    
    
        def isNStraightHand( self, hand, W):
            c = collections.Counter(hand)
            for i in sorted(c):
                if c[i] > 0:
                    for j in range(W)[::-1]:
                        c[i + j] -= c[i]
                        if c[i + j] < 0:
                            return False
            return True
    

**Follow Up**  
We just got lucky AC solution. Because `W <= 10000`.  
What if W is huge, should we cut off card on by one?

**Explanation** :

  1. Count number of different cards to a map `c`
  2. `Cur` represent current open straight groups.
  3. In a deque `start`, we record the number of opened a straight group.
  4. Loop from the smallest card number.

For example, hand = [1,2,3,2,3,4], W = 3  
We meet one 1:  
opened = 0, we open a new straight groups starting at 1, push (1,1) to
`start`.  
We meet two 2:  
opened = 1, we need open another straight groups starting at 1, push (2,1) to
`start`.  
We meet two 3:  
opened = 2, it match current opened groups.  
We open one group at 1, now we close it. opened = opened - 1 = 1  
We meet one 4:  
opened = 1, it match current opened groups.  
We open one group at 2, now we close it. opened = opened - 1 = 0

  5. return if no more open groups.

**Time Complexity** :  
`O(MlogM)`, where `M` is the number of different cards.  
Because I count and sort cards.  
In Cpp and Java it's `O(NlogM)`, which can also be improved.

**C++:**

    
    
         bool isNStraightHand(vector<int> hand, int W) {
            map<int, int> c;
            for (int i : hand) c[i]++;
            queue<int> start;
            int last_checked = -1, opened = 0;
            for (auto it : c) {
                int i = it.first;
                if (opened > 0 && i > last_checked + 1 || opened > c[i]) return false;
                start.push(c[i] - opened);
                last_checked = i, opened = c[i];
                if (start.size() == W) {
                    opened -= start.front();
                    start.pop();
                }
            }
            return opened == 0;
        }
    

**Java**

    
    
         public boolean isNStraightHand(int[] hand, int W) {
            Map<Integer, Integer> c = new TreeMap<>();
            for (int i : hand) c.put(i, c.getOrDefault(i, 0)+1);
            Queue<Integer> start = new LinkedList<>();
            int last_checked = -1, opened = 0;
            for (int i : c.keySet()) {
                if (opened > 0 && i > last_checked + 1 || opened > c.get(i)) return false;
                start.add(c.get(i) - opened);
                last_checked = i; opened = c.get(i);
                if (start.size() == W) opened -= start.remove();
            }
            return opened == 0;
        }
    

**Python:**

    
    
        def isNStraightHand(self, hand, W):
            c = collections.Counter(hand)
            start = collections.deque()
            last_checked, opened =  -1, 0
            for i in sorted(c):
                if opened > c[i] or opened > 0 and i > last_checked + 1: return False
                start.append(c[i] - opened)
                last_checked, opened = i, c[i]
                if len(start) == W: opened -= start.popleft()
            return opened == 0
    

**Update 2018-06-03** :  
Thanks to suggeestion from @cliuzh, I check whether the cards in the map are
consecutive.  
And I add only opened group number to queue.


### Solution 2
Would love any criticism / recommendations!

Empirical runtime:  
40ms

Motivation:  
Since the numbers need to consecutive we immediately can figure out the next W
- 1 numbers that must occur after a given number. By using a priority queue we
can poll the smallest number and remove the next W - 1 consecutive numbers. If
any of the consecutive numbers are not in the priority queue, it implies the
hand is invalid and thus returns false.

    
    
    class Solution {
        public boolean isNStraightHand(int[] hand, int W) {
            PriorityQueue<Integer> minHeap = new PriorityQueue<>();
            for(int i : hand){
                minHeap.add(i);
            }
            while(minHeap.size() != 0) {
                int start = minHeap.poll();
                for(int j = 1; j < W; j++){
                    if(minHeap.remove(start + j)) {
                        continue;
                    }
                    else {
                        return false;
                    }
                }
            }
            return true;
        }
    }
    


### Solution 3
    
    
        hand.sort()
        while hand:
            try:
                base = hand[0]
                for i in range(W):
                    hand.remove(base+i)
            except:
                return False
        return True
    

B站解说传送门：<https://www.bilibili.com/video/av24391209/?p=3>



