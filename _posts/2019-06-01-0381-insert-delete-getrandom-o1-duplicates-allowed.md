---
layout: post
title: 381. Insert Delete GetRandom O(1) - Duplicates allowed
---
### Question
Design a data structure that supports all following operations in _average_
**O(1)** time.

 **Note: Duplicate elements are allowed.**

  1. `insert(val)`: Inserts an item val to the collection.
  2. `remove(val)`: Removes an item val from the collection if present.
  3. `getRandom`: Returns a random element from current collection of elements. The probability of each element being returned is **linearly related** to the number of same value the collection contains.

 **Example:**

    
    
    // Init an empty collection.
    RandomizedCollection collection = new RandomizedCollection();
    
    // Inserts 1 to the collection. Returns true as the collection did not contain 1.
    collection.insert(1);
    
    // Inserts another 1 to the collection. Returns false as the collection contained 1. Collection now contains [1,1].
    collection.insert(1);
    
    // Inserts 2 to the collection, returns true. Collection now contains [1,1,2].
    collection.insert(2);
    
    // getRandom should return 1 with the probability 2/3, and returns 2 with the probability 1/3.
    collection.getRandom();
    
    // Removes 1 from the collection, returns true. Collection now contains [1,2].
    collection.remove(1);
    
    // getRandom should return 1 and 2 both equally likely.
    collection.getRandom();
    

### Solution 1
There are two data member in the solution

  1. a vector **nums**
  2. an unordered_map **m**

The **key** of **m** is the **val** , the **value** of **m** is a vector
contains indices where the **val** appears in **nums**.  
Each element of **nums** is a pair, the first element of the pair is **val**
itself, the second element of the pair is an index into **m[val]**.  
There is a relationship between **nums** and **m** :

 **m[nums[i].first][nums[i].second] == i;**

    
    
     class RandomizedCollection {
    public:
        /** Initialize your data structure here. */
        RandomizedCollection() {
            
        }
        
        /** Inserts a value to the collection. Returns true if the collection did not already contain the specified element. */
        bool insert(int val) {
            auto result = m.find(val) == m.end();
            
            m[val].push_back(nums.size());
            nums.push_back(pair<int, int>(val, m[val].size() - 1));
            
            return result;
        }
        
        /** Removes a value from the collection. Returns true if the collection contained the specified element. */
        bool remove(int val) {
            auto result = m.find(val) != m.end();
            if(result)
            {
                auto last = nums.back();
                m[last.first][last.second] = m[val].back();
                nums[m[val].back()] = last;
                m[val].pop_back();
                if(m[val].empty()) m.erase(val);
                nums.pop_back();
            }
            return result;
        }
        
        /** Get a random element from the collection. */
        int getRandom() {
            return nums[rand() % nums.size()].first;
        }
    private:
        vector<pair<int, int>> nums;
        unordered_map<int, vector<int>> m;
    };
    


### Solution 2
See my previous post [here](https://discuss.leetcode.com/topic/53216/java-
solution-using-a-hashmap-and-an-arraylist-along-with-a-follow-up-131-ms/4).  
I modified the code by replacing HashSet with LinkedHashSet because the
set.iterator() might be costly when a number has too many duplicates. Using
LinkedHashSet can be considered as O(1) if we only get the first element to
remove.

    
    
    public class RandomizedCollection {
        ArrayList<Integer> nums;
    	HashMap<Integer, Set<Integer>> locs;
    	java.util.Random rand = new java.util.Random();
        /** Initialize your data structure here. */
        public RandomizedCollection() {
            nums = new ArrayList<Integer>();
    	    locs = new HashMap<Integer, Set<Integer>>();
        }
        
        /** Inserts a value to the collection. Returns true if the collection did not already contain the specified element. */
        public boolean insert(int val) {
            boolean contain = locs.containsKey(val);
    	    if ( ! contain ) locs.put( val, new LinkedHashSet<Integer>() ); 
    	    locs.get(val).add(nums.size());        
    	    nums.add(val);
    	    return ! contain ;
        }
        
        /** Removes a value from the collection. Returns true if the collection contained the specified element. */
        public boolean remove(int val) {
            boolean contain = locs.containsKey(val);
    	    if ( ! contain ) return false;
    	    int loc = locs.get(val).iterator().next();
    	    locs.get(val).remove(loc);
    	    if (loc < nums.size() - 1 ) {
    	       int lastone = nums.get( nums.size()-1 );
    	       nums.set( loc , lastone );
    	       locs.get(lastone).remove( nums.size()-1);
    	       locs.get(lastone).add(loc);
    	    }
    	    nums.remove(nums.size() - 1);
    	   
    	    if (locs.get(val).isEmpty()) locs.remove(val);
    	    return true;
        }
        
        /** Get a random element from the collection. */
        public int getRandom() {
            return nums.get( rand.nextInt(nums.size()) );
        }
    }
    


### Solution 3
    
    
    import random
    
    class RandomizedCollection(object):
    
        def __init__(self):
            self.vals, self.idxs = [], collections.defaultdict(set)
            
    
        def insert(self, val):
            self.vals.append(val)
            self.idxs[val].add(len(self.vals) - 1)
            return len(self.idxs[val]) == 1
            
    
        def remove(self, val):
            if self.idxs[val]:
                out, ins = self.idxs[val].pop(), self.vals[-1]
                self.vals[out] = ins
                if self.idxs[ins]:
                    self.idxs[ins].add(out)
                    self.idxs[ins].discard(len(self.vals) - 1)
                self.vals.pop()
                return True
            return False 
    
        def getRandom(self):
            return random.choice(self.vals)



