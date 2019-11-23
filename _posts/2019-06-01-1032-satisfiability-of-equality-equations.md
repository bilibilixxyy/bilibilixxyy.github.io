---
layout: post
title: 1032. Satisfiability of Equality Equations
---
### Question
Given an array equations of strings that represent relationships between
variables, each string `equations[i]` has length `4` and takes one of two
different forms: `"a==b"` or `"a!=b"`.  Here, `a` and `b` are lowercase
letters (not necessarily different) that represent one-letter variable names.

Return `true` if and only if it is possible to assign integers to variable
names so as to satisfy all the given equations.



 **Example 1:**

    
    
     **Input:** ["a==b","b!=a"]
    **Output:** false
    **Explanation:** If we assign say, a = 1 and b = 1, then the first equation is satisfied, but not the second.  There is no way to assign the variables to satisfy both equations.
    

**Example 2:**

    
    
    **Input:** ["b==a","a==b"]
    **Output:** true
    **Explanation:** We could assign a = 1 and b = 1 to satisfy both equations.
    

**Example 3:**

    
    
    **Input:** ["a==b","b==c","a==c"]
    **Output:** true
    

**Example 4:**

    
    
    **Input:** ["a==b","b!=c","c==a"]
    **Output:** false
    

**Example 5:**

    
    
    **Input:** ["c==c","b==d","x!=z"]
    **Output:** true
    



 **Note:**

  1. `1 <= equations.length <= 500`
  2. `equations[i].length == 4`
  3. `equations[i][0]` and `equations[i][3]` are lowercase letters
  4. `equations[i][1]` is either `'='` or `'!'`
  5. `equations[i][2]` is `'='`

### Solution 1
 **Intuition** :  
We have 26 nodes in the graph.  
All "==" equations actually represent the connection in the graph.  
The connected nodes should be in the same color/union/set.

Then we check all inequations.  
Two inequal nodes should be in the different color/union/set.

 **Explanation**  
We can solve this problem by DFS or Union Find.

Firt pass all "==" equations.  
Union equal letters together  
Now we know which letters must equal to the others.

Second pass all "!=" inequations,  
Check if there are any contradict happens.

 **Time Complexity** :  
Union Find Operation, amortized `O(1)`  
First pass all equations, `O(N)`  
Second pass all inequations, `O(N)`

Overall `O(N)`

  

 **Java:**

    
    
         int[] uf = new int[26];
        public boolean equationsPossible(String[] equations) {
            for (int i = 0; i < 26; ++i) uf[i] = i;
            for (String e : equations)
                if (e.charAt(1) == '=')
                    uf[find(e.charAt(0) - 'a')] = find(e.charAt(3) - 'a');
            for (String e : equations)
                if (e.charAt(1) == '!' && find(e.charAt(0) - 'a') == find(e.charAt(3) - 'a'))
                    return false;
            return true;
        }
    
        public int find(int x) {
            if (x != uf[x]) uf[x] = find(uf[x]);
            return uf[x];
        }
    

**C++:**

    
    
         int uf[26];
        bool equationsPossible(vector<string>& equations) {
            for (int i = 0; i < 26; ++i) uf[i] = i;
            for (string e : equations)
                if (e[1] == '=')
                    uf[find(e[0] - 'a')] = find(e[3] - 'a');
            for (string e : equations)
                if (e[1] == '!' && find(e[0] - 'a') == find(e[3] - 'a'))
                    return false;
            return true;
        }
    
        int find(int x) {
            if (x != uf[x]) uf[x] = find(uf[x]);
            return uf[x];
        }
    

**Python:**

    
    
        def equationsPossible(self, equations):
            def  find(x):
                if x != uf[x]: uf[x] = find(uf[x])
                return uf[x]
            uf = {a: a for a in string.lowercase}
            for a, e, _, b in equations:
                if e == "=":
                    uf[find(a)] = find(b)
            return not any(e == "!" and find(a) == find(b) for a, e, _, b in equations)
    


### Solution 2
    
    
    class Solution
    {
        vector<bool> visited;
        vector<int> color;
        vector<vector<int>> adj;
        
    public:
        void dfsVisit(int u, int colorNumber);
        bool equationsPossible(vector<string>& equations);
    };
    
    /* Performs the dfs and assigns the same colorNumber to each element in the connected component */
    void Solution :: dfsVisit(int u, int colorNumber)
    {
        // Visit this vertex and assign the same color number as its parent
        visited[u] = true;
        color[u] = colorNumber;
        
        // Traverse the adjaceny list and perform dfs on each element with the same color number
        for(auto v : adj[u])
            if(!visited[v]) dfsVisit(v,colorNumber);
    }
    
    /* Returns true if the equation is possible */
    bool Solution :: equationsPossible(vector<string>& equations)
    {
        // The number of nodes in the graph
        int alphabetSize = 26;
        
        // Resize the adjacency list, visited vector and color vector vector according to number of nodes
        adj.resize(alphabetSize);
        color.resize(alphabetSize);
        visited.resize(alphabetSize);
        
        // Traverse the vector of queries and focus on all the equality relations
        for(auto str: equations)
        {
            // If they are equal, then add an undirected edge between them
            if(str[1]=='=')
            {
                // Extract the letter
                char firstLetter  = str[0];
                char secondLetter = str[3];
                
                // Add undirected edge, since a==b implies b==a.There might be multi edges, but dfs would still work.
                adj[firstLetter-'a'].push_back(secondLetter-'a');
                adj[secondLetter-'a'].push_back(firstLetter-'a');
            }
        }
        
        // Assign same colours to each node in the same connected component
        // Use one color for ecah connected component
        int colorNumber = 0;
        for(int u=0; u<alphabetSize; u++)
        {
            // Visit the vertex if not already visited
            if(!visited[u]) dfsVisit(u,colorNumber);
            
            // Note that after it comes out of the if condition, all vertices in same c-component have been coloured.
            // Preapare a different colur for the next connnected component
            colorNumber ++;
        }
        
        // Traverse the query vector and check the inequality conditons violate equality conditions
        for(auto str : equations)
        {
            // Only check the inequality conditions
            if(str[1]=='!')
            {
                // Extract the letter
                char firstLetter  = str[0];
                char secondLetter = str[3];
                
                // Since, they are unequal, their color must be different
                if(color[firstLetter-'a'] == color[secondLetter-'a']) return false;
            }
        }
        
        // If it reaches here, it means no inequality violation happens.
        // It means we can satisfy the equation by assigning the colorNumber to each node
        return true;
    }
    
    


### Solution 3
Bascially we are making a graph.  
If `a == b` we will have two edges: `a->b` and `b->a`.  
After we construct the graph, we check all the `x != y`  
and make sure they are not able to visit each other.

    
    
    class Solution:
        def equationsPossible(self, equations: 'List[str]') -> 'bool':
            graph = collections.defaultdict(set)
            notEquals = []
            
            def canMeet(u, target, visited):
                if u == target:
                    return True 
                visited.add(u)
                for v in graph[u]:
                    if v in visited:
                        continue
                    if canMeet(v, target, visited):
                        return True 
                return False    
            
            for eq in equations:
                if eq[1:3] == '!=':
                    a, b = eq.split('!=')
                    notEquals.append((a, b))
                    continue
                u, v = eq.split('==')    
                graph[u].add(v)
                graph[v].add(u)
                    
            for u, v in notEquals:
                if canMeet(u, v, set()):
                    return False
            return True  
    



