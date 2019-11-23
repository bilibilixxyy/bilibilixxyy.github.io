---
layout: post
title: 399. Evaluate Division
---
### Question
Equations are given in the format `A / B = k`, where `A` and `B` are variables
represented as strings, and `k` is a real number (floating point number).
Given some queries, return the answers. If the answer does not exist, return
`-1.0`.

 **Example:**  
Given ` a / b = 2.0, b / c = 3.0.`  
queries are: ` a / c = ?, b / a = ?, a / e = ?, a / a = ?, x / x = ? .`  
return ` [6.0, 0.5, -1.0, 1.0, -1.0 ].`

The input is: ` vector<pair<string, string>> equations, vector<double>&
values, vector<pair<string, string>> queries `, where `equations.size() ==
values.size()`, and the values are positive. This represents the equations.
Return ` vector<double>`.

According to the example above:

    
    
    equations = [ ["a", "b"], ["b", "c"] ],
    values = [2.0, 3.0],
    queries = [ ["a", "c"], ["b", "a"], ["a", "e"], ["a", "a"], ["x", "x"] ]. 



The input is always valid. You may assume that evaluating the queries will
result in no division by zero and there is no contradiction.

### Solution 1
Image a/b = k as a link between node a and b, the weight from a to b is k, the
reverse link is 1/k. Query is to find a path between two nodes.

    
    
        public double[] calcEquation(String[][] equations, double[] values, String[][] queries) {
            HashMap<String, ArrayList<String>> pairs = new HashMap<String, ArrayList<String>>();
            HashMap<String, ArrayList<Double>> valuesPair = new HashMap<String, ArrayList<Double>>();
            for (int i = 0; i < equations.length; i++) {
                String[] equation = equations[i];
                if (!pairs.containsKey(equation[0])) {
                    pairs.put(equation[0], new ArrayList<String>());
                    valuesPair.put(equation[0], new ArrayList<Double>());
                }
                if (!pairs.containsKey(equation[1])) {
                    pairs.put(equation[1], new ArrayList<String>());
                    valuesPair.put(equation[1], new ArrayList<Double>());
                }
                pairs.get(equation[0]).add(equation[1]);
                pairs.get(equation[1]).add(equation[0]);
                valuesPair.get(equation[0]).add(values[i]);
                valuesPair.get(equation[1]).add(1/values[i]);
            }
            
            double[] result = new double[queries.length];
            for (int i = 0; i < queries.length; i++) {
                String[] query = queries[i];
                result[i] = dfs(query[0], query[1], pairs, valuesPair, new HashSet<String>(), 1.0);
                if (result[i] == 0.0) result[i] = -1.0;
            }
            return result;
        }
        
        private double dfs(String start, String end, HashMap<String, ArrayList<String>> pairs, HashMap<String, ArrayList<Double>> values, HashSet<String> set, double value) {
            if (set.contains(start)) return 0.0;
            if (!pairs.containsKey(start)) return 0.0;
            if (start.equals(end)) return value;
            set.add(start);
            
            ArrayList<String> strList = pairs.get(start);
            ArrayList<Double> valueList = values.get(start);
            double tmp = 0.0;
            for (int i = 0; i < strList.size(); i++) {
                tmp = dfs(strList.get(i), end, pairs, values, set, value*valueList.get(i));
                if (tmp != 0.0) {
                    break;
                }
            }
            set.remove(start);
            return tmp;
        }


### Solution 2
A variation of
[**Floyd–Warshall**](https://en.wikipedia.org/wiki/Floyd%E2%80%93Warshall_algorithm),
computing quotients instead of shortest paths. An equation `A/B=k` is like a
graph edge `A->B`, and `(A/B)*(B/C)*(C/D)` is like the path `A->B->C->D`.
Submitted once, accepted in 35 ms.

    
    
    def calcEquation(self, equations,  values, queries):
        quot = collections.defaultdict(dict)
        for (num, den), val in zip(equations, values):
            quot[num][num] = quot[den][den] = 1.0
            quot[num][den] = val
            quot[den][num] = 1 / val
        for k, i, j in itertools.permutations(quot, 3):
            if k in quot[i] and j in quot[k]:
                quot[i][j] = quot[i][k] * quot[k][j]
        return [quot[num].get(den, -1.0) for num, den in queries]
    

  

Variation without the `if` (submitted twice, accepted in 68 and 39 ms):

    
    
    def calcEquation(self, equations, values, queries):
        quot = collections.defaultdict(dict)
        for (num, den), val in zip(equations, values):
            quot[num][num] = quot[den][den] = 1.0
            quot[num][den] = val
            quot[den][num] = 1 / val
        for k in quot:
            for i in quot[k]:
                for j in quot[k]:
                    quot[i][j] = quot[i][k] * quot[k][j]
        return [quot[num].get(den, -1.0) for num, den in queries]
    

Could save a line with `for i, j in itertools.permutations(quot[k], 2)` but
it's longer and I don't like it as much here.


### Solution 3
Although this looks like a math problem, we can easily model it with graph.

For example:  
Given:  
a/b = 2.0, b/c = 3.0  
We can build a directed graph:  
a -- 2.0 --> b -- 3.0 --> c  
If we were asked to find a/c, we have:  
a/c = a/b * b/c = 2.0 * 3.0  
In the graph, it is the product of costs of edges.

Do notice that, 2 edges need to added into the graph with one given equation,  
because with a/b we also get result of b/a, which is the reciprocal of a/b.

so the previous example also gives edges:  
c -- 0.333 --> b -- 0.5 --> a

Now we know how to model this problem, what we need to do is simply build the  
graph with given equations, and traverse the graph, either DFS or BFS, to find
a path  
for a given query, and the result is the product of costs of edges on the
path.

One optimization, which is not implemented in the code, is to "compress" paths
for  
past queries, which will make future searches faster. This is the same idea
used in  
compressing paths in union find set. So after a query is conducted and a
result is found,  
we add two edges for this query if these edges are not already in the graph.

Given the number of variables N, and number of equations E,  
building the graph takes O(E), each query takes O(N), space for graph takes
O(E)

I think if we start to compress paths, the graph will grow to O(N^2), and we  
can optimize the query to O(1), please correct me if I'm wrong.

    
    
    class Solution(object):
        def calcEquation(self, equations, values, queries):
    
            graph = {}
            
            def build_graph(equations, values):
                def add_edge(f, t, value):
                    if f in graph:
                        graph[f].append((t, value))
                    else:
                        graph[f] = [(t, value)]
                
                for vertices, value in zip(equations, values):
                    f, t = vertices
                    add_edge(f, t, value)
                    add_edge(t, f, 1/value)
            
            def find_path(query):
                b, e = query
                
                if b not in graph or e not in graph:
                    return -1.0
                    
                q = collections.deque([(b, 1.0)])
                visited = set()
                
                while q:
                    front, cur_product = q.popleft()
                    if front == e:
                        return cur_product
                    visited.add(front)
                    for neighbor, value in graph[front]:
                        if neighbor not in visited:
                            q.append((neighbor, cur_product*value))
                
                return -1.0
            
            build_graph(equations, values)
            return [find_path(q) for q in queries]
    



