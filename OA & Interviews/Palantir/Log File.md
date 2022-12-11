# Log File

## 1. Each user's start time & end time

> Return each user's starting time and ending time

- Input and output clarification

```java
public void startEndTime(String[][] log) {
    // Idea: Use HashMap to store each user's access, then find out the time
    // Corner case: ...
    Map<String, Integer[]> map = new HashMap<>(); // <userID, starting time & ending time>

    for (String[] record : log) {
        // Get info
        String user = record[1];
        Integer time = Integer.valueOf(record[0]);
        if (!map.containsKey(user)) {
            map.put(user, new Integer[] {Integer.MAX_VALUE, Integer.MIN_VALUE});
        }
        // Add to map
        if (time < map.get(user)[0]) {
            map.get(user)[0] = Integer.valueOf(time);
        }
        if (time > map.get(user)[1]) {
            map.get(user)[1] = Integer.valueOf(time);
        }
    }

    // Output result
    for (String user : map.keySet()) {
        System.out.println("The times for " + user + "is: ");
        System.out.print(map.get(user)[0]);
        System.out.print(",");
        System.out.println(map.get(user)[1]);
    }
}
```

- TC: O(n)
- SC: O(n) -- size of map, considering num of entries and size of each entry



## 2. Most frequently accessed file within 5 min

> Write a function that takes the logs and returns the resource with the highest number of accesses in any 5 minute window, together with how many accesses it saw.

```java
public void mostFrequentFile(String[][] log) {
    // Idea: Use HashMap to store access to each file, then find out the time
    // Corner case:...

    Map<String, List<Integer>> map = new HashMap<>(); // <resourceID, List of access time>

    // Construct map
    for (String[] record : log) {
        // Get info
        String resource = record[2];
        Integer time = Integer.valueOf(record[0]);
        // Add to map
        if (!map.containsKey(resource)) {
            map.put(resource, new ArrayList<>());
        }
        map.get(resource).add(time);
    }

    // Sliding window to find out result
    int globalMax = 1; // global max of number of accesses within 5min(300s)
    String maxId = log[0][2];
    int maxStartIdx = 0; // the index of starting value of highest num of accesses records

    for (String resource : map.keySet()) {
        List<Integer> access = map.get(resource);
        Collections.sort(access);
        map.put(resource, access); // add sorted logs back for result construction
        // Use Sliding Window to find out largest 5 minutes window
        int left = 0;
        int right = 0;
        while (right < access.size()) {
            while (access.get(right) - access.get(left) > 300) {
                left++;
            }
            // Update global max if possible
            if (right - left + 1 > globalMax) {
                globalMax = right - left + 1;
                maxId = resource;
                maxStartIdx = left;
            }
            // Update pointer
            right++;
        }
    }

    System.out.println("Most frequent visited resource is " + maxId);
    System.out.println(globalMax);
    List<Integer> access = map.get(maxId);
    for (int i = maxStartIdx; i < maxStartIdx + globalMax; i++) {
        System.out.println(access.get(i));
    }
}
```

- TC: O(n)
- SC: O(n)



## 3. Build transition graph

> Write a function that takes the logs as input, builds the transition graph and returns it as an adjacency list with probabilities. Add __START__ and __END__ states.

- Clarify input, clarify output format

```java
public void transitionGraph(String[][] log) {
    // Idea: First build each user's resource path, then count the next visit after each state
    // Corner case: ...

    // 1. Build users' resource paths
    Map<String, List<String[]>> map = new HashMap<>(); // <userID, List of [time, resource]>
    // (1) add entries
    for (String[] record : log) {
        String user = record[1];
        String time = record[0];
        String resource = record[2];
        // create entry if userID not exist
        if (!map.containsKey(user)) {
            map.put(user, new ArrayList<>());
        }
        map.get(user).add(new String[]{time,resource});
    }
    // (2) Sort each user's entry
    for (String user : map.keySet()) {
        Collections.sort(map.get(user), new Comparator<String[]>() {
            @Override
            public int compare(String[] a1, String[] a2) {
                if (a1[0].equals(a2[0])) {
                    return 0;
                }
                return Integer.valueOf(a1[0]) < Integer.valueOf(a2[0]) ? -1 : 1;
            }
        });
    }

    // Test
//        for (String user : map.keySet()) {
//            List<String[]> result = map.get(user);
//            System.out.println("The ordered records for " + user + " is:");
//            for (String[] entry : result) {
//                System.out.println(Arrays.toString(entry));
//            }
//        }

    // 2. Summarize the states info
    Map<String, Map<String, Double>> count = new HashMap<>(); // <state, Map of <next state, count>>
    count.put("_START_", new HashMap<>()); // add "start" state
    // Iterate over each user to count
    for (String user : map.keySet()) {
        List<String[]> records = map.get(user); // List of [time, state]
        for (int i = 0; i <= records.size(); i++) { // i : the current next state
            // case 1: after start
            if (i == 0) {
                Map<String, Double> nextStates = count.get("_START_"); // <next state, count>
                String nextState = records.get(i)[1];
                if (!nextStates.containsKey(nextState)) {
                    nextStates.put(nextState, (double) 1);
                } else {
                    nextStates.put(nextState, nextStates.get(nextState) + 1);
                }
            } else {
                String prevState = records.get(i - 1)[1];
                if (!count.containsKey(prevState)) {
                    count.put(prevState, new HashMap<>());
                }
                // get the map entry of counts to add to
                Map<String, Double> nextStates = count.get(prevState);
                String nextState = "";
                // case 2: the last record of a user
                if (i == records.size()) {
                    nextState = "_END_";
                } else {
                    // case 3: record in the middle
                    nextState = records.get(i)[1];
                }
                if (!nextStates.containsKey(nextState)) {
                    nextStates.put(nextState, (double) 1);
                } else {
                    nextStates.put(nextState, nextStates.get(nextState) + 1);
                }
            }
        }
    }

    // 3. Post-process the "count" map
    for (String resource : count.keySet()) {
        int total = 0;
        Map<String, Double> nextStates = count.get(resource); // <nextstate, count>
        for (Map.Entry<String, Double> state : nextStates.entrySet()) {
            total += state.getValue();
        }
        for (String state : nextStates.keySet()) {
            nextStates.put(state, nextStates.get(state) / total);
        }
    }

    // 4. Output
    for (String resource : count.keySet()) {
        System.out.println(resource + ": ");
        for (Map.Entry<String, Double> state : count.get(resource).entrySet()) {
            System.out.println(state.getKey() + ": " +  String.format("%.2f", state.getValue())+ ",");
        }
    }
}
```

- TC: O(n log n) -- add entry O(n), sorting worst case O(n log n), count info O(n)

- SC: O(n)