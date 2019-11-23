---
layout: post
title: 989. Largest Component Size by Common Factor
---
### Question
Given a non-empty array of unique positive integers `A`, consider the
following graph:

  * There are `A.length` nodes, labelled `A[0]` to `A[A.length - 1];`
  * There is an edge between `A[i]` and `A[j]` if and only if `A[i]` and `A[j]` share a common factor greater than 1.

Return the size of the largest connected component in the graph.



 **Example 1:**

    
    
     **Input:** [4,6,15,35]
    **Output:** 4
    ![](https://assets.leetcode.com/uploads/2018/12/01/ex1.png)
    

**Example 2:**

    
    
    **Input:** [20,50,9,63]
    **Output:** 2
    ![](https://assets.leetcode.com/uploads/2018/12/01/ex2.png)
    

**Example 3:**

    
    
    **Input:** [2,3,6,7,4,12,21,39]
    **Output:** 8
    ![](https://assets.leetcode.com/uploads/2018/12/01/ex3.png)
    

**Note:**

  1. `1 <= A.length <= 20000`
  2. `1 <= A[i] <= 100000`

### Solution 1
  1. Calculate all prime numbers less than `100000`.
  2. For each number in `A`, we say `A[i]`  
a. Do Prime Factorization(Brute force using the primes set in step 1), we say
the prime factor is `p`.  
b. If `p` has presented in the `primeToIndex`, union `i` and
`primeToIndex[p]`.  
c. Update `primeToIndex[p]` to `i`.

  3. Return the maximum count.

Time complexity: `O(NP)`, where `P` is the count of prime numbers < `100000`.
I think this is an upperbound time complexity.

    
    
    class Solution {
        int max = 0;
        public int largestComponentSize(int[] A) {
            boolean[] isPrime = new boolean[100001];
            Arrays.fill(isPrime, true);
            Set<Integer> primes = new HashSet<>();
            // all primes less than 100000
            for (int i = 2; i <= 100000; i++) {
                if (isPrime[i]) {
                    primes.add(i);
                    for (int j = 2; j * i <= 100000; j++) {
                        isPrime[j * i] = false;
                    }
                }
            }
            int n = A.length;
            int[] counts = new int[n];
            int[] parents = new int[n];
            int[] primeToIndex = new int[100001];
            Arrays.fill(primeToIndex, -1);
            for (int i = 0; i < n; i++) {
                parents[i] = i;
                counts[i] = 1;
            }
            for (int i = 0; i < n; i++) {
                int a = A[i];
                for (int p : primes) {
                    if (primes.contains(a)) { // Optimization
                        p = a;
                    }
                    if (a % p == 0) {
                        if (primeToIndex[p] > -1) {
                            union(parents, counts, primeToIndex[p], i);
                        }
                        primeToIndex[p] = i;
                        while (a % p == 0) {
                            a /= p;
                        }
                    }
                    if (a == 1) {
                        break;
                    }
                }
            }
            return max;
        }
        private int find(int[] parents, int a) {
            if (parents[a] != a) {
                parents[a] = find(parents, parents[a]);
            }
            return parents[a];
        }
        private void union(int[] parents, int[] counts, int a, int b) {
            int root1 = find(parents, a), root2 = find(parents, b);
            if (root1 == root2) {
                return;
            }
            int count = counts[root2] + counts[root1];
            max = Math.max(count, max);
            parents[root1] = root2;
            counts[root2] = count;
        }
    }
    


### Solution 2
Union Find template. The only additional stuff is one hashmap which is used to
convert factor to the node index in A for union.  
HashMap: key is the factor, val is the index in A  
Time complexity: O(N*sqrt(Max val of A[i]))

    
    
    class Solution {
        class UF {
            int[] parent;
            int[] size;
            int max;
            public UF (int N){
                parent = new int[N];
                size = new int[N];
                max = 1;
                for (int i = 0; i < N; i++){
                    parent[i] = i;
                    size[i] = 1;
                }
            }
            public int find(int x){
                if (x == parent[x]){
                    return x;
                }
                return parent[x] = find(parent[x]);
            }
            public void union(int x, int y){
                int rootX = find(x);
                int rootY = find(y);
                if (rootX != rootY){
                    parent[rootX] = rootY;
                    size[rootY] += size[rootX];
                    max = Math.max(max, size[rootY]);
                }
            }
        }
        public int largestComponentSize(int[] A) {
            int N = A.length;
            Map<Integer, Integer> map = new HashMap<>();// key is the factor, val is the node index
            UF uf = new UF(N);
            for (int i = 0; i < N; i++){
                int a = A[i];
                for (int j = 2; j * j <= a; j++){
                    if (a % j == 0){
                        if (!map.containsKey(j)){//this means that no index has claimed the factor yet
                            map.put(j, i);
                        }else{//this means that one index already claimed, so union that one with current
                            uf.union(i, map.get(j));
                        }
                        if (!map.containsKey(a/j)){
                            map.put(a/j, i);
                        }else{
                            uf.union(i, map.get(a/j));
                        }
                    }
                }
                if (!map.containsKey(a)){//a could be factor too. Don't miss this
                    map.put(a, i);
                }else{
                    uf.union(i, map.get(a));
                }
            }
            return uf.max;
        }
    }
    


### Solution 3
Using the input of first example,We can firstly do Prime factor decomposition
for each number in A, then we can get the map:`{2: [4, 6], 3: [6, 15], 5: [15,
35], 7: [35]})`, in which values are all multiples of the key(`must be
prime`). Then we can regard the first multiple as the root of all the
multiples, connect each multiple with the root. In this example, we connect 6
with 4(`root`), connect 15 with 6(`root`), connect 35 with 15(`root`), because
there is only one multiple in `7: [35]`, so no connection. At the end, we can
get the largest size 4, which is the result. The root of all the numbers in A
is the number `4`.

    
    
    class UnionFind(object):
        def uf(self, n):  
            self.uf = [i for i in range(n)]
            self.size = [1] * n
            
        def find(self, x):  
            while x != self.uf[x]:
                self.uf[x] = self.uf[self.uf[x]]
                x = self.uf[x]
            return self.uf[x]
    
        def union(self, x, y):  
            x_root = self.find(x)
            y_root = self.find(y)
            if x_root == y_root:
                return
            self.uf[x_root] = y_root
            self.size[y_root] += self.size[x_root]
            self.size[x_root] = 0
    
    
    class Solution(object):
        def largestComponentSize(self, A):
            """
            :type A: List[int]
            :rtype: int
            """
            def primeFactors(n):  # Prime factor decomposition
                out = set()
                while n % 2 == 0: 
                    out.add(2)
                    n //= 2
                for i in range(3, int(math.sqrt(n))+1, 2): 
                    while n % i== 0: 
                        out.add(i) 
                        n //= i 
                if n > 2: 
                    out.add(n)
                return out
            
            idx_lookup = {A[i]: i for i in range(len(A))} # in order to find idx in uf
            uf = UnionFind()
            uf.uf(len(A))
            primeAndItsMultiples = collections.defaultdict(list) # {2: [4, 6], 3: [6, 15], 5: [15, 35], 7: [35]})
            
            for i in A:
                factors = primeFactors(i)
                for f in factors:
                    primeAndItsMultiples[f].append(i)
    
            # we don't need to connect all the multiples of a prime, 
            # just use the first multiple as their root
            for idx, multiples in primeAndItsMultiples.items():
                if multiples:
                    root = multiples[0] # use the first multiple as their root
                    for node in multiples[1:]:
                        uf.union(idx_lookup[node], idx_lookup[root]) # connect node with root
                        
            return max(uf.size)
    

Inspired by `yanx1` in the comment below, `idx_lookup` and
`primeAndItsMultiples` are kind of unnecessary... all we need is a dict
primeToIndex that record the index of the element in A who has p as a prime
factor.

This allows the runtime to be reduced to `1112 ms`

    
    
    class UnionFind(object):
        def uf(self, n):  
            self.uf = [i for i in range(n)]
            self.size = [1] * n
            
        def find(self, x):  
            while x != self.uf[x]:
                self.uf[x] = self.uf[self.uf[x]]
                x = self.uf[x]
            return self.uf[x]
    
        def union(self, x, y):  
            x_root = self.find(x)
            y_root = self.find(y)
            if x_root == y_root:
                return
            self.uf[x_root] = y_root
            self.size[y_root] += self.size[x_root]
            self.size[x_root] = 0
    
    
    class Solution(object):
        def largestComponentSize(self, A):
            """
            :type A: List[int]
            :rtype: int
            """
            def primeFactors(n):  # Prime factor decomposition
                out = set()
                while n % 2 == 0: 
                    out.add(2)
                    n //= 2
                for i in range(3, int(math.sqrt(n))+1, 2): 
                    while n % i== 0: 
                        out.add(i) 
                        n //= i 
                if n > 2: 
                    out.add(n)
                return out
            
            uf = UnionFind()
            uf.uf(len(A))
            
            primeToIndex = {} 
            for i,a in enumerate(A):
                primes = primeFactors(a)
                for p in primes:
                    if p in primeToIndex:
                        uf.union(i, primeToIndex[p])
                    primeToIndex[p] = i
            return max(uf.size)
    

You can also check the solution [here](https://github.com/apachecn/awesome-
algorithm/blob/master/docs/Leetcode_Solutions/Python/952._Largest_Component_Size_by_Common_Factor.md).



