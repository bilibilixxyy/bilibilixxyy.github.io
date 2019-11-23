---
layout: post
title: 332. Reconstruct Itinerary
---
### Question
Given a list of airline tickets represented by pairs of departure and arrival
airports `[from, to]`, reconstruct the itinerary in order. All of the tickets
belong to a man who departs from `JFK`. Thus, the itinerary must begin with
`JFK`.

 **Note:**

  1. If there are multiple valid itineraries, you should return the itinerary that has the smallest lexical order when read as a single string. For example, the itinerary `["JFK", "LGA"]` has a smaller lexical order than `["JFK", "LGB"]`.
  2. All airports are represented by three capital letters (IATA code).
  3. You may assume all tickets form at least one valid itinerary.

 **Example 1:**

    
    
     **Input:**[["MUC", "LHR"], ["JFK", "MUC"], ["SFO", "SJC"], ["LHR", "SFO"]]
    **Output:**["JFK", "MUC", "LHR", "SFO", "SJC"]
    

**Example 2:**

    
    
    **Input:**[["JFK","SFO"],["JFK","ATL"],["SFO","ATL"],["ATL","JFK"],["ATL","SFO"]]
    **Output:**["JFK","ATL","JFK","SFO","ATL","SFO"]
    **Explanation:** Another possible reconstruction is ["JFK","SFO","ATL","JFK","ATL","SFO"].
                 But it is larger in lexical order.
    

### Solution 1
Just Eulerian path. Greedy DFS, building the route backwards when retreating.

More explanation and example under the codes.

Iterative versions inspired by
[fangyang](https://leetcode.com/discuss/84706/share-solution-java-greedy-
stack-15ms-with-explanation) (I had only thought of recursion, d'oh).

* * *

 **Ruby**

    
    
    def find_itinerary(tickets)
      tickets = tickets.sort.reverse.group_by(&:first)
      route  = []
      visit = -> airport {
        visit[tickets[airport].pop()[1]] while (tickets[airport] || []).any?
        route << airport
      }
      visit["JFK"]
      route.reverse
    end
    

Iterative version:

    
    
    def find_itinerary(tickets)
      tickets = tickets.sort.reverse.group_by(&:first)
      route, stack = [], ["JFK"]
      while stack.any?
        stack << tickets[stack[-1]].pop()[1] while (tickets[stack[-1]] || []).any?
        route << stack.pop()
      end
      route.reverse
    end
    

* * *

**Python**

    
    
     def findItinerary(self, tickets):
        targets = collections.defaultdict(list)
        for a, b in sorted(tickets)[::-1]:
            targets[a] += b,
        route = []
        def visit(airport):
            while targets[airport]:
                visit(targets[airport].pop())
            route.append(airport)
        visit('JFK')
        return route[::-1]
    

Iterative version:

    
    
    def findItinerary(self, tickets):
        targets = collections.defaultdict(list)
        for a, b in sorted(tickets)[::-1]:
            targets[a] += b,
        route, stack = [], ['JFK']
        while stack:
            while targets[stack[-1]]:
                stack += targets[stack[-1]].pop(),
            route += stack.pop(),
        return route[::-1]
    

* * *

**Java**

    
    
    public List<String> findItinerary(String[][] tickets) {
         for (String[] ticket : tickets)
            targets.computeIfAbsent(ticket[0], k -> new PriorityQueue()).add(ticket[1]);
        visit("JFK");
        return route;
    }
    
    Map<String, PriorityQueue<String>> targets = new HashMap<>();
    List<String> route = new LinkedList();
    
    void visit(String airport) {
        while(targets.containsKey(airport) && !targets.get(airport).isEmpty())
            visit(targets.get(airport).poll());
        route.add(0, airport);
    }
    

Iterative version:

    
    
    public List<String> findItinerary(String[][] tickets) {
        Map<String, PriorityQueue<String>> targets = new HashMap<>();
        for (String[] ticket : tickets)
            targets.computeIfAbsent(ticket[0], k -> new PriorityQueue()).add(ticket[1]);
        List<String> route = new LinkedList();
        Stack<String> stack = new Stack<>();
        stack.push("JFK");
        while (!stack.empty()) {
            while (targets.containsKey(stack.peek()) && !targets.get(stack.peek()).isEmpty())
                stack.push(targets.get(stack.peek()).poll());
            route.add(0, stack.pop());
        }
        return route;
    }
    

* * *

**C++**

    
    
     vector<string> findItinerary(vector<pair<string, string>> tickets) {
        for (auto ticket : tickets)
            targets[ticket.first].insert(ticket.second);
        visit("JFK");
        return vector<string>(route.rbegin(), route.rend());
    }
    
    map<string, multiset<string>> targets;
    vector<string> route;
    
    void visit(string airport) {
        while (targets[airport].size()) {
            string next = *targets[airport].begin();
            targets[airport].erase(targets[airport].begin());
            visit(next);
        }
        route.push_back(airport);
    }
    

* * *

**Explanation**

First keep going forward until you get stuck. That's a good main path already.
Remaining tickets form cycles which are found on the way back and get merged
into that main path. By writing down the path backwards when retreating from
recursion, merging the cycles into the main path is easy - the end part of the
path has already been written, the start part of the path hasn't been written
yet, so just write down the cycle now and then keep backwards-writing the
path.

Example:

![enter image description here][1]

From JFK we first visit JFK -> A -> C -> D -> A. There we're stuck, so we
write down A as the end of the route and retreat back to D. There we see the
unused ticket to B and follow it: D -> B -> C -> JFK -> D. Then we're stuck
again, retreat and write down the airports while doing so: Write down D before
the already written A, then JFK before the D, etc. When we're back from our
cycle at D, the written route is D -> B -> C -> JFK -> D -> A. Then we retreat
further along the original path, prepending C, A and finally JFK to the route,
ending up with the route JFK -> A -> C -> D -> B -> C -> JFK -> D -> A.


### Solution 2
See also [here](http://algobox.org/reconstruct-itinerary/)

All the airports are vertices and tickets are directed edges. Then all these
tickets form a directed graph.

The graph must be Eulerian since we know that a Eulerian path exists.

Thus, start from "JFK", we can apply the Hierholzer's algorithm to find a
Eulerian path in the graph which is a valid reconstruction.

Since the problem asks for lexical order smallest solution, we can put the
neighbors in a min-heap. In this way, we always visit the smallest possible
neighbor first in our trip.

    
    
    public class Solution {
    
        Map<String, PriorityQueue<String>> flights;
        LinkedList<String> path;
    
        public List<String> findItinerary(String[][] tickets) {
            flights = new HashMap<>();
            path = new LinkedList<>();
            for (String[] ticket : tickets) {
                flights.putIfAbsent(ticket[0], new PriorityQueue<>());
                flights.get(ticket[0]).add(ticket[1]);
            }
            dfs("JFK");
            return path;
        }
    
        public void dfs(String departure) {
            PriorityQueue<String> arrivals = flights.get(departure);
            while (arrivals != null && !arrivals.isEmpty())
                dfs(arrivals.poll());
            path.addFirst(departure);
        }
    }
    
    79 / 79 test cases passed.
    Status: Accepted
    Runtime: 11 ms


### Solution 3
The nice thing about DFS is it tries a path, and if that's wrong (i.e. path
does not lead to solution), DFS goes one step back and tries another path. It
continues to do so until we've found the correct path (which leads to the
solution). You need to always bear this nice feature in mind when utilizing
DFS to solve problems.

