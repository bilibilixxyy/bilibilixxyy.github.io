---
layout: post
title: 980. Find the Shortest Superstring
---
### Question
Given an array A of strings, find any smallest string that contains each
string in `A` as a substring.

We may assume that no string in `A` is substring of another string in `A`.



 **Example 1:**

    
    
     **Input:** ["alex","loves","leetcode"]
    **Output:** "alexlovesleetcode"
    **Explanation:** All permutations of "alex","loves","leetcode" would also be accepted.
    

**Example 2:**

    
    
    **Input:** ["catg","ctaagt","gcta","ttca","atgcatc"]
    **Output:** "gctaagttcatgcatc"



 **Note:**

  1. `1 <= A.length <= 12`
  2. `1 <= A[i].length <= 20`

### Solution 1
[Travelling Salesman
Problem](https://en.wikipedia.org/wiki/Travelling_salesman_problem)

  1. `graph[i][j]` means the length of string to append when `A[i]` followed by `A[j]`. eg. `A[i] = abcd`, `A[j] = bcde`, then `graph[i][j] = 1`
  2. Then the problem becomes to: find the shortest path in this graph which visits every node exactly once. This is a Travelling Salesman Problem.
  3. Apply TSP DP solution. Remember to record the path.

Time complexity: `O(n^2 * 2^n)`

    
    
    class Solution {
        public String shortestSuperstring(String[] A) {
            int n = A.length;
            int[][] graph = new int[n][n];
            // build the graph
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    graph[i][j] = calc(A[i], A[j]);
                    graph[j][i] = calc(A[j], A[i]);
                }
            }
            int[][] dp = new int[1 << n][n];
            int[][] path = new int[1 << n][n];
            int last = -1, min = Integer.MAX_VALUE;
    		
            // start TSP DP
            for (int i = 1; i < (1 << n); i++) {
                Arrays.fill(dp[i], Integer.MAX_VALUE);
                for (int j = 0; j < n; j++) {
                    if ((i & (1 << j)) > 0) {
                        int prev = i - (1 << j);
                        if (prev == 0) {
                            dp[i][j] = A[j].length();
                        } else {
                            for (int k = 0; k < n; k++) {
                                if (dp[prev][k] < Integer.MAX_VALUE && dp[prev][k] + graph[k][j] < dp[i][j]) {
                                    dp[i][j] = dp[prev][k] + graph[k][j];
                                    path[i][j] = k;
                                }
                            }
                        }
                    }
                    if (i == (1 << n) - 1 && dp[i][j] < min) {
                        min = dp[i][j];
                        last = j;
                    }
                }
            }
    		
            // build the path
            StringBuilder sb = new StringBuilder();
            int cur = (1 << n) - 1;
            Stack<Integer> stack = new Stack<>();
            while (cur > 0) {
                stack.push(last);
                int temp = cur;
                cur -= (1 << last);
                last = path[temp][last];
            }
    		
            // build the result
            int i = stack.pop();
            sb.append(A[i]);
            while (!stack.isEmpty()) {
                int j = stack.pop();
                sb.append(A[j].substring(A[j].length() - graph[i][j]));
                i = j;
            }
            return sb.toString();
        }
        private int calc(String a, String b) {
            for (int i = 1; i < a.length(); i++) {
                if (b.startsWith(a.substring(i))) {
                    return b.length() - a.length() + i;
                }
            }
            return b.length();
        }
    }
    


### Solution 2
By "greedy approach" I mean: each time we merge the two strings with maximum
length of overlap, remove them from the string array, and put the merged
string into the string array.

You might also have read the [geeksforgeeks article for this
problem](https://www.geeksforgeeks.org/shortest-superstring-problem/). It
features a greedy approach and it explicitly says it is "approximate".

Consider this test case:  
["ift", "tef", "efd", "fdn", "dnete"]

The correct output is "iftefdnete" (concatenate them from left to right), and
its length is 10

However, if we apply greedy approach in this order:

  1. merge "tef" and "dnete" (overlapping length = 2) -> "dnetef" (["ift", "efd", "fdn", "dnetef"])
  2. merge "dnetef" and "efd" (overlapping length = 2) -> "dnetefd" (["ift", "fdn", "dnetefd"])
  3. merge "dnetefd" and "fdn" (overlapping length = 2) -> "dnetefdn" (["ift", "dnetefdn"])
  4. merge "ift" and "dnetefdn" (overlapping length = 0) -> "iftdnetefdn" (["iftdnetefdn"])

The output is "iftdnetefdn". Its length is 11, which makes it a wrong answer.

So I'd say the test cases are a little bit weak that so many greedy solutions
actually got AC, including the 3rd-place contender's solution for this week's
contest (well, I just want to use it as an example, nothing personal here:))


