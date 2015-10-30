# Clone Graph

# Clone Graph

Clone an undirected graph. Each node in the graph contains a label and a list of its neighbors.


OJ's undirected graph serialization:

Nodes are labeled uniquely.

We use # as a separator for each node, and , as a separator for node label and each neighbor of the node.

As an example, consider the serialized graph {0,1,2#1,2#2,2}.

The graph has a total of three nodes, and therefore contains three parts as separated by #.

1. First node is labeled as 0. Connect node 0 to both nodes 1 and 2.
2. Second node is labeled as 1. Connect node 1 to node 2.
3. Third node is labeled as 2. Connect node 2 to node 2 (itself), thus forming a self-cycle.

Visually, the graph looks like the following:

        1
       / \
      /   \
     0 --- 2
          / \
          \_/

## Solution

1. DFS. 
2. BFS.

## Complexity

时间复杂度 O(n)，空间复杂度 O(n)

## Code

```java
public UndirectedGraphNode cloneGraph_1(UndirectedGraphNode node) {
        HashMap<UndirectedGraphNode, UndirectedGraphNode> map = new HashMap<UndirectedGraphNode, UndirectedGraphNode>();
        return cloneGraphRe(node, map);
    }
    
    public UndirectedGraphNode cloneGraphRe(UndirectedGraphNode node, HashMap<UndirectedGraphNode, UndirectedGraphNode> map) {
        if (node == null) return null;
        if (map.containsKey(node) == true) {
            return map.get(node);
        }
        UndirectedGraphNode newnode = new UndirectedGraphNode(node.label);
        map.put(node, newnode);
        for (UndirectedGraphNode cur : node.neighbors) {
            newnode.neighbors.add(cloneGraphRe(cur, map));
        }
        return newnode;
    }
    
    public UndirectedGraphNode cloneGraph(UndirectedGraphNode node) {
        HashMap<UndirectedGraphNode, UndirectedGraphNode> map = new HashMap<UndirectedGraphNode, UndirectedGraphNode>();
        Queue<UndirectedGraphNode> queue = new LinkedList<UndirectedGraphNode>();
        if (node == null) return null;
        queue.offer(node);
        map.put(node, new UndirectedGraphNode(node.label));
        while (queue.isEmpty() == false) {
            UndirectedGraphNode cur = queue.poll();
            for (UndirectedGraphNode neighbor : cur.neighbors) {
                if (map.containsKey(neighbor) == false) {
                    UndirectedGraphNode newnode = new UndirectedGraphNode(neighbor.label);
                    map.put(neighbor, newnode);
                    queue.offer(neighbor);
                }
                map.get(cur).neighbors.add(map.get(neighbor));
            }
        }
        return map.get(node);
    }
```

