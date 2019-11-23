---
layout: post
title: 931. Maximum Frequency Stack
---
### Question
Implement `FreqStack`, a class which simulates the operation of a stack-like
data structure.

`FreqStack` has two functions:

  * `push(int x)`, which pushes an integer `x` onto the stack.
  * `pop()`, which **removes** and returns the most frequent element in the stack. 
    * If there is a tie for most frequent element, the element closest to the top of the stack is removed and returned.



 **Example 1:**

    
    
     **Input:**
     ["FreqStack","push","push","push","push","push","push","pop","pop","pop","pop"],
    [[],[5],[7],[5],[7],[4],[5],[],[],[],[]]
    **Output:** [null,null,null,null,null,null,null,5,7,5,4]
    **Explanation** :
    After making six .push operations, the stack is [5,7,5,7,4,5] from bottom to top.  Then:
    
    pop() -> returns 5, as 5 is the most frequent.
    The stack becomes [5,7,5,7,4].
    
    pop() -> returns 7, as 5 and 7 is the most frequent, but 7 is closest to the top.
    The stack becomes [5,7,5,4].
    
    pop() -> returns 5.
    The stack becomes [5,7,4].
    
    pop() -> returns 4.
    The stack becomes [5,7].
    



 **Note:**

  * Calls to `FreqStack.push(int x)` will be such that `0 <= x <= 10^9`.
  * It is guaranteed that `FreqStack.pop()` won't be called if the stack has zero elements.
  * The total number of `FreqStack.push` calls will not exceed `10000` in a single test case.
  * The total number of `FreqStack.pop` calls will not exceed `10000` in a single test case.
  * The total number of `FreqStack.push` and `FreqStack.pop` calls will not exceed `150000` across all test cases.

### Solution 1
Hash map `freq` will count the frequence of elements.  
Hash map `m` is a map of stack.  
If element `x` has n frequence, we will push `x` n times in `m[1], m[2] ..
m[n]`  
`maxfreq` records the maximum frequence.

`push(x)` will push `x` to`m[++freq[x]]`  
`pop()` will pop from the `m[maxfreq]`

 **C++:**

    
    
        unordered_map< int, int> freq;
        unordered_map<int, stack<int>> m;
        int maxfreq = 0;
    
        void push(int x) {
            maxfreq = max(maxfreq, ++freq[x]);
            m[freq[x]].push(x);
        }
    
        int pop() {
            int x = m[maxfreq].top();
            m[maxfreq].pop();
            if (!m[freq[x]--].size()) maxfreq--;
            return x;
        }
    

**Java:**

    
    
     class FreqStack {
        HashMap<Integer, Integer> freq = new HashMap<>();
        HashMap<Integer, Stack<Integer>> m = new HashMap<>();
        int maxfreq = 0;
    
        public void push(int x) {
            int f = freq.getOrDefault(x, 0) + 1;
            freq.put(x, f);
            maxfreq = Math.max(maxfreq, f);
            if (!m.containsKey(f)) m.put(f, new Stack<Integer>());
            m.get(f).add(x);
        }
    
        public int pop() {
            int x = m.get(maxfreq).pop();
            freq.put(x, maxfreq - 1);
            if (m.get(maxfreq).size() == 0) maxfreq--;
            return x;
        }
    }
    

**Python:**

    
    
         def __init__(self):
            self.freq = collections.Counter()
            self.m = collections.defaultdict(list)
            self.maxf = 0
    
        def push(self, x):
            freq, m = self.freq, self.m
            freq[x] += 1
            self.maxf = max(self.maxf, freq[x])
            m[freq[x]].append(x)
    
        def pop(self):
            freq, m, maxf = self.freq, self.m, self.maxf
            x = m[maxf].pop()
            if not m[maxf]: self.maxf = maxf - 1
            freq[x] -= 1
            return x
    


### Solution 2
    
    
    class FreqStack {
    
        List<Stack<Integer>> bucket;
        HashMap<Integer, Integer> map;
        
        public FreqStack() {
            bucket = new ArrayList<>();
            map = new HashMap<>();
        }
        
        public void push(int x) {
            map.put(x, map.getOrDefault(x, 0) + 1);
            int freq = map.get(x);
            if (freq - 1 >= bucket.size()) {
                bucket.add(new Stack<Integer>());
            }
            bucket.get(freq - 1).add(x);
        }
        
        public int pop() {
            int freq = bucket.size();
            int x = bucket.get(freq - 1).pop();
            if (bucket.get(freq - 1).isEmpty()) bucket.remove(bucket.size() - 1);
            
            map.put(x, map.get(x) - 1);
            if (map.get(x) == 0) map.remove(x);
            
            return x;
        }
    }
    


### Solution 3
    
    
    class FreqStack {
        int pushCount;
        PriorityQueue<Node> pq;
        Map<Integer, Integer> map;
        public FreqStack() {
            pushCount = 0;
            pq = new PriorityQueue<Node>(new NodeComparator());
            map = new HashMap<Integer, Integer>();
        }
        
        public void push(int x) {
            if (!map.containsKey(x)){
                map.put(x, 0);
            }
            map.put(x, map.get(x) + 1);
            Node n = new Node(x, map.get(x), pushCount++);
            pq.offer(n);
        }
        
        public int pop() {
            return pq.poll().value;
        }
        
        
        class Node{
            public int value;
            int freq;
            int pushIndex;
            public Node(int v, int f, int p){
                this.value = v;
                this.freq = f;
                this.pushIndex = p;
            }
        }
        
        class NodeComparator implements Comparator<Node>{
            public int compare(Node a, Node b){
                if (a.freq != b.freq) return b.freq -a.freq;
                        return b.pushIndex - a.pushIndex;
    
            }
        }
    }```



