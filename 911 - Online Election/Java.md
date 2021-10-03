## Template

```JAVA
class TopVotedCandidate {

    public TopVotedCandidate(int[] persons, int[] times) {
        
    }
    
    public int q(int t) {
        
    }
}

/**
 * Your TopVotedCandidate object will be instantiated and called as such:
 * TopVotedCandidate obj = new TopVotedCandidate(persons, times);
 * int param_1 = obj.q(t);
 */
```

<br><br>

# Approach 1 - List of Lists + Binary Search

> 109 ms	114 MB

```JAVA
class TopVotedCandidate {
    List<List<Vote>> A;
    public TopVotedCandidate(int[] persons, int[] times) {
        A = new ArrayList();
        Map<Integer, Integer> count = new HashMap();
        for (int i = 0; i < persons.length; ++i) {
            int p = persons[i], t = times[i];
            int c = count.getOrDefault(p, 0) + 1;

            count.put(p, c);
            while (A.size() <= c)
                A.add(new ArrayList<Vote>());
            A.get(c).add(new Vote(p, t));
        }
    }

    public int q(int t) {
        // Binary search on A[i][0].time for smallest i
        // such that A[i][0].time > t
        int lo = 1, hi = A.size();
        while (lo < hi) {
            int mi = lo + (hi - lo) / 2;
            if (A.get(mi).get(0).time <= t)
                lo = mi + 1;
            else
                hi = mi;
        }
        int i = lo - 1;

        // Binary search on A[i][j].time for smallest j
        // such that A[i][j].time > t
        lo = 0; hi = A.get(i).size();
        while (lo < hi) {
            int mi = lo + (hi - lo) / 2;
            if (A.get(i).get(mi).time <= t)
                lo = mi + 1;
            else
                hi = mi;
        }
        int j = Math.max(lo-1, 0);
        return A.get(i).get(j).person;
    }
}

class Vote {
    int person, time;
    Vote(int p, int t) {
        person = p;
        time = t;
    }
}
```

[ **See the Solution tab of the Question** ]
<br><br>

# Approach 2 - Precomputed Answer + Binary Search

> 100 ms	115.4 MB

```JAVA
class TopVotedCandidate {
    List<Vote> A;
    public TopVotedCandidate(int[] persons, int[] times) {
        A = new ArrayList();
        Map<Integer, Integer> count = new HashMap();
        int leader = -1;  // current leader
        int m = 0;  // current number of votes for leader

        for (int i = 0; i < persons.length; ++i) {
            int p = persons[i], t = times[i];
            int c = count.getOrDefault(p, 0) + 1;
            count.put(p, c);

            if (c >= m) {
                if (p != leader) {  // lead change
                    leader = p;
                    A.add(new Vote(leader, t));
                }

                if (c > m) m = c;
            }
        }
    }

    public int q(int t) {
        int lo = 1, hi = A.size();
        while (lo < hi) {
            int mi = lo + (hi - lo) / 2;
            if (A.get(mi).time <= t)
                lo = mi + 1;
            else
                hi = mi;
        }

        return A.get(lo - 1).person;
    }
}

class Vote {
    int person, time;
    Vote(int p, int t) {
        person = p;
        time = t;
    }
}
```

[ **See the Solution tab of the Question** ]
<br><br>

# Approach 3 - Lee's Solution

> 120 ms	116.9 MB

```JAVA
class TopVotedCandidate {
    Map<Integer, Integer> m = new HashMap<>();
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
}
```

[hello](https://leetcode.com/problems/online-election/discuss/173382/C++JavaPython-Binary-Search-in-Times)
<br><br>

# Approach 4

```JAVA
class TopVotedCandidate {
    // 68 ms	48.8 MB
    private TreeMap<Integer, Integer> tm = new TreeMap<>(); // time and leading candidat
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
}

// =========================Similar Approach=========================

class TopVotedCandidate {
    // 90 ms	117.9 MB
    private Map<Integer, Integer> map = new HashMap<>(); // time and leading candidate
    private int[] times;
    public TopVotedCandidate(int[] persons, int[] times) {
        this.times = times;
        int[] count = new int[persons.length + 1]; // count[i]: count of votes for persons[i].
        for (int i = 0, winner = -1; i < times.length; ++i) {
            ++count[persons[i]]; // at times[i], persons[i] got a vote.
            // is persons[i] leading? update winner.
            if (map.isEmpty() || count[winner] <= count[persons[i]]) { 
                winner = persons[i]; 
            } 
            map.put(times[i], winner); // update time and winner.
        }
    }
    public int q(int t) {
        int idx = Arrays.binarySearch(times, t); // search for the time slot.
        return map.get(times[idx < 0 ? -idx - 2 : idx]); // fetch the corresponding information.
    }
}
```

[hello](https://leetcode.com/problems/online-election/discuss/173387/JavaPython-3-two-methods-with-comment-using-TreeMap-and-binary-search-respectively), [there](https://leetcode.com/problems/online-election/discuss/173387/JavaPython-3-two-methods-with-comment-using-TreeMap-and-binary-search-respectively)
<br><br>

***
