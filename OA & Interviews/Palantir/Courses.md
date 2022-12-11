# Courses

## 1. Find pair-wise course overlap

> Write a function that takes in a list of (student ID number, course name) pairs and returns, for every pair of students, a list of all courses they share.

- Input & Output clarification

```java
public List<List<List<String>>> courseOverlaps(String[][] studentCoursePairs) {
    // Idea: Use a HashMap to store all the info, then pair-wise bruteforce
    // Corner case:
    if (studentCoursePairs == null || studentCoursePairs.length == 0) {
        return null;
    }

    List<List<List<String>>> result = new ArrayList<>();
    Map<String, List<String>> map = new HashMap<>(); // <studentID, List of courses>

    // Parse all the records
    for (String[] record : studentCoursePairs) {
        String id = record[0];
        String course = record[1];
        if (!map.containsKey(id)) {
            map.put(id, new ArrayList<>());
        }
        map.get(id).add(course);
    }

    // Find all pair-wise common courses
    // (1) get all students ids
    String[] ids = new String[map.keySet().size()];
    int index = 0;
    for (String id : map.keySet()) {
        ids[index++] = id;
    }
    // (2) calculate pairwise common
    for (int i = 0; i < ids.length - 1; i++) {
        for (int j = i + 1; j < ids.length; j++) {
            // construct student pair
            List<String> idPair = Arrays.asList(ids[i], ids[j]);
            // find common course
            List<String> common = new ArrayList<>();
            for (String c1 : map.get(ids[i])) {
                for (String c2 : map.get(ids[j])) {
                    if (c1.equals(c2)) {
                        common.add(c1);
                    }
                }
            }
            // add to result
            List<List<String>> pair = new ArrayList<>();
            pair.add(idPair);
            pair.add(common);
            result.add(pair);
        }
    }
    return result;
}
```



## 2. Output mid class

> Write a function that takes a list of (source, destination) pairs, and returns the name of all of the courses that the students could be taking when they are halfway through their track of courses.

- Idea: First construct the graph using a HashMap, then use DFS to find all path, then find mid of each path