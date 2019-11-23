---
layout: post
title: 347. Top K Frequent Elements
---
### Question
Given a non-empty array of integers, return the **_k_** most frequent
elements.

 **Example 1:**

    
    
     **Input:** nums = [1,1,1,2,2,3], k = 2
    **Output:** [1,2]
    

**Example 2:**

    
    
    **Input:** nums = [1], k = 1
    **Output:** [1]

 **Note:**

  * You may assume _k_ is always valid, 1 ≤ _k_ ≤ number of unique elements.
  * Your algorithm's time complexity **must be** better than O( _n_ log _n_ ), where _n_ is the array's size.

### Solution 1
Idea is simple. Build a array of list to be buckets with length 1 to sort.

    
    
    public List<Integer> topKFrequent(int[] nums, int k) {
    
    	List<Integer>[] bucket = new List[nums.length + 1];
    	Map<Integer, Integer> frequencyMap = new HashMap<Integer, Integer>();
    
    	for (int n : nums) {
    		frequencyMap.put(n, frequencyMap.getOrDefault(n, 0) + 1);
    	}
    
    	for (int key : frequencyMap.keySet()) {
    		int frequency = frequencyMap.get(key);
    		if (bucket[frequency] == null) {
    			bucket[frequency] = new ArrayList<>();
    		}
    		bucket[frequency].add(key);
    	}
    
    	List<Integer> res = new ArrayList<>();
    
    	for (int pos = bucket.length - 1; pos >= 0 && res.size() < k; pos--) {
    		if (bucket[pos] != null) {
    			res.addAll(bucket[pos]);
    		}
    	}
    	return res;
    }


### Solution 2
    
    
    // use an array to save numbers into different bucket whose index is the frequency
    public class Solution {
        public List<Integer> topKFrequent(int[] nums, int k) {
            Map<Integer, Integer> map = new HashMap<>();
            for(int n: nums){
                map.put(n, map.getOrDefault(n,0)+1);
            }
            
            // corner case: if there is only one number in nums, we need the bucket has index 1.
            List<Integer>[] bucket = new List[nums.length+1];
            for(int n:map.keySet()){
                int freq = map.get(n);
                if(bucket[freq]==null)
                    bucket[freq] = new LinkedList<>();
                bucket[freq].add(n);
            }
            
            List<Integer> res = new LinkedList<>();
            for(int i=bucket.length-1; i>0 && k>0; --i){
                if(bucket[i]!=null){
                    List<Integer> list = bucket[i]; 
                    res.addAll(list);
                    k-= list.size();
                }
            }
            
            return res;
        }
    }
    
    
    
    // use maxHeap. Put entry into maxHeap so we can always poll a number with largest frequency
    public class Solution {
        public List<Integer> topKFrequent(int[] nums, int k) {
            Map<Integer, Integer> map = new HashMap<>();
            for(int n: nums){
                map.put(n, map.getOrDefault(n,0)+1);
            }
               
            PriorityQueue<Map.Entry<Integer, Integer>> maxHeap = 
                             new PriorityQueue<>((a,b)->(b.getValue()-a.getValue()));
            for(Map.Entry<Integer,Integer> entry: map.entrySet()){
                maxHeap.add(entry);
            }
            
            List<Integer> res = new ArrayList<>();
            while(res.size()<k){
                Map.Entry<Integer, Integer> entry = maxHeap.poll();
                res.add(entry.getKey());
            }
            return res;
        }
    }
    
    
    
    // use treeMap. Use freqncy as the key so we can get all freqencies in order
    public class Solution {
        public List<Integer> topKFrequent(int[] nums, int k) {
            Map<Integer, Integer> map = new HashMap<>();
            for(int n: nums){
                map.put(n, map.getOrDefault(n,0)+1);
            }
            
            TreeMap<Integer, List<Integer>> freqMap = new TreeMap<>();
            for(int num : map.keySet()){
               int freq = map.get(num);
               if(!freqMap.containsKey(freq)){
                   freqMap.put(freq, new LinkedList<>());
               }
               freqMap.get(freq).add(num);
            }
            
            List<Integer> res = new ArrayList<>();
            while(res.size()<k){
                Map.Entry<Integer, List<Integer>> entry = freqMap.pollLastEntry();
                res.addAll(entry.getValue());
            }
            return res;
        }
    }


### Solution 3
    
    
    class Solution {
    public:
        vector<int> topKFrequent(vector<int>& nums, int k) {
            unordered_map<int,int> map;
            for(int num : nums){
                map[num]++;
            }
            
            vector<int> res;
            // pair<first, second>: first is frequency,  second is number
            priority_queue<pair<int,int>> pq; 
            for(auto it = map.begin(); it != map.end(); it++){
                pq.push(make_pair(it->second, it->first));
                if(pq.size() > (int)map.size() - k){
                    res.push_back(pq.top().second);
                    pq.pop();
                }
            }
            return res;
        }
    };



