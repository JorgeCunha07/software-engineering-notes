# Java Data Structures

## Basic Data Structures

### Arrays

Arrays are contiguous blocks of memory that store elements of the same data type. They offer constant-time access to elements by index (O(1)). In Java, arrays are fixed in size once created.

```java
int numbers = new int[1];
numbers[0] = 10;

int firstNumber = numbers[0]; // O(1)
```

### ArrayList

Is adynamic array implementation in Java that belongs to the Collections Framework. It can grow or shrink in size as needed, providing flexibility. Accessing elements by index is still O(1) on average.

```java
import java.util.ArrayList;

List<String> names = new ArrayList<>();
names.add("Alice"); // O(1) inserting at the end
names.add(0, "Bob"); // O(n) inserting at front needs to shift elements

String firstPerson = names.get(0); // O(1)
```

### LinkedList

Is another implementation of the `List` interface in Java, using a doubly-linked list structure. It excels at insertions and deletions, especially at the beginning or end of the list (O(1)), but accessing elements by index takes linear time (O(n));

```java
import java.util.LinkedList;

List<Integer> numbersList = new LinkedList<>();
numbersList.addFirst(1); // O(1)
numbersList.addLast(2); // O(1)

int firstElement = numbersList.getFirst(); // O(n)
```

### Stack

Stacks follow the Last-In-First-Out (LIFO) principle. Common operations include `push` (add to the top) and `pop` (remove from the top), both typically taking O(1) time. The `Stack` class is a legacy subclass of `Vector`. Prefer using `Deque` (e.g. `ArrayDeque`) for stack behavior.

```java
import java.util.ArrayDeque;

Deque<String> stack = new ArrayDeque<>();
stack.push("First"); // O(1)
stack.push("Second"); // O(1)

String topElement = stack.pop(); // O(1) Returns "Second"
```

`ArrayDeque` is often faster and more memory-efficient than `LinkedList` for queues or stacks (it uses a circular array).

### Queue

Queues follow the First-In-First-Out (FIFO) principle. Common operations include `enqueue` (add to the rear) and `dequeue` (remove from the front), usually with O(1) time complexity. Java provides the `Queue` interface, with implementations like `LinkedList` and `PriorityQueue`.

```java
import java.util.LinkedList;
import java.util.Queue;

Queue<String> queue = new LinkedList<>();
queue.offer("First"); // O(1)
queue.offer("Second"); // O(1)

String frontElement = queue.poll(); // O(1) Returns "First"
```

A `PriorityQueue` is a data structure that behaves like a queue but orders its elements based on priority, not insertion order.
By default, it's a min-heap, meaning:

- The smallest element (based on natural order or custom comparator) is always at the head.
- Operations like `add`, `poll` and `remove` are all O(log n).
- `peek()` returns the smallest element in O(1) time.

