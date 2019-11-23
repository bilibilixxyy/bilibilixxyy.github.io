---
layout: post
title: 947. Online Election
---
### Question
In an election, the `i`-th vote was cast for `persons[i]` at time `times[i]`.

Now, we would like to implement the following query function:
`TopVotedCandidate.q(int t)` will return the number of the person that was
leading the election at time `t`.  

Votes cast at time `t` will count towards our query.  In the case of a tie,
the most recent vote (among tied candidates) wins.



 **Example 1:**

    
    
     **Input:** ["TopVotedCandidate","q","q","q","q","q","q"], [[[0,1,1,0,0,1,0],[0,5,10,15,20,25,30]],[3],[12],[25],[15],[24],[8]]
    **Output:** [null,0,1,1,0,0,1]
    **Explanation:**
    At time 3, the votes are [0], and 0 is leading.
    At time 12, the votes are [0,1,1], and 1 is leading.
    At time 25, the votes are [0,1,1,0,0,1], and 1 is leading (as ties go to the most recent vote.)
    This continues for 3 more queries at time 15, 24, and 8.
    



 **Note:**

  1. `1 <= persons.length = times.length <= 5000`
  2. `0 <= persons[i] <= persons.length`
  3. `times` is a strictly increasing array with all elements in `[0, 10^9]`.
  4. `TopVotedCandidate.q` is called at most `10000` times per test case.
  5. `TopVotedCandidate.q(int t)` is always called with `t >= times[0]`.

### Solution 1
I don't understand the example that is given ... why does the input include a
bunch of strings? Why are some steps lists of lists of lists and others a list
with a single number?

I feel like the description needs to be greatly improved, and is a bit
ambiguous at the moment.


### Solution 2
 **Intuition** :  
Initialization part:

In the order of time, we count the number of votes for each person.  
Also, we update the current lead of votes for each time point.  
`if (count[person] >= count[lead]) lead = person`  
Time Complexity: `O(N)`

Query part:  
Binary search `t` in `times`, find out the latest time point no later than
`t`.  
Return the lead of votes at that time point.  
Time Complexity: `O(logN)`

 **C++:**

    
    
         map<int, int> m;
        TopVotedCandidate(vector<int> persons, vector<int> times) {
            int n = persons.size(), lead = -1;
            unordered_map<int, int> count;
            for (int i = 0; i < n; ++i) m[times[i]] = persons[i];
            for (auto it : m) {
                if (++count[it.second] >= count[lead])lead = it.second;
                m[it.first] = lead;
            }
        }
    
        int q(int t) {
            return (--m.upper_bound(t))-> second;
        }
    

**Update 2019-01-06:**  
Time limit for Java is really tight for this problem.  
My solution has runtime around 800ms ~ 1000ms.  
And sometimes it get TLE.

**Java:**

    
    
        Map< Integer, Integer> m = new HashMap<>();
        int[] time;
        public TopVotedCandidate(int[] persons, int[] times) {
            int n = persons.length, lead = -1;
            Map<Integer, Integer> count = new HashMap<>();
            time = times;
            for (int i = 0; i < n; ++i) {
                count.put(persons[i], count.getOrDefault(persons[i], 0) + 1);
                if (i == 0 || count.get(persons[i]) >= count.get(lead)) lead = persons[i];
                m.put(times[i], lead);
            }
        }
    
        public int q(int t) {
            int i = Arrays.binarySearch(time, t);
            return i < 0 ? m.get(time[-i-2]) : m.get(time[i]);
        }
    

**Python:**

    
    
        def __init__( self, persons, times):
            self.leads, self.times, count = [], times, {}
            lead = -1
            for t, p in zip(times, persons):
                count[p] = count.get(p, 0) + 1
                if count[p] >= count.get(lead, 0): lead = p
                self.leads.append(lead)
    
        def q(self, t):
            return self.leads[bisect.bisect(self.times, t) - 1]
    


### Solution 3
Method 1:  
Time complexity for constructor `TopVotedCandidate(int[] persons, int[]
times)` is O(nlogn), and for `q(int t)` is O(logn).

    
    
    private TreeMap<Integer, Integer> tm = new TreeMap<>(); // time and leading candidate
    public TopVotedCandidate(int[] persons, int[] times) {
        int[] count = new int[persons.length]; // count[i]: count of votes for persons[i].
        for (int i = 0, max = -1; i < times.length; ++i) {
            ++count[persons[i]]; // at times[i], persons[i] got a vote.
            if (max <= count[persons[i]]) { // is persons[i] leading?
                max = count[persons[i]]; // update leading count.
                tm.put(times[i], persons[i]); // update leading candidate.
            }
        }
    }
    public int q(int t) {
        return tm.floorEntry(t).getValue(); // fetch the corresponding information. 
    }
    

Mthod 2:  
HashMap.put() cost only O(1) for each operation. Therefore,  
time complexity: Constructor O(n), `q(int t)` is O(logn).

    
    
    private Map<Integer, Integer> map = new HashMap<>(); // time and leading candidate
    private int[] times;
    public TopVotedCandidate(int[] persons, int[] times) {
        this.times = times;
        int[] count = new int[persons.length + 1]; // count[i]: count of votes for persons[i].
        for (int i = 0, winner = -1; i < times.length; ++i) {
            ++count[persons[i]]; // at times[i], persons[i] got a vote.
            // is persons[i] leading? update winner.
            if (map.isEmpty() || count[winner] <= count[persons[i]]) { winner = persons[i]; } 
            map.put(times[i], winner); // update time and winner.
        }
    }
    public int q(int t) {
        int idx = Arrays.binarySearch(times, t); // search for the time slot.
        return map.get(times[idx < 0 ? -idx - 2 : idx]); // fetch the corresponding information.
    }
    



