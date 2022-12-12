# Others

## 1. Schedule worker's time

```
// If just A & B, then satisfy first 3 days off for A, put into set, then get B's off
```



## 2. Find the most powerful

1. Construct representation of graph using map. At the same time, maintain a Set of roots, remove them if they appear in the "less powerful" position in a given relationship.
2. For each one in the root, do BFS to count number of nodes



## 3. Refer User

1. Loop to build a map: <refer id: List of {{id, price}}>
2. for each refer id, get count



## 4. Mutual Friends, restaurants

用两个set找两个人的friends 然后intersect一下得到mutual friends； 之后loop一遍餐厅找最多人的



## 5. Wedding seats reasonable

把座位排布抽象成一个图，然后每个no‍‍‍‌‍‌‍‍‍‌‌‌‍‌‌‍‌‍‌de有很多family，然后逐个判断node和neighbors是否符合要求

