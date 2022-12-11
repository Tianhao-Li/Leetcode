# Gate Access

## 1. Find people with invalid badge record

> Given a list of people who enter and exit, find the people who entered without
>  their badge and who exited without their badge.

- Clarify: how do you define an invalid enter or exit?
- input and output format clarification

```java
public List<List<String>> invalidBadgeRecords(String[][] records) {
    // Idea: Use a map to keep track of each person's entry&exit
    // Corner case:
    if (records == null || records.length == 0 || records[0].length == 0) {
        return new ArrayList<>();
    }

    Map<String, String> map = new HashMap<>();
    Set<String> invalidEnter = new HashSet<>();
    Set<String> invalidExit = new HashSet<>();

    // Iterate over each record
    for (String[] record : records) {
        String name = record[0];
        String action = record[1];
        if (!map.containsKey(name)) {
            map.put(name, "exited");
        }
        // Check action
        // 1. enter action
        if (action.equals("enter")) {
            if (map.get(name).equals("entered")) {
                // wrong status, invalid exit
                invalidExit.add(name);
            }
            map.put(name, "entered");
        } else {
            // 2. exit action
            if (map.get(name).equals("exited")) {
                // wrong status, invalid enter
                invalidEnter.add(name);
            }
            map.put(name, "exited");
        }
    }

    // Post-processing, check people who don't left
    for (String person : map.keySet()) {
        if (map.get(person).equals("entered")) {
            invalidExit.add(person);
        }
    }

    // Output result
    List<String> wrongEnter = new ArrayList<>(invalidEnter);
    List<String> wrongExit = new ArrayList<>(invalidExit);
    List<List<String>> result = new ArrayList<>();
    result.add(wrongEnter);
    result.add(wrongExit);
    return result;
}
```

- TC: O(n)
- SC: O(num of people)



## 2. Multiple access within 1 hour

> ```
> return: list of names and the times where their swipe badges within one hour.
> ```

```java
public List<List<List<String>>> frequentAccess(String[][] records) {
    // Idea: Use map to store each user's information, iterate over each person to find result
    // Corner case
    if (records == null || records.length == 0 || records[0].length == 0) {
        return new ArrayList<>();
    }

    List<List<List<String>>> result = new ArrayList<>();
    Map<String, List<String>> map = new HashMap<>();

    // Gather each person's info
    for (String[] record : records) {
        String name = record[0];
        String time = record[1];
        if (!map.containsKey(name)) {
            map.put(name, new ArrayList<>());
        }
        map.get(name).add(time);
    }

    // For each person, investigate
    for (String person : map.keySet()) {
        List<String> times = map.get(person);
        Collections.sort(times);
        for (int start = 0; start < times.size() - 1; start++) {
            int end = start + 1;
            // find 1 hour interval starting from this start time
            while (end < times.size()) {
                int diff = timeDiff(times.get(start), times.get(end));
                if (diff >= 60) {
                    break;
                }
                end++;
            }
            List<String> timestamps = new ArrayList<>();
            // check access times
            if (end > start + 1) {
                // find multiple access within 1 hour
                end = end == times.size() ? times.size() - 1 : end;
                for (int i = start; i <= end; i++) {
                    timestamps.add(times.get(i));
                }
                List<List<String>> record = new ArrayList<>();
                // construct name part
                List<String> nameList = new ArrayList<>();
                nameList.add(person);
                record.add(nameList);
                // construct timestamps part
                record.add(timestamps);
                // add to result
                result.add(record);
                break;
            }
        }
    }

    return result;
}

// Helper function for calculating time diff
private int timeDiff(String small, String big) {
    int t1 = Integer.parseInt(small);
    int t2 = Integer.parseInt(big);
    // hour info
    int h1 = t1 / 100;
    int h2 = t2 / 100;
    // minute info
    int m1 = t1 % 100;
    int m2 = t2 % 100;
    // calculate diff
    return h2 * 60 + m2 - h1 * 60 - m1;
}
```

- TC: O(n log n) -- dominated by sorting
- SC: O(n)