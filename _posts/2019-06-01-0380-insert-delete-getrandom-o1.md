---
layout: post
title: 380. Insert Delete GetRandom O(1)
---
### Question
Design a data structure that supports all following operations in _average_
**O(1)** time.

  1. `insert(val)`: Inserts an item val to the set if not already present.
  2. `remove(val)`: Removes an item val from the set if present.
  3. `getRandom`: Returns a random element from current set of elements. Each element must have the **same probability** of being returned.

 **Example:**

    
    
    // Init an empty set.
    RandomizedSet randomSet = new RandomizedSet();
    
    // Inserts 1 to the set. Returns true as 1 was inserted successfully.
    randomSet.insert(1);
    
    // Returns false as 2 does not exist in the set.
    randomSet.remove(2);
    
    // Inserts 2 to the set, returns true. Set now contains [1,2].
    randomSet.insert(2);
    
    // getRandom should return either 1 or 2 randomly.
    randomSet.getRandom();
    
    // Removes 1 from the set, returns true. Set now contains [2].
    randomSet.remove(1);
    
    // 2 was already in the set, so return false.
    randomSet.insert(2);
    
    // Since 2 is the only number in the set, getRandom always return 2.
    randomSet.getRandom();
    

### Solution 1
I got a similar question for my phone interview. The difference is that the
duplicated number is allowed. So, think that is a follow-up of this question.  
How do you modify your code to allow duplicated number?

    
    
    public class RandomizedSet {
        ArrayList<Integer> nums;
        HashMap<Integer, Integer> locs;
        java.util.Random rand = new java.util.Random();
        /** Initialize your data structure here. */
        public RandomizedSet() {
            nums = new ArrayList<Integer>();
            locs = new HashMap<Integer, Integer>();
        }
        
        /** Inserts a value to the set. Returns true if the set did not already contain the specified element. */
        public boolean insert(int val) {
            boolean contain = locs.containsKey(val);
            if ( contain ) return false;
            locs.put( val, nums.size());
            nums.add(val);
            return true;
        }
        
        /** Removes a value from the set. Returns true if the set contained the specified element. */
        public boolean remove(int val) {
            boolean contain = locs.containsKey(val);
            if ( ! contain ) return false;
            int loc = locs.get(val);
            if (loc < nums.size() - 1 ) { // not the last one than swap the last one with this val
                int lastone = nums.get(nums.size() - 1 );
                nums.set( loc , lastone );
                locs.put(lastone, loc);
            }
            locs.remove(val);
            nums.remove(nums.size() - 1);
            return true;
        }
        
        /** Get a random element from the set. */
        public int getRandom() {
            return nums.get( rand.nextInt(nums.size()) );
        }
    }
    


### Solution 2
We just keep track of the index of the added elements, so when we remove them,
we copy the last one into it.

From Python docs (<https://wiki.python.org/moin/TimeComplexity>) we know that
`list.append()` takes O(1), both average and amortized. Dictionary `get` and
`set` functions take O(1) average, so we are OK.

    
    
    import random
    
    class RandomizedSet(object):
    
        def __init__(self):
            self.nums, self.pos = [], {}
            
        def insert(self, val):
            if val not in self.pos:
                self.nums.append(val)
                self.pos[val] = len(self.nums) - 1
                return True
            return False
            
    
        def remove(self, val):
            if val in self.pos:
                idx, last = self.pos[val], self.nums[-1]
                self.nums[idx], self.pos[last] = last, idx
                self.nums.pop(); self.pos.pop(val, 0)
                return True
            return False
                
        def getRandom(self):
            return self.nums[random.randint(0, len(self.nums) - 1)]
    
    # 15 / 15 test cases passed.
    # Status: Accepted
    # Runtime: 144 ms
    


### Solution 3
    
    
    class RandomizedSet {
    public:
        /** Initialize your data structure here. */
        RandomizedSet() {
            
        }
        
        /** Inserts a value to the set. Returns true if the set did not already contain the specified element. */
        bool insert(int val) {
            if (m.find(val) != m.end()) return false;
            nums.emplace_back(val);
            m[val] = nums.size() - 1;
            return true;
        }
        
        /** Removes a value from the set. Returns true if the set contained the specified element. */
        bool remove(int val) {
            if (m.find(val) == m.end()) return false;
            int last = nums.back();
            m[last] = m[val];
            nums[m[val]] = last;
            nums.pop_back();
            m.erase(val);
            return true;
        }
        
        /** Get a random element from the set. */
        int getRandom() {
            return nums[rand() % nums.size()];
        }
    private:
        vector<int> nums;
        unordered_map<int, int> m;
    };
    
    



