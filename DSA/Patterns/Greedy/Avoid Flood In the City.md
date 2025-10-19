# Problem: Avoid Flood in the City

[Problem](https://leetcode.com/problems/avoid-flood-in-the-city/description/?envType=company&envId=google&favoriteSlug=google-thirty-days)

Your country has an infinite number of lakes. Initially, all the lakes are empty, but when it rains over the `n<sup>th</sup>` lake, the `n<sup>th</sup>` lake becomes full of water. If it rains over a lake that is **full of water** , there will be a **flood** . Your goal is to avoid floods in any lake.

Given an integer array `rains` where:

- `rains[i] > 0` means there will be rains over the `rains[i]` lake.
- `rains[i] == 0` means there are no rains this day and you **must** choose **one lake** this day and **dry it** .

Return _an array `ans`_ where:

- `ans.length == rains.length`
- `ans[i] == -1` if `rains[i] > 0`.
- `ans[i]` is the lake you choose to dry in the `ith` day if `rains[i] == 0`.

If there are multiple valid answers return **any** of them. If it is impossible to avoid flood return **an empty array** .

Notice that if you chose to dry a full lake, it becomes empty, but if you chose to dry an empty lake, nothing changes.

### Intuition

When approaching this problem, i knew to think of Greedy / Intervals. You can see that you need to identify which are overlapping, and then to next think about what one to pick. What i stuggled to grasp the need to continue checking.

So we can use a HashMap to track the days for which the lake would dry up. We can use a Sorted List with Binary Search.

#### What does the Code Look Like ?

```java
import java.util.HashMap;
import java.util.TreeSet;

class Solution {
    public int[] avoidFlood(int[] rains) {
        int n = rains.length;
        int[] ans = new int[n];
        // Maps a full lake (lake ID) to the day it became full (index i)
        HashMap<Integer, Integer> fullLakes = new HashMap<>();
        // Stores the indices of sunny days (days with rains[i] == 0)
        TreeSet<Integer> sunnyDays = new TreeSet<>();

        for (int i = 0; i < n; i++) {
            int lake = rains[i];

            if (lake == 0) {
                // It's a sunny day, add its index to the set of available dry days
                sunnyDays.add(i);
                ans[i] = 1; // Set a default value (e.g., dry lake 1)
            } else {
                // It's a rainy day for 'lake'
                if (fullLakes.containsKey(lake)) {
                    // This lake is already full. We need to find a sunny day
                    // *after* the day it last rained on this lake.

                    // Find the earliest sunny day that comes *after* the day the lake was last filled
                    // fullLakes.get(lake) gets the index (day) when 'lake' was last filled
                    Integer dryDay = sunnyDays.ceiling(fullLakes.get(lake));

                    if (dryDay == null) {
                        // No available sunny day found after the lake was last filled
                        // This means a flood is inevitable.
                        return new int[0]; // Return an empty array indicating failure
                    }

                    // We found a sunny day 'dryDay' to dry the 'lake'
                    ans[dryDay] = lake; // Assign the drying task to that sunny day
                    sunnyDays.remove(dryDay); // This sunny day is now used
                }

                // Mark this day as a rainy day (no drying action)
                ans[i] = -1;
                // Update the map: 'lake' is now full as of day 'i'
                fullLakes.put(lake, i);
            }
        }
        return ans;
    }
}
```

1. Iterate through the rains array day by day.
2. If its a sunny day (0), add its index to our sunny days sorted list.
3. If it rains on a lake:
   1. Check our HashMap, is this lake already full?
      1. Binary Search sunny_days for the earliest day AFTER the lake last filled.
      2. If none exists, its impossible so return []
   2. If Yes,
      1. If one exists, use it! Update the answer and remove it from sunny_days
   3. Update the hashmap with the current day for this lake.

### Whats the Trick ?

The trick is leverage the treeSet for binary search instead of actually performing it, and knowing that when you need a value you can do .ceiling to find the smallest value greater or equal to the current element.
