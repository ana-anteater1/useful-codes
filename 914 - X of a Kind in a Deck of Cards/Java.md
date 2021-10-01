# Approach 1 - Brute Force

> 5 ms	51.7 MB

```JAVA
class Solution {
    public boolean hasGroupsSizeX(int[] deck) {
        int N = deck.length;
        int[] count = new int[10000];
        for (int c: deck)
            count[c]++;

        List<Integer> values = new ArrayList();
        for (int i = 0; i < 10000; ++i)
            if (count[i] > 0)
                values.add(count[i]);

        search: for (int X = 2; X <= N; ++X)
            if (N % X == 0) {
                for (int v: values)
                    if (v % X != 0)
                        continue search;
                return true;
            }

        return false;
    }
}
```

[ **See the Solution tab of the Question** ]
<br><br>

# Approach 2 - Greatest Common Divisor

```JAVA
class Solution {
    // 5 ms	51.5 MB
    public boolean hasGroupsSizeX(int[] deck) {
        int[] count = new int[10000];
        for (int c: deck)
            count[c]++;

        int g = -1;
        for (int i = 0; i < 10000; ++i)
            if (count[i] > 0) {
                if (g == -1)
                    g = count[i];
                else
                    g = gcd(g, count[i]);
            }

        return g >= 2;
    }

    public int gcd(int x, int y) {
        return x == 0 ? y : gcd(y%x, x);
    }
}

// =========================Similar Approach=========================

public class Solution {
    // 14 ms	49 MB
    // Lee's solution
    public boolean hasGroupsSizeX(int[] deck) {
        Map<Integer, Integer> count = new HashMap<>();
        int res = 0;
        for (int i : deck) count.put(i, count.getOrDefault(i, 0) + 1);
        for (int i : count.values()) res = gcd(i, res);
        return res > 1;
    }

    public int gcd(int a, int b) {
        return b > 0 ? gcd(b, a % b) : a;
    }
}
```

[ **See the Solution tab of the Question** ], [hello](https://leetcode.com/problems/x-of-a-kind-in-a-deck-of-cards/discuss/175845/C++JavaPython-Greatest-Common-Divisor)
<br><br>

# Approach 3

> 14 ms	49.4 MB
<br><br>
> Leetcode.ca

```JAVA
class Solution {
    public boolean hasGroupsSizeX(int[] deck) {
        Map<Integer, Integer> countMap = new HashMap<Integer, Integer>();
        for (int num : deck) {
            int count = countMap.getOrDefault(num, 0);
            count++;
            countMap.put(num, count);
        }
        int gcd = 0;
        Set<Integer> keySet = countMap.keySet();
        for (int num : keySet) {
            int count = countMap.get(num);
            gcd = gcd(gcd, count);
        }
        return gcd >= 2;
    }

    public int gcd(int num1, int num2) {
        if (num1 == 0 && num2 == 0)
            return 1;
        while (num1 != 0 && num2 != 0) {
            if (num1 > num2) {
                int temp = num1;
                num1 = num2;
                num2 = temp;
            }
            num2 %= num1;
        }
        return num1 == 0 ? num2 : num1;
    }
}
```

[hello](https://leetcode.ca/2018-06-01-914-X-of-a-Kind-in-a-Deck-of-Cards/)
<br><br>

# Approach 4

> 13 ms	49.4 MB
<br><br>
> From Fishercoder1534

```JAVA
public class Solution {
    public boolean hasGroupsSizeX(int[] deck) {
        //Size too small for partitions
        if (deck.length < 2) {
            return false;
        }

        //Track repetitions of values in deck array
        Map<Integer, Integer> mapReps = new HashMap<>();
        for (int card : deck) {
            if (!mapReps.containsKey(card)) {
                mapReps.put(card, 1);
            } else {
                mapReps.put(card, mapReps.get(card) + 1);
            }
        }

        //Create array of map values
        int num = 0;
        int[] arrReps = new int[mapReps.size()];
        for (Map.Entry<Integer, Integer> e : mapReps.entrySet()) {
            arrReps[num++] = e.getValue();
        }

        //Find greatest common denominator
        num = arrGCD(arrReps, arrReps.length);

        //If gcd of all repetitions is greater than 1, it's partitionable.
        return num > 1;
    }

    private int gcd(int a, int b) {
        return b == 0 ? a : gcd(b, a % b);
    }

    private int arrGCD(int[] arr, int n) {
        int result = arr[0];
        for (int i = 1; i < n; i++) {
            result = gcd(arr[i], result);
        }

        return result;
    }
}
```

[hello](https://github.com/fishercoder1534/Leetcode/blob/master/src/main/java/com/fishercoder/solutions/_914.java)
<br><br>

***