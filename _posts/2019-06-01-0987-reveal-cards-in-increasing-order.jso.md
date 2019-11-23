---
layout: post
title: 987. Reveal Cards In Increasing Order
---
### Question
In a deck of cards, every card has a unique integer.  You can order the deck
in any order you want.

Initially, all the cards start face down (unrevealed) in one deck.

Now, you do the following steps repeatedly, until all cards are revealed:

  1. Take the top card of the deck, reveal it, and take it out of the deck.
  2. If there are still cards in the deck, put the next top card of the deck at the bottom of the deck.
  3. If there are still unrevealed cards, go back to step 1.  Otherwise, stop.

Return an ordering of the deck that would reveal the cards in **increasing
order.**

The first entry in the answer is considered to be the top of the deck.



 **Example 1:**

    
    
     **Input:** [17,13,11,2,3,5,7]
    **Output:** [2,13,3,11,5,17,7]
    **Explanation:**
    We get the deck in the order [17,13,11,2,3,5,7] (this order doesn't matter), and reorder it.
    After reordering, the deck starts as [2,13,3,11,5,17,7], where 2 is the top of the deck.
    We reveal 2, and move 13 to the bottom.  The deck is now [3,11,5,17,7,13].
    We reveal 3, and move 11 to the bottom.  The deck is now [5,17,7,13,11].
    We reveal 5, and move 17 to the bottom.  The deck is now [7,13,11,17].
    We reveal 7, and move 13 to the bottom.  The deck is now [11,17,13].
    We reveal 11, and move 17 to the bottom.  The deck is now [13,17].
    We reveal 13, and move 17 to the bottom.  The deck is now [17].
    We reveal 17.
    Since all the cards revealed are in increasing order, the answer is correct.
    



 **Note:**

  1. `1 <= A.length <= 1000`
  2. `1 <= A[i] <= 10^6`
  3. `A[i] != A[j]` for all `i != j`

### Solution 1
Simulate the process with a queue.

  1. Sort the deck, it is actually the "final sequence" we want to get according to the question.
  2. Then put it back to the result array, we just need to deal with the index now!
  3. Simulate the process with a queue (initialized with 0,1,2...(n-1)), now how do we pick the card?
  4. We first pick the index at the top: `res[q.poll()]=deck[i]`
  5. Then we put the next index to the bottom: `q.add(q.poll());`
  6. Repeat it n times, and you will have the result array!

 **update**  
Let's walk through the example:  
Input: `[17,13,11,2,3,5,7]`  
Output: `[2,13,3,11,5,17,7]`

  1. Sort the deck: `[2,3,5,7,11,13,17]`, this is the increasing order we want to generate
  2. Initialize the queue: `[0,1,2,3,4,5,6]`, this is the index of the result array
  3.  **The first card** we pick is `res[0]`, observe the deck, it should be `deck[0]==2`, so assign `res[0]=2`
  4. Then we put `res[1]` to the bottom, so we re-insert `1` to the queue
  5.  **The second card** we pick is `res[2]`, which should be `deck[1]==3`, so assign `res[2]=3`
  6. Then we re-insert `3` to the queue
  7. Each time we assign 1 value to the res, so we repeat this n times.

Hope this helps.

    
    
    class Solution {
        public int[] deckRevealedIncreasing(int[] deck) {
            int n= deck.length;
            Arrays.sort(deck);
            Queue<Integer> q= new LinkedList<>();
            for (int i=0; i<n; i++) q.add(i);
            int[] res= new int[n];
            for (int i=0; i<n; i++){
                res[q.poll()]=deck[i];
                q.add(q.poll());
            }
            return res;
        }
    }
    

Happy Coding!


### Solution 2
We simulate the reversed process.  
Initial an empty list or deque or queue,  
each time rotate the last element to the first,  
and append a the next biggest number to the left.

Time complexity:  
`O(NlogN)` to sort,  
`O(N)` to construct using deque or queue.

 **Java, using queue**

    
    
         public int[] deckRevealedIncreasing(int[] deck) {
            int n = deck.length;
            Arrays.sort(deck);
            Queue<Integer> q = new LinkedList<>();
            for (int i = n - 1; i >= 0; --i) {
                if (q.size() > 0) q.add(q.poll());
                q.add(deck[i]);
            }
            int[] res = new int[n];
            for (int i = n - 1; i >= 0; --i) {
                res[i] = q.poll();
            }
            return res;
        }
    

**C++, using deque**

    
    
        vector< int> deckRevealedIncreasing(vector<int>& deck) {
            sort(deck.rbegin(), deck.rend());
            deque<int> d;
            d.push_back(deck[0]);
            for (int i = 1; i < deck.size(); i++) {
                d.push_front(d.back());
                d.pop_back();
                d.push_front(deck[i]);
            }
            vector<int> res(d.begin(), d.end());
            return res;
        }
    

**Python, using list,`O(N^2)`:**

    
    
         def deckRevealedIncreasing(self, deck):
            d = []
            for x in sorted(deck)[::-1]:
                d = [x] + d[-1:] + d[:-1]
            return d
    

**Python, using deque:**

    
    
         def deckRevealedIncreasing(self, deck):
            d = collections.deque()
            for x in sorted(deck)[::-1]:
                d.rotate()
                d.appendleft(x)
            return list(d)
    


### Solution 3
We sort the input array and initialize the result array with zeros (empty
spaces). Then we insert sorted numbers into the second available empty space,
skipping the first available empty space.

After we reach the end of the result array, we continue looking for empty
spaces from the beginning. The picture below demonstrates each "swipe" of the
results array for input numbers [1..15]. Empty spaces are yellow, newly
inserted numbers are highlighted.  
![image](https://assets.leetcode.com/users/votrubac/image_1543911256.png)  
Note that for the very last number, we skip the last empty space (position 14)
twice.

    
    
    vector<int> deckRevealedIncreasing(vector<int>& deck) {
      sort(begin(deck), end(deck));
      vector<int> res(deck.size(), 0);
      res[0] = deck[0];
      for (auto i = 1, p = 0; i < deck.size(); ++i) {
        for (auto j = 0; j < 2; p %= res.size(), j += (res[p] == 0 ? 1 : 0)) ++p;
        res[p] = deck[i];
      }
      return res;
    }
    

Another way to look at this is to have a list of positions `l = [0, 1, ... n -
1]`. We skip the first position and take (remove) the second one. This is the
position of the next element from `deck`. We then repeat the same until we
have no more positions left. The solution below demonstrates this. The second
solution should be a bit faster since we go through all positions `2 * n`,
where in the first solution it's `n log n` (we scan through the entire array
`log n` times).

    
    
    vector<int> deckRevealedIncreasing(vector<int>& deck) {
      sort(begin(deck), end(deck));
      list<int> l(deck.size());
      iota(begin(l), end(l), 0);    
      vector<int> res(deck.size());
      auto lp = l.begin();
      for (int i = 0, skip = 0; !l.empty(); skip = !skip) {
          if (lp == l.end()) lp = l.begin();
          if (skip) ++lp;
          else {
              res[*lp] = deck[i++];
              l.erase(lp++);
          }
      }
      return res;
    }
    



