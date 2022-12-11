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



## 2. Does two nodes have common ancestor

- Clarify whether the two given nodes(x & y for example) are guaranteed to be in the graph

```java
public boolean hasCommonAncestor(int[][] edges, int x, int y) {
    // Idea: build <child, parent> map, find all ancestors of x & y, then compare
    // Corner case:
    if (edges == null || edges.length == 0) {
        return false;
    }

    Map<Integer, Set<Integer>> map = new HashMap<>(); // <node, parents>

    // 1. Construct map
    // Get graph info
    for (int i = 0; i < edges.length; i++) {
        int[] edge = edges[i];
        int child = edge[1];
        int parent = edge[0];
        // Process child
        // Make sure the child exist in map
        if (!map.containsKey(child)) {
            map.put(child, new HashSet<Integer>());
        }
        // Add current edge to map
        map.get(child).add(parent);
    }

    // 2. Find all ancestors of both nodes
    Set<Integer> ancestorX = getAllAncestors(map, x);
    Set<Integer> ancestorY = getAllAncestors(map, y);

    // 3. Check if common ancestor exists
    for (int candidate : ancestorX) {
        if (ancestorY.contains(candidate)) {
            return true;
        }
    }

    return false; // when fail to return true
}

// Helper function for finding all ancestors
private Set<Integer> getAllAncestors(Map<Integer, Set<Integer>> map, int x) {
    // Use a stack to gradually find all ancestors
    Set<Integer> ancestors = new HashSet<>();
    ancestors.add(x); // If x or y is a root, we need to handle this
    Queue<Integer> queue = new ArrayDeque<>();
    queue.offer(x);

    while (!queue.isEmpty()) {
        // find all parents of cur node
        int cur = queue.poll();
        Set<Integer> parents = map.get(cur);
        // add each parent to the stack
        if (parents != null) {
            for (int parent : parents) {
                ancestors.add(parent);
                queue.offer(parent);
            }
        }
    }

    return ancestors;
}
```

- TC: O(n)
- SC: O(n)



## 3. Farthest ancestor

- Clarify: can I assume that if there are multiple ancestors that have same distances to the given node, i just return any of them

```java
public int earliestAncestor(int[][] edges, int x) {
    // Idea: level-order traversal of the graph in down-top way, use Queue
    // Corner case:
    if (edges == null || edges.length == 0) {
        return -1;
    }

    List<Integer> ancestors = new ArrayList<>();
    Map<Integer, Set<Integer>> map = new HashMap<>(); // <node, parents>
    Queue<Integer> queue = new ArrayDeque<>();
    queue.offer(x);

    // 1. Construct map
    for (int i = 0; i < edges.length; i++) {
        int[] edge = edges[i];
        int child = edge[1];
        int parent = edge[0];
        // Process child
        // Make sure the child exist in map
        if (!map.containsKey(child)) {
            map.put(child, new HashSet<Integer>());
        }
        // Add current edge to map
        map.get(child).add(parent);
    }

    // 2. Level-order traversal
    while (!queue.isEmpty()) {
        // get cur element
        int cur = queue.poll();
        // append all parents to the queue
        Set<Integer> parents = map.get(cur);
        if (parents != null) {
            // cur node still have ancestor, no need to store this into ancestors
            for (int parent : parents) {
                queue.offer(parent);
            }
        } else {
            ancestors.add(cur);
        }
    }

    return ancestors.get(ancestors.size() - 1);
}
```

- TC: O(n)
- SC: O(n)