---
layout: post
title: 817. Design HashMap
---
### Question
Design a HashMap without using any built-in hash table libraries.

To be specific, your design should include these functions:

  * `put(key, value)` : Insert a (key, value) pair into the HashMap. If the value already exists in the HashMap, update the value.
  * `get(key)`: Returns the value to which the specified key is mapped, or -1 if this map contains no mapping for the key.
  * `remove(key)` : Remove the mapping for the value key if this map contains the mapping for the key.

  
 **Example:**

    
    
    MyHashMap hashMap = new MyHashMap();
    hashMap.put(1, 1);          
    hashMap.put(2, 2);         
    hashMap.get(1);            // returns 1
    hashMap.get(3);            // returns -1 (not found)
    hashMap.put(2, 1);          // update the existing value
    hashMap.get(2);            // returns 1 
    hashMap.remove(2);          // remove the mapping for 2
    hashMap.get(2);            // returns -1 (not found) 
    

  
**Note:**

  * All keys and values will be in the range of `[0, 1000000]`.
  * The number of operations will be in the range of `[1, 10000]`.
  * Please do not use the built-in HashMap library.

### Solution 1
Collisions are resolved using linked list

    
    
     class MyHashMap {
            final ListNode[] nodes = new ListNode[10000];
    
            public void put(int key, int value) {
                int i = idx(key);
                if (nodes[i] == null)
                    nodes[i] = new ListNode(-1, -1);
                ListNode prev = find(nodes[i], key);
                if (prev.next == null)
                    prev.next = new ListNode(key, value);
                else prev.next.val = value;
            }
    
            public int get(int key) {
                int i = idx(key);
                if (nodes[i] == null)
                    return -1;
                ListNode node = find(nodes[i], key);
                return node.next == null ? -1 : node.next.val;
            }
    
            public void remove(int key) {
                int i = idx(key);
                if (nodes[i] == null) return;
                ListNode prev = find(nodes[i], key);
                if (prev.next == null) return;
                prev.next = prev.next.next;
            }
    
            int idx(int key) { return Integer.hashCode(key) % nodes.length;}
    
            ListNode find(ListNode bucket, int key) {
                ListNode node = bucket, prev = null;
                while (node != null && node.key != key) {
                    prev = node;
                    node = node.next;
                }
                return prev;
            }
    
            class ListNode {
                int key, val;
                ListNode next;
    
                ListNode(int key, int val) {
                    this.key = key;
                    this.val = val;
                }
            }
        }
    

**11/03/2018 update:** remove `Bucket` class and rename `final ListNode[]
buckets = new ListNode[10000]` to `final ListNode[] nodes = new
ListNode[10000]`. Thanks to <https://leetcode.com/johnson9432>


### Solution 2
I do not think array solutions are acceptable interviews. So here is my naive
implementation of Hash with chaining. One can play with m (number of slots in
HashTable) to optimize the runtime. I got 90% with setting m = 1000.

    
    
    # using just arrays, direct access table
    # using linked list for chaining
    
    class ListNode:
        def __init__(self, key, val):
            self.pair = (key, val)
            self.next = None
    
    class MyHashMap:
    
        def __init__(self):
            """
            Initialize your data structure here.
            """
            self.m = 1000;
            self.h = [None]*self.m
            
    
        def put(self, key, value):
            """
            value will always be non-negative.
            :type key: int
            :type value: int
            :rtype: void
            """
            index = key % self.m
            if self.h[index] == None:
                self.h[index] = ListNode(key, value)
            else:
                cur = self.h[index]
                while True:
                    if cur.pair[0] == key:
                        cur.pair = (key, value) #update
                        return
                    if cur.next == None: break
                    cur = cur.next
                cur.next = ListNode(key, value)
            
    
        def get(self, key):
            """
            Returns the value to which the specified key is mapped, or -1 if this map contains no mapping for the key
            :type key: int
            :rtype: int
            """
            index = key % self.m
            cur = self.h[index]
            while cur:
                if cur.pair[0] == key:
                    return cur.pair[1]
                else:
                    cur = cur.next
            return -1
                
            
    
        def remove(self, key):
            """
            Removes the mapping of the specified value key if this map contains a mapping for the key
            :type key: int
            :rtype: void
            """
            index = key % self.m
            cur = prev = self.h[index]
            if not cur: return
            if cur.pair[0] == key:
                self.h[index] = cur.next
            else:
                cur = cur.next
                while cur:
                    if cur.pair[0] == key:
                        prev.next = cur.next
                        break
                    else:
                        cur, prev = cur.next, prev.next
                    
    
    
    # Your MyHashMap object will be instantiated and called as such:
    # obj = MyHashMap()
    # obj.put(key,value)
    # param_2 = obj.get(key)
    # obj.remove(key)
    


