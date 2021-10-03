# Approach 1 - DP

> 2-nd version : Leetcode.ca

```JAVA
class Solution {
    // 26 ms	36.1 MB
    public int knightDialer(int N) {
        int MOD = 1_000_000_007;
        int[][] moves = new int[][]{
            {4,6},{6,8},{7,9},{4,8},{3,9,0},
            {},{1,7,0},{2,6},{1,3},{2,4}};

        int[][] dp = new int[2][10];
        Arrays.fill(dp[0], 1);
        for (int hops = 0; hops < N-1; ++hops) {
            Arrays.fill(dp[~hops & 1], 0);
            for (int node = 0; node < 10; ++node)
                for (int nei: moves[node]) {
                    dp[~hops & 1][nei] += dp[hops & 1][node];
                    dp[~hops & 1][nei] %= MOD;
                }
        }

        long ans = 0;
        for (int x: dp[~N & 1])
            ans += x;
        return (int) (ans % MOD);
    }
}

// =========================Similar Approach=========================

class Solution {
    // 54 ms	38.7 MB
    public int knightDialer(int N) {
        final int MODULO = 1000000007;
        Map<Integer, int[]> adjacentMap = new HashMap<Integer, int[]>();
        adjacentMap.put(0, new int[]{4, 6});
        adjacentMap.put(1, new int[]{6, 8});
        adjacentMap.put(2, new int[]{7, 9});
        adjacentMap.put(3, new int[]{4, 8});
        adjacentMap.put(4, new int[]{0, 3, 9});
        adjacentMap.put(5, new int[0]);
        adjacentMap.put(6, new int[]{0, 1, 7});
        adjacentMap.put(7, new int[]{2, 6});
        adjacentMap.put(8, new int[]{1, 3});
        adjacentMap.put(9, new int[]{2, 4});
        int[][] dp = new int[N][10];
        for (int i = 0; i < 10; i++)
            dp[0][i] = 1;
        for (int i = 1; i < N; i++) {
            for (int j = 0; j < 10; j++) {
                int[] adjacents = adjacentMap.get(j);
                for (int adjacent : adjacents)
                    dp[i][j] = (dp[i][j] + dp[i - 1][adjacent]) % MODULO;
            }
        }
        int sum = 0;
        for (int i = 0; i < 10; i++)
            sum = (sum + dp[N - 1][i]) % MODULO;
        return sum;
    }
}

// =========================Similar Approach=========================

class Solution {
    // 43 ms	38.8 MB
    // https://leetcode.com/problems/knight-dialer/discuss/189287/O(n)-time-O(1)-space-DP-solution-+-Google-interview-question-writeup/701651
    public int knightDialer(int n) {
        int MOD = 1000000007;
        int paths[][] = {{4, 6}, {6, 8}, {7, 9}, {4, 8}, {0, 3, 9}, {}, {0, 1, 7}, {2, 6}, {1, 3}, {2, 4}}; // Previous moves of knight-> For instance, if a knight is at 0, it reached from either 4 or 6. Similarly if it is at 1, it is reached from 7 or 9  & so on
        double dp[][] = new double[n + 1][10]; // rows -> no of steps taken to reach row i      cols-> no of digits
        for (int j = 0; j < 10; j++)
            dp[1][j] = 1; //populate the base case for n =1
        for (int i = 2; i < n + 1; i++) { // no of steps taken by knight to reach i
            for (int j = 0; j < 10; j++) { // no of digits
                for (int p : paths[j]) { // Previous move of knight in order to reach digit j
                    dp[i][j] += dp[i - 1][p]; // cumulatively add from the previous knight move. For instance., F(2, 0) -> F(1,4) +  F(1,6) F(2,6) -> F(1,0) + F(1,1) + F(1,7)
                }
                dp[i][j] %= MOD;
            }
        }
        double sum = 0d;
        for (int j = 0; j < 10; j++)
            sum += dp[n][j];
        return (int) (sum % MOD);
    }
}
```

