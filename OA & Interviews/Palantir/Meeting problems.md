# Meeting problems

## 1. Schedule a new meeting

> 输入是一个int[][] meetings, int start, int end,看新的meeting 能不能安排到meetings

- Can I assume there's no need to do sanity check on the input?



```java
public boolean canSchedule(int[][] meetings, int start, int end) {
    // Idea: for each meeting we have, check if it conflicts with the new meeting
    // Corner case: ...
    for (int[] meeting : meetings) {
        // Check for conflict
        if (start >= meeting[0] && start < meeting[1]
                || end > meeting[0] && end <= meeting[1]
                || start < meeting[0] && end > meeting[1]) {
            return false;
        }
    }
    return true;
}
```



## 2. Return spare times

> 类似merge interval，唯一的区别是输出，输出空闲的时间段

- Clarification: how do you define spare time? what about time before first meeting and after last meeting?



**<u>这里code只输出了中间的spare time！！</u>**



```java
public int[][] spareTime(int[][] meetings) {
    // Idea: merge the intervals, then scan the merges intervals to produce result
    // Corner case: ...

    // 1. Merge intervals
    meetings = merge(meetings);

    // 2. Scan to find spare time
    List<int[]> spareTimes = new ArrayList<>();
    for (int i = 1; i < meetings.length; i++) {
        spareTimes.add(new int[]{meetings[i - 1][1], meetings[i][0]});
    }

    return spareTimes.toArray(new int[0][]);
}

// Helper function for merging the intervals
private int[][] merge(int[][] intervals) {
    // Idea: we add an interval to the result and constantly extends its right boundary if
    //       possible, when there's disjoint we move on to the next interval

    // Corner case
    if (intervals.length == 1) {
        return intervals;
    }

    // 1. Sort the intervals based on left boundary
    Arrays.sort(intervals, new Comparator<int[]>() {
        @Override
        public int compare(int[] i1, int[] i2) {
            if (i1[0] == i2[0]) {
                return i1[1] - i2[1];
            } else {
                return i1[0] - i2[0];
            }
        }
    });

    // 2. Add and extends Array
    int[] newInterval = intervals[0];
    List<int[]> result = new ArrayList<>();
    result.add(newInterval);

    for (int[] interval : intervals) {
        if (interval[0] <= newInterval[1]) {
            // Case 1: intervals have intersection, extend the newInterval
            newInterval[1] = Math.max(interval[1], newInterval[1]);
        } else {
            // Case 2: disjointed intervals, consider extending the new interval
            newInterval = interval;
            result.add(interval);
        }
    }

    // 3. Output
    return result.toArray(new int[0][]);
}
```