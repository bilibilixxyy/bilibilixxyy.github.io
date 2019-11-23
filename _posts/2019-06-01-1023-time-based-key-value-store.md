---
layout: post
title: 1023. Time Based Key-Value Store
---
### Question
Create a timebased key-value store class `TimeMap`, that supports two
operations.

1\. `set(string key, string value, int timestamp)`

  * Stores the `key` and `value`, along with the given `timestamp`.

2\. `get(string key, int timestamp)`

  * Returns a value such that `set(key, value, timestamp_prev)` was called previously, with `timestamp_prev <= timestamp`.
  * If there are multiple such values, it returns the one with the largest `timestamp_prev`.
  * If there are no values, it returns the empty string (`""`).



 **Example 1:**

    
    
     **Input:** inputs = ["TimeMap","set","get","get","set","get","get"], inputs = [[],["foo","bar",1],["foo",1],["foo",3],["foo","bar2",4],["foo",4],["foo",5]]
    **Output:** [null,null,"bar","bar",null,"bar2","bar2"]
    **Explanation:**   
    TimeMap kv;   
    kv.set("foo", "bar", 1); // store the key "foo" and value "bar" along with timestamp = 1   
    kv.get("foo", 1);  // output "bar"   
    kv.get("foo", 3); // output "bar" since there is no value corresponding to foo at timestamp 3 and timestamp 2, then the only value is at timestamp 1 ie "bar"   
    kv.set("foo", "bar2", 4);   
    kv.get("foo", 4); // output "bar2"   
    kv.get("foo", 5); //output "bar2"   
    
    

**Example 2:**

    
    
    **Input:** inputs = ["TimeMap","set","set","get","get","get","get","get"], inputs = [[],["love","high",10],["love","low",20],["love",5],["love",10],["love",15],["love",20],["love",25]]
    **Output:** [null,null,null,"","high","high","low","low"]
    



 **Note:**

  1. All key/value strings are lowercase.
  2. All key/value strings have length in the range `[1, 100]`
  3. The `timestamps` for all `TimeMap.set` operations are strictly increasing.
  4. `1 <= timestamp <= 10^7`
  5. `TimeMap.set` and `TimeMap.get` functions will be called a total of `120000` times (combined) per test case.

### Solution 1
We use hash map to lookup ordered `{timestamp, value}` pairs by key in O(1).
Then, we use binary search to find the value with a timestamp less or equal
than the requested one.

    
    
    unordered_map<string, map<int, string>> m;
    void set(string key, string value, int timestamp) {
      m[key].insert({ timestamp, value });
    }
    string get(string key, int timestamp) {
      auto it = m[key].upper_bound(timestamp);
      return it == m[key].begin() ? "" : prev(it)->second;
    }
    

# Complexity analysis

Assuming `n` is the number of set operations, and `m` is the number of get
operations:

  * Time Complexity:
    * Set: `O(1)` single operation, and total `O(n)`.  
Note: assuing timestamps are only increasing. If not, it's `O(n log n)`.

    * Get: `O(log n)` for a single operation, and total `O(m log n)`.
  * Space Complexity: `O(n)` (assuming every `{ timestamp, value }` is unique).


### Solution 2
    
    
    public class TimeMap {
    
        private Map<String,TreeMap<Integer,String>> map;
    
        /** Initialize your data structure here. */
        public TimeMap() {
            map = new HashMap<>();
        }
    
        public void set(String key, String value, int timestamp) {
            if(!map.containsKey(key)) {
                map.put(key,new TreeMap<>());
            }
            map.get(key).put(timestamp,value);
        }
    
        public String get(String key, int timestamp) {
            TreeMap<Integer,String> treeMap = map.get(key);
            if(treeMap==null) {
                return "";
            }
            Integer floor = treeMap.floorKey(timestamp);
            if(floor==null) {
                return "";
            }
            return treeMap.get(floor);
        }
    }
    


### Solution 3
    
    
    class Data {
        String val;
        int time;
        Data(String val, int time) {
            this.val = val;
            this.time = time;
        }
    }
    class TimeMap {
    
        /** Initialize your data structure here. */
        Map<String, List<Data>> map;
        public TimeMap() {
            map = new HashMap<String, List<Data>>();
        }
        
        public void set(String key, String value, int timestamp) {
            if (!map.containsKey(key)) map.put(key, new ArrayList<Data>());
            map.get(key).add(new Data(value, timestamp));
        }
        
        public String get(String key, int timestamp) {
            if (!map.containsKey(key)) return "";
            return binarySearch(map.get(key), timestamp);
        }
        
        protected String binarySearch(List<Data> list, int time) {
            int low = 0, high = list.size() - 1;
            while (low < high) {
                int mid = (low + high) >> 1;
                if (list.get(mid).time == time) return list.get(mid).val;
                if (list.get(mid).time < time) {
                    if (list.get(mid+1).time > time) return list.get(mid).val;
                    low = mid + 1;
                }
                else high = mid -1;
            }
            return list.get(low).time <= time ? list.get(low).val : "";
        }
    }
    



