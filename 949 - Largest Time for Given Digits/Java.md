# Approach 1 - Permutation via Enumeration

> 9 ms	36.8 MB

```JAVA
class Solution {
    private int max_time = -1;

    public String largestTimeFromDigits(int[] A) {
        this.max_time = -1;

        for (int i1 = 0; i1 < A.length; ++i1)
            for (int i2 = 0; i2 < A.length; ++i2)
                for (int i3 = 0; i3 < A.length; ++i3) {
                    // skip duplicate elements
                    if (i1 == i2 || i2 == i3 || i1 == i3)
                        continue;
                    // the total sum of indices is 0 + 1 + 2 + 3 = 6.
                    int i4 = 6 - i1 - i2 - i3;
                    int [] perm = {A[i1], A[i2], A[i3], A[i4]};
                    // check if the permutation can form a time
                    validateTime(perm);
                }
        if (this.max_time == -1)
            return "";
        else
            return String.format("%02d:%02d", max_time / 60, max_time % 60);
    }

    protected void validateTime(int[] perm) {
        int hour = perm[0] * 10 + perm[1];
        int minute = perm[2] * 10 + perm[3];
        if (hour < 24 && minute < 60)
            this.max_time = Math.max(this.max_time, hour*60 + minute);
    }
}
```

[ **See the Solution tab of the Question** ]
<br><br>

# Approach 2 - Permutation via Backtracking

> 9 ms	36.5 MB

```JAVA
class Solution {
    private int max_time = -1;

    public String largestTimeFromDigits(int[] A) {
        this.max_time = -1;
        permutate(A, 0);
        if (this.max_time == -1)
            return "";
        else
            return String.format("%02d:%02d", max_time / 60, max_time % 60);
    }

    protected void permutate(int[] array, int start) {
        if (start == array.length) {
            this.build_time(array);
            return;
        }
        for (int i = start; i < array.length; ++i) {
            this.swap(array, i, start);
            this.permutate(array, start + 1);
            this.swap(array, i, start);
        }
    }

    protected void build_time(int[] perm) {
        int hour = perm[0] * 10 + perm[1];
        int minute = perm[2] * 10 + perm[3];
        if (hour < 24 && minute < 60)
            this.max_time = Math.max(this.max_time, hour * 60 + minute);
    }

    protected void swap(int[] array, int i, int j) {
        if (i != j) {
            int temp = array[i];
            array[i] = array[j];
            array[j] = temp;
        }
    }
}
```

[ **See the Solution tab of the Question** ]
<br><br>

# Approach 3

> 9 ms	37.2 MB
<br><br>
> Leetcode.ca

```JAVA
class Solution {
    public String largestTimeFromDigits(int[] A) {
        int curHour = -1, curMinute = -1;
        for (int i = 0; i < 4; i++) {
            for (int j = 0; j < 4; j++) {
                if (j == i)
                    continue;
                for (int k = 0; k < 4; k++) {
                    if (k == i || k == j)
                        continue;
                    int l = 6 - i - j - k;
                    int hour = A[i] * 10 + A[j], minute = A[k] * 10 + A[l];
                    if (hour <= 23 && minute <= 59) {
                        if (hour > curHour || hour == curHour && minute > curMinute) {
                            curHour = hour;
                            curMinute = minute;
                        }
                    }
                }
            }
        }
        return curHour >= 0 ? String.format("%02d:%02d", curHour, curMinute) : "";
    }
}
```

[hello](https://leetcode.ca/2018-07-06-949-Largest-Time-for-Given-Digits/)
<br><br>

# Approach 4

```JAVA
class Solution {
    // 14 ms	39.3 MB
    public String largestTimeFromDigits(int[] A) {
        String ans = "";
        for (int i = 0; i < 4; ++i) {
            for (int j = 0; j < 4; ++j) {
                for (int k = 0; k < 4; ++k) {
                    if (i == j || i == k || j == k) continue; // avoid duplicate among i, j & k.
                    String h = "" + A[i] + A[j], m = "" + A[k] + A[6 - i - j - k], t = h + ":" + m; // hour, minutes, & time.
                    if (h.compareTo("24") < 0 && m.compareTo("60") < 0 && ans.compareTo(t) < 0) ans = t; // hour < 24; minute < 60; update result.
                }
            }
        }
        return ans;
    }
}

// =========================Similar Approach=========================

class Solution {
    // 16 ms	37.7 MB
    // O(1) time. O(1) space.
    public String largestTimeFromDigits(int[] arr) {
        int[] digits = new int[10];
        for (int digit : arr) digits[digit]++;  // Count the number of occurrences of each digit in arr.

        // Iterate through all possible times, from greatest to lowest.
        for (int h = 23; h >= 0; --h) {
            for (int m = 59; m >= 0; --m) {
               // Pick the current four digits.
                digits[h / 10]--; digits[h % 10]--; digits[m / 10]--; digits[m % 10]--;

                boolean validTime = true;
                for (int i = 0; i < 10 && validTime; ++i) {
                    validTime = digits[i] == 0;     // The current time is valid iff. after picking the current 4 digits all occurrences are 0.
                }

                if (validTime) {
                    return (h < 10 ? "0" : "") + h + ":" + (m < 10 ? "0" : "") + m;  // Add a leading 0 if necessary.
                } else {
                    // Reverse the changes. Change back the values to the original number of occurrences.
                    digits[h / 10]++; digits[h % 10]++; digits[m / 10]++; digits[m % 10]++;
                }
            }
        }
        return "";
    }
}
```

[hello](https://leetcode.com/problems/largest-time-for-given-digits/discuss/200693/JavaPython-3-11-liner-O(64)-w-comment-6-ms.), [there](https://leetcode.com/problems/largest-time-for-given-digits/discuss/200693/JavaPython-3-11-liner-O(64)-w-comment-6-ms./785524)
<br><br>

***
