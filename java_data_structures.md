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

## Advanced Data Structures

### Binary Tree

Each node has at most two children (left and right).

```java
public class TreeNode<T> {
  T data;
  TreeNode<T> left;
  TreeNode<T> right;

  public TreeNode(T data) {
    this.data = data;
    this.left = null;
    this.right = null;
  }
}
```