### Solution 3
 _ **1\. Formulate the problem as a graph problem**_  
Let's consider each string as a node on the graph, using their overlapping
range as a similarity measure, then the edge from string A to string B is
defined as:

    
    
    (A,B) = len(A) - overlapping(tail of A to head of B), 
    eg    A="catg" B= "atgcatc", overlapping is "atg",which is a tail part of A, and a head part of B, therefore (A,B) = 4-3 = 1
    

Notice here that we must formulate the problem setup as a digraph, as
(A,B)!=(B,A)  
eg B="atgcatc" A="catg", overlapping is "c", so (B,A) = 7-1=6

Thus we can compute a distance matrix for this graph (see code below). Notice
that the diagonal is always 0, and as this is a digraph, this matrix is
asymmetric.

_**2\. Using DP to find a minimum Hamiltonian cycle (which is in fact a
Travelling Salesman Problem)**_  
The major steps here are:  
(1) We arbitrarily select a starting node. It doesn't matter which one we
choose, as we are looking for a Hamiltonian cycle, so every node will be
included and can be used as a starting node. Here we choose node 0.  
(2) We build a path by selecting a node as an endpoint, and build it up from
there. For example,

    
    
    We want to compute a subpath containing { 2,4,5}. We have 3 candidates here:
    2 as the endpoint, compute subproblem {4,5};
    4 as the endpoint, compute subproblem {2,5};
    5 as the endpoint, compute subprobelm {2,4};
    The one with the minimum distance will be our choice.
    

Notice the base case here is simply a subpath of only one node i. In this case
we simply return the distance from our starting node(0) to node i, this is
where the pre-computed distance matrix comes in handy.  
`eg {i} = distance_matrix [0] [i]`

Also notice that I implement it as a top-down approach, a bottom up one is
equally if not more efficient.

_**3\. Convert the full tour to a path**_  
Why do we need to convert the tour to a path? This point can be subtle, as it
seems that we can simply build the superstring directly from the tour, but
there is a problem, our superstring might "wrap around" and fail to contain
all the strings. Consider this example:

    
    
    [ "catg","atgcatc"]
    The distance matrix is
    0 1
    6 0
    

And the TSP algorithm will give the result of "catgcat", as the min distance
is 7, and we indeed have 7 characters in the superstring, but do you see the
problem here? "atgcatc" misses a "c"! This is because the "c" **wraps around**
to the head of "catg". So the TSP algorithm give a correct result per se, but
we need to modify it to get the correct Hamiltonian Path for our particular
problem.

Below I will highlight the intuition, and the implementation favors clarity
rather than efficiency.

Say we've found a tour, (0,2,4,3,1,0) for and input of 5 strings.  
Now there are 5 possible shortest Hamiltonian Paths as our candidates.  
`(0,2,4,3,1), (2,4,3,1,0), (4,3,1,0,2), (3,1,0,2,4), (1,0,2,4,3).`  
The insight here is that **the length of these 5 paths may well be different**
, precisely due to **the wrap around nature** of our string problem.

    
    
    Consider this example:
    
     "catg","ttca"
    
    Both "catgttca" and "ttcatg" will be valid Hamiltonian paths, as we only have 2 nodes here. So (1,2) and (2,1) are two valid paths.
    The unmodified TSP might give us "catgtt" or "ttcatg", both of length 6. 
    

Notice that for the first one, the "ca" of "ttca" wraps around to the head of
"catg" as we explaned above, so "tcaa" is not actually contained in our
result, but it's the correct tour! The second one is the one we want. **But
depending our choice of starting node, we might get the first one.** That's
why we need this extra step to convert a tour to a correct path.  
This is, however, very straightforward, if not very efficient. We simply
construct all the possible paths from the tour, and compare their length, then
return the one with the min length. By this we solve the uncertainty with the
starting point choice. In the original TSP, it doesn't matter where the
salesman starts his journey, as it's a tour. But our superstring problem
requires this small modification.

