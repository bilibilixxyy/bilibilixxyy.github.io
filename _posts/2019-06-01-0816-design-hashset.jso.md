---
layout: post
title: 816. Design HashSet
---
### Question
Design a HashSet without using any built-in hash table libraries.

To be specific, your design should include these functions:

  * `add(value)`: Insert a value into the HashSet. 
  * `contains(value)` : Return whether the value exists in the HashSet or not.
  * `remove(value)`: Remove a value in the HashSet. If the value does not exist in the HashSet, do nothing.

  
 **Example:**

    
    
    MyHashSet hashSet = new MyHashSet();
    hashSet.add(1);         
    hashSet.add(2);         
    hashSet.contains(1);    // returns true
    hashSet.contains(3);    // returns false (not found)
    hashSet.add(2);          
    hashSet.contains(2);    // returns true
    hashSet.remove(2);          
    hashSet.contains(2);    // returns false (already removed)
    

  
**Note:**

  * All values will be in the range of `[0, 1000000]`.
  * The number of operations will be in the range of `[1, 10000]`.
  * Please do not use the built-in HashSet library.

### Solution 1
the purpose of this leetcode question is not to pass all the test. it is for
understanding how hashset or hashmap works under the hood.

So please do not post any answers using built-in hashset/hashmap, or just use
a single array. Ask yourself, are you going to do the same stupid thing if
this is the question you are asked in a real interview?


### Solution 2
A lot of people don't follow the rules and aren't interested in learning. This
implementation was based on some data structures concepts and what I've read
about Python's dict implementation.

The underlying structure that my hash set uses is a list. The list simply
contains a key at the index the key is hashed to, unless there are multiple
keys at the same index (ie a collision). More on that later...

My hashset is designed to use a limited amount of memory, but expands itself
by a factor of 2 when the load factor (size divided by array spots) exceeds
2/3. This will allow for O(1) operations on average. The downside to this is
that every time the hashset doubles, it has to create a new list and rehash
every element which takes O(n) each time. The average time will be O(1) for
each add call as the cost of rehashing the set is _amortized_ over the calls.

Open addressing and chaining each have their own advantages and disadvantages.
I'm not going to explain the nuances of the methods, but know that chaining
hash sets/tables have the head of a linked list of keys in each array spot,
while open addressing just moves the key to an empty index.

I used open addressing for this problem. If we add an element that is hashed
to the same index as another key, then we apply a secondary operation on the
index until we find an empty spot (or a previously removed spot). This is
called double hashing. The formula I used is similar to that of the Python
dict implementation, but without the perturb part. This is better than linear
probing since the hash function (which just mods the key by the capacity of
the list) is likely to fill contiguous array spots and double hashing makes
the probability of finding an empty spot more uniform in those cases than
linear probing.

For removing, the removed element is replaced by a tombstone so that the
contains function won't get messed up when the path to an existing element has
an empty spot in it, causing the contains function to return false. The add
function will know that tombstones are able to be replaced by new elements.

    
    
    class MyHashSet(object):
    
        def __init__(self):
            """
            Initialize your data structure here.
            """
            self.capacity = 8
            self.size = 0
            self.s = [None]*8
            self.lf = float(2)/3
            
        def myhash(self, key): # can be modified to hash other hashable objects like built in python hash function
            return key%self.capacity
            
    
        def add(self, key):
            """
            :type key: int
            :rtype: void
            """
            if float(self.size)/self.capacity >= self.lf:
                self.capacity <<= 1
                ns = [None]*self.capacity
                for i in range(self.capacity >> 1):
                    if self.s[i] and self.s[i] != "==TOMBSTONE==":
                        n = self.myhash(self.s[i])
                        while ns[n] is not None:
                            n = (5*n+1)%self.capacity
                        ns[n] = self.s[i]
                self.s = ns
            h = self.myhash(key)
            while self.s[h] is not None:
                if self.s[h] == key:
                    return
                h = (5*h + 1) % self.capacity
                if self.s[h] == "==TOMBSTONE==":
                    break
            self.s[h] = key
            self.size += 1
            
            
    
        def remove(self, key):
            """
            :type key: int
            :rtype: void
            """
            h = self.myhash(key)
            while self.s[h]:
                if self.s[h] == key:
                    self.s[h] = "==TOMBSTONE=="
                    self.size -= 1
                    return
                h = (5*h+1)%self.capacity
            
    
        def contains(self, key):
            """
            Returns true if this set contains the specified element
            :type key: int
            :rtype: bool
            """
            h = self.myhash(key)
            while self.s[h] is not None:
                if self.s[h] == key:
                    return True
                h = (5*h + 1)%self.capacity
            return False
    


### Solution 3
itemsPerBucket is set to 1001 to deal with the edge case when the key is 0
even though the description specify the number value ranges from 1-100k. This
will occupy 1000 additional booleans in memory.

    
    
    class MyHashSet {
    
        private int buckets = 1000;
        private int itemsPerBucket = 1001;
        private boolean[][] table;
        
        /** Initialize your data structure here. */
        public MyHashSet() {
            table = new boolean[buckets][];
        }
    
        public int hash(int key) {
            return key % buckets;
        }
    
        public int pos(int key) {
            return key / buckets;
        }
        
        public void add(int key) {
            int hashkey = hash(key);
            
            if (table[hashkey] == null) {
                table[hashkey] = new boolean[itemsPerBucket];
            }
            table[hashkey][pos(key)] = true;
        }
        
        public void remove(int key) {
            int hashkey = hash(key);
    
            if (table[hashkey] != null)
                table[hashkey][pos(key)] = false;
        }
        
        /** Returns true if this set did not already contain the specified element */
        public boolean contains(int key) {
            int hashkey = hash(key);
            return table[hashkey] != null && table[hashkey][pos(key)];
        }
    }
    



