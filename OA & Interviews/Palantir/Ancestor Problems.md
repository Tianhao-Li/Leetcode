# Ancestor Problems

## 1. Nodes with 0 or 1 parent

> Given a 2d int array like {{1,4}, {1,5}, {2,5}, {3,6}, {6,7}}, edge[0] is edge[1]'s parent. Out put nodes with zero and/or one parent.

- Check for input assumptions, check for input value uniqueness 
- Check for output order

```java
public List<Integer> zeroOrOneParent(int[][] edges) {
        // Idea: Use Map to store each node's parents count
        // Corner case:
        if (edges == null || edges.length == 0) {
            return new ArrayList<>();
        }

        Map<Integer, Integer> count = new HashMap<>();

        // Construct simple version of the graph
        for (int[] edge : edges) {
            int child = edge[1];
            int parent = edge[0];
            // Add to map
            count.put(parent, count.getOrDefault(parent, 0));
            count.put(child, count.getOrDefault(child, 0) + 1);
        }

        List<Integer> result = new ArrayList<>();
        for (Integer node : count.keySet()) {
            int num = count.get(node);
            if (num == 0 || num == 1) {
                result.add(node);
            }
        }

        return result; // order?
    }
```

- TC: O(n) -- if output sorted then O(n log n)
- SC: O(n)