# Shortest Path (Unweighted Graph)
# 最短路径算法

Goal: find the shortest route to go from one node to another in a graph.
目标：找到图表中从一个节点到另一个节点的最短路径。

Suppose we have to following graph:
假设我们必须遵循以下图表：

![Example graph](Images/Graph.png)

We may want to find out what the shortest way is to get from node `A` to node `F`.
我们可能想知道从节点`A`到节点`F`的最短路径是什么。

If the [graph is unweighed](../Graph/), then finding the shortest path is easy: we can use the breadth-first search algorithm. For a weighted graph, we can use Dijkstra's algorithm.
如果[图未加权](../Graph/)，那么找到最短路径很容易：我们可以使用广度优先搜索算法。 对于加权图，我们可以使用Dijkstra算法。

## Unweighted graph: breadth-first search
## 未加权图：广度优先搜索

[Breadth-first search](../Breadth-First%20Search/) is a method for traversing a tree or graph data structure. It starts at a source node and explores the immediate neighbor nodes first, before moving to the next level neighbors. As a convenient side effect, it automatically computes the shortest path between a source node and each of the other nodes in the tree or graph.
[广度优先搜索](../Breadth-First%20Search/)是遍历树或图数据结构的方法。 它从源节点开始，在移动到下一级邻居之前首先探索直接邻居节点。 作为方便的副作用，它会自动计算源节点与树或图中其他每个节点之间的最短路径。

The result of the breadth-first search can be represented with a tree:
广度优先搜索的结果可以用树表示：

![The BFS tree](../Breadth-First%20Search/Images/TraversalTree.png)

The root of the tree is the node you started the breadth-first search from. To find the distance from node `A` to any other node, we simply count the number of edges in the tree. And so we find that the shortest path between `A` and `F` is 2. The tree not only tells you how long that path is, but also how to actually get from `A` to `F` (or any of the other nodes).
树的根是您从广度优先搜索开始的节点。 为了找到从节点`A`到任何其他节点的距离，我们只计算树中边的数量。 所以我们发现`A`和`F`之间的最短路径是2.树不仅告诉你路径有多长，而且还告诉你如何实际从`A`到`F`（或者任何一个） 其他节点）。

Let's put breadth-first search into practice and calculate the shortest path from `A` to all the other nodes. We start with the source node `A` and add it to a queue with a distance of `0`.
让我们将广度优先搜索付诸实践，并计算从`A`到所有其他节点的最短路径。 我们从源节点“A”开始，并将其添加到距离为`0`的队列中。

```swift
queue.enqueue(element: A)
A.distance = 0
```

The queue is now `[ A ]`. We dequeue `A` and enqueue its two immediate neighbor nodes `B` and `C` with a distance of `1`.
队列现在是`[A]`。 我们将`A`出列并将其两个直接邻居节点`B`和`C`排列为距离`1`。

```swift
queue.dequeue()   // A
queue.enqueue(element: B)
B.distance = A.distance + 1   // result: 1
queue.enqueue(element: C)
C.distance = A.distance + 1   // result: 1
```

The queue is now `[ B, C ]`. Dequeue `B` and enqueue `B`'s neighbor nodes `D` and `E` with a distance of `2`.
队列现在是`[B, C]`。 将`B`出列并将`B`的邻居节点`D`和`E`排列为距离`2`。

```swift
queue.dequeue()   // B
queue.enqueue(element: D)
D.distance = B.distance + 1   // result: 2
queue.enqueue(element: E)
E.distance = B.distance + 1   // result: 2
```

The queue is now `[ C, D, E ]`. Dequeue `C` and enqueue `C`'s neighbor nodes `F` and `G`, also with a distance of `2`.
队列现在是`[C, D, E]`。 将`C`出列并将`C`的邻居节点`F`和`G`排队，距离为`2`。

```swift
queue.dequeue()   // C
queue.enqueue(element: F)
F.distance = C.distance + 1   // result: 2
queue.enqueue(element: G)
G.distance = C.distance + 1   // result: 2
```

This continues until the queue is empty and we've visited all the nodes. Each time we discover a new node, it gets the `distance` of its parent plus 1. As you can see, this is exactly what the [breadth-first search](../Breadth-First%20Search/) algorithm does, except that we now also keep track of the distance travelled.
这一直持续到队列为空并且我们访问了所有节点。 每次我们发现一个新节点时，它会获得其父节点的`distance`”加1.正如您所看到的，这正是[广度优先搜索](../Breadth-First%20Search/)算法的作用， 除了我们现在还跟踪行进的距离。

Here's the code:

```swift
func breadthFirstSearchShortestPath(graph: Graph, source: Node) -> Graph {
  let shortestPathGraph = graph.duplicate()

  var queue = Queue<Node>()
  let sourceInShortestPathsGraph = shortestPathGraph.findNodeWithLabel(label: source.label)
  queue.enqueue(element: sourceInShortestPathsGraph)
  sourceInShortestPathsGraph.distance = 0

  while let current = queue.dequeue() {
    for edge in current.neighbors {
      let neighborNode = edge.neighbor
      if !neighborNode.hasDistance {
        queue.enqueue(element: neighborNode)
        neighborNode.distance = current.distance! + 1
      }
    }
  }

  return shortestPathGraph
}
```

Put this code in a playground and test it like so:

```swift
let shortestPathGraph = breadthFirstSearchShortestPath(graph: graph, source: nodeA)
print(shortestPathGraph.nodes)
```

This will output:

	Node(label: a, distance: 0), Node(label: b, distance: 1), Node(label: c, distance: 1),
	Node(label: d, distance: 2), Node(label: e, distance: 2), Node(label: f, distance: 2),
	Node(label: g, distance: 2), Node(label: h, distance: 3)

> **Note:** This version of `breadthFirstSearchShortestPath()` does not actually produce the tree, it only computes the distances. See [minimum spanning tree](../Minimum%20Spanning%20Tree%20(Unweighted)/) on how you can convert the graph into a tree by removing edges.
> **注意：**这个版本的`breadthFirstSearchShortestPath()`实际上并不生成树，它只计算距离。 有关如何通过去除边缘将图形转换为树，请参见[最小生成树](../Minimum%20Spanning%20Tree%20(Unweighted)/)。

*Written by [Chris Pilcher](https://github.com/chris-pilcher) and Matthijs Hollemans*  

*作者：[Chris Pilcher](https://github.com/chris-pilcher)，Matthijs Hollemans*  
*翻译：[Andy Ron](https://github.com/andyRon)*  
