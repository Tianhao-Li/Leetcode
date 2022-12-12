# Domain problems

## 1. Count visits to each domain

> We are given a list cpdomains of count-paired domains. We would like a list of count-paired domains, (in the same format as the input, and in any order), that explicitly counts the number of visits to each subdomain.

- Clarify the input domain format

```java
class Solution {
    public List<String> subdomainVisits(String[] cpdomains) {
        // Idea: split the entry into number & website, then further split web into more
        Map<String, Integer> map = new HashMap<>();

        for (String record : cpdomains) {
            // Split into num and website
            String[] splitRes = record.split(" ");
            int num = Integer.parseInt(splitRes[0]);
            String web = splitRes[1];
            // Split web into domains
            String[] subDomains = web.split("\\.");
            // concat strings from back to forth
            String domain = "";
            for (int i = subDomains.length - 1; i >= 0; i--) {
                if (i == subDomains.length - 1) {
                    domain = subDomains[i] + domain;
                } else {
                    domain = subDomains[i] + "." + domain;
                }
                // Add to map
                if (!map.containsKey(domain)) {
                    map.put(domain, 0);
                }
                int newCount = map.get(domain) + num;
                map.put(domain, newCount);
            }
        }

        // Construct the output
        List<String> result = new ArrayList<>();
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            String combo = entry.getValue() + " " + entry.getKey();
            result.add(combo);
        }

        return result;
    }
}
```

- TC: O(n)
- SC: O(n)



## 2. Longest common subarray

> Output longest common access history subarray

```java
public List<String> longestCommonContinuousHistory(String[] history1, String[] history2) {
    // Idea: DP solution, maintain a 2D memo matrix (optimized to only an array)
    // Corner case:
    if (history1 == null || history2 == null || history1.length == 0 || history2.length == 0) {
        return new ArrayList<>();
    }

    int l1 = history1.length;
    int l2 = history2.length;
    int[] prev = new int[l2 + 1]; // the longest common subarray length at each position
    int maxLength = 0;
    int end = 0; // the ending index of the longest common subarray in history2

    // Fill the memo row
    for (int i = 1; i <= l1; i++) { // the first i history in history1
        int[] cur = new int[l2 + 1];
        for (int j = 1; j <= l2; j++) { // the first j history in history2
            if (history1[i - 1].equals(history2[j - 1])) {
                cur[j] = prev[j - 1] + 1;
                // update globalmax if possible
                if (cur[j] > maxLength) {
                    maxLength = cur[j];
                    end = j - 1; // notice that this is index
                }
            }
        }
        prev = cur;
    }

    // Construct the output base on: history2, maxLength, end
    List<String> result = new ArrayList<>();
    for (int index = end - maxLength + 1; index <= end; index++) {
        result.add(history2[index]);
    }

    System.out.println(maxLength);
    System.out.println(end);

    return result;
}
```

- TC: O(l1 * l2)
- SC: O(l2)



## 3. Ads conversion rate

> Write a function to parse this data, determine how many times each ad was clicked,
> then return the ad text, that ad's number of clicks, and how many of those ad clicks
> were from users who made a purchase.

- Clarify output format

```java
public List<List<String>> adsConversionRate(String[] completedPurchaseUserIds, String[] adClicks, String[] allUserIps) {
    // Idea: put all user IP-ID into map, then store click info text-IP into map, put purchase id into set
    // finally for each text, check if each ID is in the purchase list

    // Corner case: ...
    Map<String, String> users = new HashMap<>(); // <user ip, user id> for all users
    Map<String, List<String>> clicks = new HashMap<>(); // <text, list of clicked ids> for all the ads
    Set<String> purchaseUsers = new HashSet<>(); // set of <user id> for all purchased users

    // Input all users
    for (String record : allUserIps) {
        String[] entry = record.split(",");
        String id = entry[0];
        String ip = entry[1];
        users.put(ip, id);
    }
    // Input all purchased users
    for (String user : completedPurchaseUserIds) {
        purchaseUsers.add(user);
    }
    // Input click info (transform ip into id)
    for (String record : adClicks) {
        // tidy data
        String[] entry = record.split(",");
        String ip = entry[0];
        String text = entry[2];
        String id = users.get(ip);
        // input into map
        if (!clicks.containsKey(text)) {
            clicks.put(text, new ArrayList<>());
        }
        clicks.get(text).add(id);
    }

    // Construct output
    List<List<String>> result = new ArrayList<>();
    for (String text : clicks.keySet()) {
        int clicked = clicks.get(text).size();
        // Get purchase count
        int bought = 0;
        for (String id : clicks.get(text)) {
            if (purchaseUsers.contains(id)) {
                bought++;
            }
        }
        result.add(Arrays.asList(text, String.valueOf(bought), String.valueOf(clicked)));
    }

    return result;
}
```

Testing:

```java
for (List<String> record : result) {
    System.out.println("Text: " + record.get(0) + ", bought " + record.get(1) +
            " of " + record.get(2) + " clicked");
}
```