In this problem, the path we are going to find is an itinerary which:

  1. uses all tickets to travel among airports
  2. preferably in ascending lexical order of airport code

Keep in mind that requirement 1 must be satisfied before we consider 2. If we
always choose the airport with the smallest lexical order, this would lead to
a perfectly lexical-ordered itinerary, but pay attention that when doing so,
there can be a "dead end" somewhere in the tickets such that we are not able
visit all airports (or we can't use all our tickets), which is bad because it
fails to satisfy requirement 1 of this problem. Thus we need to take a step
back and try other possible airports, which might not give us a perfectly
ordered solution, but will use all tickets and cover all airports.

Thus it's natural to think about the "backtracking" feature of DFS. We start
by building a graph and then sorting vertices in the adjacency list so that
when we traverse the graph later, we can guarantee the lexical order of the
itinerary can be as good as possible. When we have generated an itinerary, we
check if we have used all our airline tickets. If not, we revert the change
and try another ticket. We keep trying until we have used all our tickets.

    
    
    public class Solution {
        private HashMap<String, List<String>> adjList = new HashMap<>();
        private LinkedList<String> route = new LinkedList<>();
        private int numTickets = 0;
        private int numTicketsUsed = 0;
        
        public List<String> findItinerary(String[][] tickets) {
            if (tickets == null || tickets.length == 0) return route;
            // build graph
            numTickets = tickets.length;
            for (int i = 0; i < tickets.length; ++i) {
                if (!adjList.containsKey(tickets[i][0])) {
                    // create a new list
                    List<String> list = new ArrayList<>();
                    list.add(tickets[i][1]);
                    adjList.put(tickets[i][0], list);
                } else {
                    // add to existing list
                    adjList.get(tickets[i][0]).add(tickets[i][1]);
                }
            }
            // sort vertices in the adjacency list so they appear in lexical order
            for (Map.Entry<String, List<String>> entry : adjList.entrySet()) {
                Collections.sort(entry.getValue());
            }
            
            // start DFS
            route.add("JFK");
            dfsRoute("JFK");
            return route;
        }
        
        private void dfsRoute(String v) {
            // base case: vertex v is not in adjacency list
            // v is not a starting point in any itinerary, or we would have stored it
            // thus we have reached end point in our DFS
            if (!adjList.containsKey(v)) return;
            List<String> list = adjList.get(v);
            for (int i = 0; i < list.size(); ++i) {
                String neighbor = list.get(i);
                // remove ticket(route) from graph
                list.remove(i);
                route.add(neighbor);
                numTicketsUsed++;
                dfsRoute(neighbor);
                // we only return when we have used all tickets
                if (numTickets == numTicketsUsed) return;
                // otherwise we need to revert the changes and try other tickets
                list.add(i, neighbor);
                // This line took me a long time to debug
                // we must remove the last airport, since in an itinerary, the same airport can appear many times!!
                route.removeLast();
                numTicketsUsed--;
            }
        }
        
    }