### Solution 3
**Solution 1: Traditional Hash-Table Implementation - Using Array of
LinkedList - Accepted in 75 ms**

  * The general implementation of `HashMap` uses `bucket` which is basically a **chain of linked lists** and each node containing `<key, value>` pair.
  * So if we have duplicate nodes, that doesn't matter - it will still replicate each key with it's value in linked list node.
  * When we insert the pair `(10, 20)` and then (`10, 30)`, there is technically no collision involved. We are just replacing the old value with the new value for a given key `10`, since in both cases, `10` is equal to `10` and also the hash code for `10` is always `10`.
  * Collision happens when multiple keys hash to the same `bucket`. In that case, we need to make sure that we can distinguish between those keys. **Chaining collision resolution** is one of those techniques which is used for this.
  * Just for the information. In JDK 8, `HashMap` has been tweaked so that if keys can be compared for ordering, then any densely-populated `bucket` is implemented as a `tree`, so that even if there are lots of entries with the same hash-code, the complexity is`O(log n)`.

Time complexity: `O(1)` average and `O(n)` worst case - for all
`get()`,`put()` and `remove()` methods.  
Space complexity: `O(n)` \- where `n` is the number of entries in HashMap

    
    
    class MyHashMap
    {
    	ListNode[] nodes = new ListNode[10000];
    
    	public int get(int key)
    	{
    		int index = getIndex(key);
    		ListNode prev = findElement(index, key);
    		return prev.next == null ? -1 : prev.next.val;
    	}
    	
    	public void put(int key, int value)
    	{
    		int index = getIndex(key);
    		ListNode prev = findElement(index, key);
    		
    		if (prev.next == null)
    			prev.next = new ListNode(key, value);
    		else 
    			prev.next.val = value;
    	}
    
    	public void remove(int key)
    	{
    		int index = getIndex(key);
            ListNode prev = findElement(index, key);
    			
            if(prev.next != null)
    		    prev.next = prev.next.next;
    	}
    
    	private int getIndex(int key)
    	{	
    		return Integer.hashCode(key) % nodes.length;
    	}
    
    	private ListNode findElement(int index, int key)
    	{
    		if(nodes[index] == null)
    			return nodes[index] = new ListNode(-1, -1);
            
            ListNode prev = nodes[index];
    		
    		while(prev.next != null && prev.next.key != key)
    		{
    			prev = prev.next;
    		}
    		return prev;
    	}
    
    	private static class ListNode
    	{
    		int key, val;
    		ListNode next;
    
    		ListNode(int key, int val)
    		{
    			this.key = key;
    			this.val = val;
    		}
    	}
    }
    

**Solution 2: Using Large Sized Array - Accepted in 100 ms**

Time complexity: `O(1)` \- for all `get()`,`put()` and `remove()` methods.  
Space complexity: `O(n)` \- where `n` is the maximum possible value for the
key.

    
    
    class MyHashMap
    {
        int[] map;
    
        public MyHashMap()
    	{
            map = new int[1000001];
            Arrays.fill(map,-1);
        }
    	
        public int get(int key)
    	{
            return map[key];
        }
        
    	public void put(int key, int value)
    	{
            map[key] = value;
        }
        
    	public void remove(int key)
    	{
            map[key] = -1;
        }
    }
    