Operations `add/offer` and `poll` take O(log n), while `peek` is O(1).
Use `PriorityQueue` when you need the smalles (or largest, with a reversed comparator) element quickly (e.g., Djikstra's algorithm).

#### There are two ways to define priority

##### 1. Using `Comparable` (natural order)

Your class must implement `Comparable<T>` and override the `compareTo` method.

Ordering integers naturally (smallest first is default):

```java
import java.util.PriorityQueue;

PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.add(5); // O(log n)
pq.add(2);

int min = pq.poll(); // O(log n) returns 2
```

For a custom class:

```java
class Task implements Comparable<Task> {
  int priority;

  public Task(int priority) {
    this.priority = priority;
  }

  @Override
  public int compareTo(Task other) {
    return Integer.compare(this.priority, other.priority);
  }
}

PriorityQueue<Task> pq = new PriorityQueue<>();

pq.add(new Task(10));
pq.add(new Task(5));

System.out.println(pq.poll().priority); // 5 - lowest priority first
```

##### 2. Using `Comparator` (custom order without modifying the class)

You can pass a custom `Comparator` to the constructor if:

- The class does not implement `Comparable`.
- You want a different priority rule.

Highest number first (max-heap behavior):

```java
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Comparator.reverseOrder());

maxheap.add(5);
maxHeap.add(1);
maxHeap.add(3);

System.out.println(maxHeap.poll()); // 5 (largest first)
```

Custom class with `Comparator`:

```java
class Task {
  int priority;
  String name;

  public Task(int priority, String name) {
    this.priority = priority;
    this.name = name;
  }
}

PriorityQueue<Task> pq = new PriorityQueue<>(Comparator.comparingInt(t -> t.priority));

pq.add(new Task(10, "Low"));
pq.add(new Task(1, "High"));

System.out.println(pq.poll().name); // "High"
```

You can also sort descending like this:

```java
PriorityQueue<Task> pq = new PriorityQueue<>(
  Comparator.comparingInt((Task t) -> t.priority).reversed()
);
```

### HashMap

Is a hash table implementation in Java that stores key-value pairs. It provides average O(1) time complexity for basic operations like `put` (insert), `get` (retrieve), and `remove` (delete), assuming a good hash function.

```java
import java.util.HashMap;

Map<String, Integer> ages = new HashMap<>();

ages.put("Alice", 30); // O(1) average

int aliceAge = ages.get("Alice"); // O(1) average, returns 30
```

### HashSet

Is backed by a `HashMap`. Operations (`add`, `contains`, `remove`) are O(1) average (same caveats as `HashMap`). It forbids duplicate entries.

```java
Set<String> hashSet = new HashSet<>();

hashSet.add("hello"); // O(1) average

boolean exists = hashSet.contains("hello"); // O(1) average
```

## Trees

### Binary Tree

A binary tree is a specific type of tree data structure where each node is constrained to have at most two children, which are typically designated as the left child and right child. This restriction to a maximum of two children per node is the defining feature of binary trees, setting them apart from other tree structures that can allow a greater number of children. This fundamental constraint simplifies both the implementation and analysis of operations performed on binary trees, making them a widely used foundation for more complex tree-based structures.

Binary trees possess several fundamental properties. They exhibit a hierarchical structure, originating from a single root node. Nodes that do not have any children are termed **leaf nodes**. The tree is organized into levels, with the root at level 0, its children at level 1, and so forth, based on their distance from the root. Each node in a binary tree can also be considered as the root of its own **subtree**. There are different categories of binary trees based on their structural characteristics, including **full binary trees** (where every node other than the leaves has two children), **complete binary trees** (where all levels are completely filled except possibly the last level, which is filled from left to right), and **perfect binary trees** (where all internal nodes have exactly two children and all leaf ndoes are at the same level). Understanding these properties and types is essential as they dictate the suitability of binary trees for various applications and algorithms. For instance, complete binary trees are the foundational tree structure for heaps, a type of priority queue.

In Java, a binary tree is typically implemented using a class to represent a node. A basic **node structure**, often named `TreeNode`, will contain an attribute to store the node's data, commonly referred to as `value` or `data`, and two references to other `TreeNode` objects: `left` for the left child and `right` for the right child. These `left` and `right` references can either point to another `TreeNode` object, representing a child node, or be `null`, indicating the absence of a child in that position. This node structure serves as the fundamental building block for constructing and manipulating binary trees in Java.

The **insertion operation** in a general binary tree involves adding a new node at a specific location, often as a left or right child of an existing node. The precise process depends on the specific requirements or the type of binary tree being implemented. For example, in a Binary Search Tree (BST), the insertion follows a specific ordering rule based on the value of the new node compared to the existing nodes in the tree. The following Java code snippet illustrates a basic insertion into a binary tree, assuming a specific parent node and child position are known:

#### Create/Declare a Node of a Binary Tree

```java
class Node {
  int data;
  Node left, right;

  Node(int d) {
    this.data = d;
    this.left = null;
    this.right = null;
  }
}

class Main {
  public static void main(String[] args) {
    // Initialize and allocate memory for tree nodes
    Node firstNode = new Node(2);
    Node sedondNode = new Node(3);
    Node thirdNode = new Node(4);
    Node fourthNode = new Node(5);

    // Connect binary tree nodes
    firstNode.left = secondNode;
    firstNode.right = thirdNode;
    secondNode.left = fourthNode;
  }
}
```

#### Terminologies in Binary Tree

- Nodes: The fundamental part of a binary tree, where each node contains data and link to two childs nodes.
- Root: The topmost node in a tree is known as the root node. It has no parent and serves as the starting point for all nodes in the tree.
- Parent Node: A node that has one or more child nodes. In a binary tree, each node can have at most two children.
- Child Node: A node that is descendant of another node (its parent).
- Leaf Node: A node that does not have any children or both children are null.
- Internal Node: A node that has at least one child. This includes all nodes except the leaf nodes.
- Depth of a Node: The number of edges from a specific node to the root node. The depth of the root node is zero.
- Height of a Binary Tree: The number of nodes from the deepest leaf node to the root node.

#### Properties of Binary Tree

- The maximum number of nodes at level L of a binary tree is 2^L.
- The maximum number of nodes in a binary tree of height H is 2^H - 1.
- Total number of leaf nodes in a binary tree = total number of nodes with 2 children + 1.
- In a Binary Tree with N nodes, the minimum possible height or the minimum number of levels is Log2(N+1).
- A Binary Tree with L levels has at least |Log2L| + 1 levels.

#### Operations in Binary Tree

##### 1. Traversal in Binary Tree

Traversal in Binary Tree involves visiting all nodes of the binary tree. Tree Traversal algorithms can be classified broadly into two categories, **DFS** and **BFS**.

**Depth-First Search (DFS) algorithms:** explores as far down a branch as possible before backtracking. It is implemented using recursion. The main traversal methods in DFS for binary trees are:

- Preorder Traversal (current-left-right): Visits the node first, then left subtree, then right subtree.
- Inorder Traversal (left-current-right): Visits left subtree, then the node, then the right subtree.
- Postorder Traversal (left-right-current): Visits left subtree, then right subtree, then the node.

**Breadth-First Search (BFS) algorithms:** BFS explores all nodes at the present depth before moving on to nodes at the next depth level. it is typically implemented using a queue. BFS in a binary tree is commonly referred to as Level Order Traversal.

```java
import java.util.LinkedList;
import java.util.Queue;

class Node {
  int data;
  Node left;
  Node right;

  Node(int d) {
    this.data = d;
    this.left = null;
    this.right = null;
  }
}

class Main {
  // Pre-order DFS: Root, Left, Right
  static void preOrderDFS(Node node) {
    if(node == null) return;
    System.out.println(node.data + " ");
    preOrderDFS(node.left);
    preOrderDFS(node.right);

    // Result: 2 3 5 4
  }

  // In-order DFS: Left, Root, Right
  static void inOrderDFS(Node node) {
    if(node == null) return;
    inOrderDFS(node.left);
    System.out.println(node.data + " ");
    inOrderDFS(node.right);

    // Result: 5 3 2 4
  }

  // Post-order DFS: Left, Right, Root
  static void postOrderDFS(Node node) {
    if(node == null) return;
    postOrderDFS(node.left);
    postOrderDFS(node.right);
    System.out.println(node.data + " ");

    // Result: 5 3 4 2
  }

  // BFS: Level order traversal
  static void BFS(Node root) {
    if(root == null) return;
    Queue<Node> queue = new LinkedList<>();
    queue.add(root);

    while(!queue.isEmpty()) {
      Node node = queue.poll();
      System.out.println(node.data + " ");
      if(node.left != null) queue.add(node.left);
      if(node.right != null) queue.add(node.right);
    }

    // Result: 2 3 4 5
  }

  public static void main(String[] args) {
    // Creating the tree
    Node root = new Node(2);
    root.left = new Node(3);
    root.right = new Node(4);
    root.left.left = new Node(5);
  }
}

```

##### 2. Insertion in Binary Tree

Inserting elements means add a new node into the binary tree. As we know that there is no such ordering of elements in the binary tree, So we do not have to worry about the ordering of node in the binary tree. We would first create a root node in case of empty tree. Then subsequent insertions involve iteratively searching for an empty place at each level of the tree. When an empty left or right child is found, then a new node is inserted there. By convention, insertion always starts with the left child node.

```java
class Main {
  // Function to insert a new node in the binary tree
  static Node insert(Node root, int key) {
    if(root == null) return new Node(key);

    // Create a queue for level order traversal
    Queue<Node> q = new LinkedList<>();
    q.add(root);

    while(!q.isEmpty()) {
      Node temp = q.poll();

      // If left child is empty, insert the new node here
      if(temp.left == null) {
        temp.left = new Node(key);
        break;
      } else {
        q.add(temp.left);
      }

      // If right child is empty, insert the new node here
      if(temp.right == null) {
        temp.right = new Node(key);
        break;
      } else {
        q.add(temp.right);
      }
    }

    return root;
  }
}
```

##### 3. Searching in Binary Tree

Searching for a value in binary tree means looking through the tree to find a node that has that value. Since binary trees do not have a specific order like binary search trees, we typically use any traversal method to search. The most common method are **depth-first search (DFS)** and **breadth-first search (BFS)**. In **DFS**, we start from the root and explore the depth nodes first. In BFS, we explore all the nodes at the present depth level before moving on to the nodes at the next level. We continue this process until we either find the node with the desired value or reach the end of the tree. If the tree is empty or the value isn't found after exploring all possibilities, we conclude that the value does not exist in the tree.

Search in a binary tree using Depth-First Search (DFS):

```java
// Function to search for a value in the binary tree
// using DFS
static boolean searchDFS(Node root, int value) {
  // Base case: If the tree is empty or we've reached a leaf node
  if(root == null) return false;

  // if the node's data is equal to the value we are searching for
  if(root.data == value) return true;

  // Recursively search in the left and right subtrees
  boolean left_res = searchDFS(root.left, value);
  boolean right_res = searchDFS(root.right, value);

  return left_res || right_res;
}
```

##### 5. Deletion in Binary Tree

Deleting a node from a binary tree means removing a specific node while keeping the tree's structure. First, we need to find the root node that we want to delete by traversing through the tree using any traversal method. Then replace the node's value with the value of the last node in the tree (found by traversing to the rightmost leaf), and then delete that last node. This way, the tree structure won't be affected. And remember to check for special cases, like trying to delete from an empty tree to avoid any issues.

```java
// Function to delete a node from the binary tree
static Node deleteNode(Node root, int val) {
  if(root == null) return null;

  // Use a queue to perform BFS
  Queue<Node> q = new LinkedList<>();
  q.add(root);
  Node target = null;

  // Find the target node
  while(!q.isEmpty()) {
    Node curr = q.poll();

    if(curr.data == val) {
      target = curr;
      break;
    }
    if(curr.left != null) q.add(curr.left);
    if(curr.right != null) q.add(curr.right);
  }

  if(target == null) return root;

  // Find the deepest rightmost node and its parent
  Node lastNode = null;
  Node lastParent = null;
  Queue<Node> q1 = new LinkedList<>();
  Queue<Node> parentQueue = new LinkedList<>();

  q1.add(root);
  parentQueue.add(null);

  while(!q1.isEmpty()) {
    Node curr = q1.poll();
    Node parent = parentQueue.poll();

    lastNode = curr;
    lastParent = parent;

    if(curr.left != null) {
      q1.add(curr.left);
      parentQueue.add(curr);
    }

    if(curr.right != null) {
      q1.add(curr.right);
      parentQueue.add(curr);
    }
  }

  // Replace target's value with the last node's value
  target.data = lastNode.data;

  // Remove the last node
  if(lastParent != null) {
    if(lastParent.left == lastNode) lastParent.left = null;
    else lastParent.right = null;
  } else {
    return null;
  }

  return root;
}
```