Below is the top down approach. For clarity and debugging purposes, I use a
String to store the path. Arrays, tuples (if your language supports it) and
any other data structure that preserves ordering will be adequate.

As I said, this is not the most efficient implementation of the TSP / Shortest
Hamiltonian Path problem, but I hope this gives you an idea behind the
algorithm and the subtleties of it when applied to another problem.

    
    
    class Solution {
        int[][] distance;
        Map<String,String> memo;
        public String shortestSuperstring(String[] A) {
            if(A.length==1) return A[0];
            buildDistanceMatrix(A);
            memo = new HashMap<>();
            //build key for the final solution, key is in the form "end:set of nodes", eg "0:1,2,3,4"
            StringBuilder builder = new StringBuilder();
            for(int i=1; i<A.length; i++){
                builder.append(i);
                builder.append(",");
            }
            builder.deleteCharAt(builder.length() - 1);
            String key = "0"+":"+builder.toString();
            
            // start top-down algo, val is in the form of "sequence;minDistance", eg "0,3,4,2,1,0;20"
            String seq = TSP(key).split(";")[0];
            String res = convertToHP(seq,A);
            return res;
        }
        
        private String convertToHP(String seq, String[]A){
            String[] tour = seq.split(",");
            int N = tour.length-1;
            int[] hamPath = new int[N];
            for(int i=0; i<N; i++){
                hamPath[i] = Integer.parseInt(tour[i]);
            }
            
            int[][] pool = new int[N][N];
            pool[0] = hamPath;
            for(int i=1; i<N; i++){
                int[]candidate = new int[N];
                for(int j=i; j<i+N; j++){
                    candidate[j-i] = hamPath[j%N];
                }
                pool[i] = candidate;
            }
            int min = 1000;
            String sol = "";
            for(int[]path:pool){
                String res = buildPath(path,A);
                if(res.length()<min){
                    min = res.length();
                    sol = res;
                }
            }
            return sol;
        }
        
        private String buildPath(int[]path,String[]A){
            StringBuilder builder = new StringBuilder();
            for(int j=0; j<path.length-1; j++){
                int start = path[j];
                int end = path[j+1];
                int len = distance[start][end];
                builder.append(A[start].substring(0,len));
            }
            builder.append(A[path[path.length-1]]);
            return builder.toString();
        }
        
        private void buildDistanceMatrix(String[]A){
            int N = A.length;
            distance = new int[N][N];
            for(int i=0; i<N; i++){
                for(int j=0; j<N; j++){
                    if(i==j) continue;
                    String start = A[i];
                    String end = A[j];
                    for(int k=Math.min(start.length(),end.length()); k>0; k--){
                        if(start.endsWith(end.substring(0,k))){
                            distance[i][j] = start.length()-k;
                            break;
                        }
                        else distance[i][j] = start.length();
                    }
                }
            }
        }
        
        private String TSP(String key){
            if(memo.containsKey(key)) return memo.get(key);
            
            String[] temp = key.split(":");
            String endPointS = temp[0];
            int endPoint = Integer.parseInt(endPointS);
            
            //base case
            if(key.endsWith(":")){
                String seq = "0"+","+endPointS;
                int dist = distance[0][endPoint];
                String res = seq+";"+Integer.toString(dist);
                memo.put(key,res);
                return res;
            }
            
            String[] elements = temp[1].split(",");
            int min = 1000;
            String candidate="X";
            for(int i=0; i<elements.length; i++){
                String subKey = buildKey(elements, i);
                String[] res = TSP(subKey).split(";");
                int updatedDist = distance[Integer.parseInt(elements[i])][endPoint] + Integer.parseInt(res[1]);
                String updatedSeq = res[0] + "," + endPointS;
                if(updatedDist<min){
                    min = updatedDist;
                    candidate = updatedSeq;
                }
            }
            String val = candidate + ";" + Integer.toString(min);
            memo.put(key,val);
            return val;
        }
        
        private String buildKey(String[]s, int excl){
            String endPoint = s[excl];
            StringBuilder builder = new StringBuilder();
            builder.append(endPoint);
            builder.append(":");
            if(s.length==1) return builder.toString();
            
            for(String node:s){
                if(node.equals(endPoint))continue;
                builder.append(node);
                builder.append(",");
            }
            builder.deleteCharAt(builder.length() - 1);
            return builder.toString();
        }
    }
    