[hello](https://leetcode.libaoj.in/knight-dialer.html), [there](https://leetcode.ca/2018-06-22-935-Knight-Dialer/)
<br><br>

# Approach 2

> 60 ms	38 MB
<br><br>
> From Fishercoder1534

```JAVA
public class Solution {
    /*
     * The intuition is to calculate the number of ways
     * we can reach a key k after i hops, based on the number of ways we can reach keys x after i-1 hops
     * s.t. the knight can move from x to k in one move
     * For example,
     * We can reach 6 in 3 ways after 1 hop (1 -> 6, 7 -> 6 or 0 -> 6)
     * We can reach 8 in 2 ways after 1 hop (1 -> 8 or 3 -> 8)
     * Thus, we can reach 1 in 5 ways after 2 hops:
     * . 1. 1 -> 6 -> 1
     * . 2. 7 -> 6 -> 1
     * . 3. 0 -> 6 -> 1
     *   4. 1 -> 8 -> 1
     *   5. 3 -> 8 -> 1
     */
    private static final int MOD = 1000_000_007;

    // whereFromHere[i] is an array of keys that can be reached from the ith digit
    private static final int[][] whereFromHere = {
            {4, 6}, {6, 8}, {7, 9}, {4, 8},  // 0, 1, 2, 3
            {3, 9, 0}, {}, {1, 7, 0},          // 4, 5, 6
            {2, 6}, {1, 3}, {2, 4}              // 7, 8, 9
    };

    public int knightDialer(int N) {
        // a[i] is the number of ways we can end up on the ith digit
        // The initial array is for N = 1, i.e. for 0 hops.
        long[] a = new long[]{1, 1, 1, 1, 1, 1, 1, 1, 1, 1};

        // Simulate N - 1 hops
        for (int i = 0; i < N - 1; ++i) {
            long[] tmp = new long[10];

            // For each digit
            for (int j = 0; j < 10; j++) {
                // Which other digits can we reach?
                for (int k : whereFromHere[j]) {
                    tmp[j] = (tmp[j] + a[k]) % MOD;
                }
            }

            // Sanity checks based on symmetry of the keypad
            assert tmp[1] == tmp[3];
            assert tmp[4] == tmp[6];
            assert tmp[7] == tmp[9];

            a = tmp;
        }

        long ans = 0;
        for (long k : a) {
            ans = (ans + k) % MOD;
        }

        return (int) ans;
    }
}
```

[hello](https://github.com/fishercoder1534/Leetcode/blob/master/src/main/java/com/fishercoder/solutions/_935.java)
<br><br>

# Approach 3

```JAVA
public class Solution {
    // 276 ms	41.3 MB
    public static final int max = (int) Math.pow(10, 9) + 7;
	
    public int knightDialer(int n) {
       // A 3D array to store the solutions to the subproblems
       long M[][][] = new long[n + 1][4][3];
       long s = 0;
       //do n hops from every i, j index (the very requirement of the problem)
       for(int i = 0; i < 4; i++) {
          for(int j = 0; j < 3; j++) {
             s = (s + paths(M, i, j, n)) % max;
          }
       }
       return (int) s;
    }

    private long paths(long[][][] M, int i, int j, int n) {
       // if the knight hops outside of the matrix or to * return 0 
       //as there are no unique paths from here
       if(i < 0 || j < 0 || i >= 4 || j >= 3 || (i == 3 && j != 1)) return 0;
       if(n == 1) return 1;
       //if the subproblem's solution is already computed, then return it
       if(M[n][i][j] > 0) return M[n][i][j];
       //else compute the subproblem's solution and save it in memory
       M[n][i][j] = paths(M, i - 1, j - 2, n - 1) % max + // jump to a
                    paths(M, i - 2, j - 1, n - 1) % max + // jump to b
                    paths(M, i - 2, j + 1, n - 1) % max + // jump to c
                    paths(M, i - 1, j + 2, n - 1) % max + // jump to d
                    paths(M, i + 1, j + 2, n - 1) % max + // jump to e
                    paths(M, i + 2, j + 1, n - 1) % max + // jump to f
                    paths(M, i + 2, j - 1, n - 1) % max + // jump to g
                    paths(M, i + 1, j - 2, n - 1) % max; // jump to h
       return M[n][i][j];
    }
}

// =========================Similar Approach=========================

class Solution {
    // 1047 ms	75 MB
    public static final int max = 1000000007;
	private static final int[][] dirs = {{-2, 1}, {-1, 2}, {1, 2}, {2, 1}, {2, -1}, {1, -2}, {-1, -2}, {-2, -1}};
        Map<String,Long> pathCount = new HashMap<>();
    
    public int knightDialer(int N) {
        long res = 0;
        for(int i=0;i<4;i++){
            for(int j=0;j<3;j++){
                res = (res+helper(i,j,N))%max;
            }
        }
        return (int) res;
    }
    
    public long helper(int i,int j,int n){
        
        if(i < 0 || i >=4 || j<0 || j >=3 || (i == 3 && j!=1))
            return 0;
        if(n == 1)
            return 1;
        String key = i+"_"+j+"_"+n;
        if(pathCount.containsKey(key))
            return pathCount.get(key);
        long ans = 0;
        for(int k=0;k<dirs.length;k++){
            ans += helper(i+dirs[k][0],j+dirs[k][1],n-1)%max;
        }
        pathCount.put(key,ans);
        return ans;
    }
}
```

[hello](https://leetcode.com/problems/knight-dialer/discuss/190787/How-to-solve-this-problem-explained-for-noobs!!!), [there](https://leetcode.com/problems/knight-dialer/discuss/190787/How-to-solve-this-problem-explained-for-noobs!!!/256076)
<br><br>

***
