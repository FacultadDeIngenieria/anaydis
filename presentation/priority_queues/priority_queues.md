class: center, middle, inverse

# Priority Queues

---

# Problem

* Many applications require that we process items having keys in order,
	* but not necessarily in full sorted order, and 
	* not necessarily all at once
* What we do is:
	* we collect a set of items
	* process the one with the largest key
	* collect more items
	* process the one with the current largest key
	* and so forth
* An appropriate data type that solves that problem supports two operations:
	* remove the maximum
	* insert
* Such a data type is called a **priority queue**

???

For example, you are likely to have a computer (or a cellphone) that is capable of running several applications at the same time. This effect is typically achieved by assigning a priority to events associated with applications, then always choosing to process next the highest-priority event. For example, most cellphones are likely to process an incoming call with higher priority than a game application.

---

# API

```kotlin

interface PriorityQueue<K> {

    /** Insert a given key */
    fun insert(key: K)

    /** Remove and return the maximum key */
    fun pop(): K

    /** Return the maximum key without removing it */
    fun peek(): K

    /** Returns the number of keys in this priority queue */
    fun size(): Int

    /** Returns true if this priority queue contains no keys */
    fun isEmpty(): Boolean = size() == 0
}

```

* Parameterized type *K* that implements the *Comparable* interface
* Records can have duplicate keys, *maximum* means *any* record with the *largest* key value

---

# Elementary implementations

* Using priority queues is similar to using *queues* (remove the oldest) and *stacks* (remove the newest)
	* but implementing them efficiently is more challenging
* *Array representation (unordered)*: 
	* The simplest priority queue implementation based on our code for pushdown stacks
	* The code for *insert* in the priority queue is the same as for *push* in the stack
	* To implement *remove* the maximum:
		* Add code like the inner loop of selection sort to exchange the maximum item with the item at the end and then delete that one, as we did with **pop()** for stacks
* *Array representation (ordered)*: 
	* Another approach is to add code for *insert* to move larger entries one position to the right, thus keeping the entries in the array in order (as in insertion sort)
	* The largest item is always at the end, and the code for *remove the maximum* in the priority queue is the same as for *pop* in the stack

---

# Elementary implementations

* *Linked-list representations (unordered and reverse-ordered)*:
	* We can start with our linked-list code for pushdown stacks, either modifying the code for **pop()** to find and return the maximum, or
	* the code for **push()** to keep items in reverse order and the code for **pop()** to unlink and return the first (maximum) item on the list.

???

Using unordered sequences is the prototypical lazy approach to this problem, where we defer doing work until necessary (to find the maximum); using ordered sequences is the prototypical eager approach to the problem, where we do as much work as we can up front (keep the list sorted on insertion) to make later operations efficient.

---

# Sequence of operations

.center.full-width[![]({{site.baseurl}}/presentation/priority_queues/pq-sequence.png)]

---

# Orders of growth

.center.full-width[![]({{site.baseurl}}/presentation/priority_queues/pq-orders.png)]

---

# Array implementation - which?

```kotlin
class ArrayPriorityQueue<K>(max: Int, private val comparator: Comparator<K>) : PriorityQueue<K> {

    private val keys: Array<K?> = Generics.fill(max)
    private var size = 0

    override fun insert(key: K) {
        keys[size++] = key
    }

    override fun pop(): K {
        val max = max()
        exch(max, size - 1)
        return keys[--size]!!
    }

    override fun peek(): K = keys[max()]!!

    override fun size(): Int = size

    ...

}
```

* exch(): the one and only exch() you already know
* max(): finds the max :-)

---

# Heap implementation

* Finding an implementation where both operations are guaranteed to be fast is a more interesting task
* **Heap definitions**
	* The *binary heap* is a data structure that can efficiently support the basic priority-queue operations
	* The items are stored in an array such that each key is guaranteed to be larger than (or equal to) the keys at two other specific positions
	* In turn, each of those keys must be larger than two more keys, and so forth
	* This ordering is easy to see if we view the keys as being in a binary tree structure with edges from each key to the two keys known to be smaller
* **Definition** 
	* A binary tree is *heap-ordered* if the key in each node is larger than (or equal to) the keys in that nodes two children (if any)
* **Proposition** 
	* The largest key in a heap-ordered binary tree is found at the root.

---

# Heap implementation

* We can impose the heap-ordering restriction on any binary tree. It is particularly convenient, however, to use a complete binary tree like the one below

.center.full-width[![]({{site.baseurl}}/presentation/priority_queues/heap-ordered-bt.png)]

---

# Heap implementation

* We represent complete binary trees sequentially within an array by putting the nodes with *level* order:
	* with the root at position 1, its children at positions 2 and 3, their children in positions 4, 5, 6 and 7, and so on
* **Definition.** A binary heap is a set of nodes with keys arranged in a complete heap-ordered binary tree, represented in level order in an array (not using the first entry)
* In a heap: 
	* the parent of the node in position *k* is in position *k/2*
	* the two children of the node in position *k* are in positions *2k* and *2k + 1*
	* we can travel up and down by doing simple arithmetic on array indices
		* to move up the tree from *a[k]* we set *k* to *k/2*
		* to move down the tree we set *k* to *2*k* or *2*k+1*

---

# Heap implementation

.center[![]({{site.baseurl}}/presentation/priority_queues/binary-heap.png)]

---

# Heap implementation

* We represent a heap of size *n* in private array **pq[]** of length *n + 1*
	* **pq[0]** unused and the heap in **pq[1]** through **pq[n]**
* The heap operations important to us at first are the ones that could violate the heap condition
	* then traveling through the heap, modifying the heap as required to ensure that the heap condition is satisfied everywhere
	* We refer to this process as *reheapifying*, or *restoring heap order*
* Those are:
	* *Bottom-up reheapify (swim)*
	* *Top-down heapify (sink)*

---

# Bottom-up reheapify (swim)

* If the heap order is violated because a node's key becomes larger than that node's parents key, then we can make progress toward fixing the violation by exchanging the node with its parent
* After the exchange, the node is larger than both its children
	* one is the old parent, and the other is smaller than the old parent because it was a child of that node
	* but the node may still be larger than its parent
* We can fix that violation in the same way, and so forth, moving up the heap until we reach a node with a larger key, or the root

---

# Bottom-up reheapify (swim)

.center[![]({{site.baseurl}}/presentation/priority_queues/swim.png)]

---

# Bottom-up reheapify (swim)

```kotlin
private fun swim(k: Int) {
  	var pos = k
  	while (pos > 1 && less(pos / 2, pos)) {
    		exch(pos / 2, pos);
    		pos /= 2;
  	}
}
```

---

# Top-down heapify (sink)

* If the heap order is violated because a node's key becomes smaller than one or both of that node's children's keys, then we can make progress toward fixing the violation by exchanging the node with the larger of its two children
	* This switch may cause a violation at the child
	* we fix that violation in the same way, and so forth, moving down the heap until we reach a node with both children smaller, or the bottom

---

# Top-down heapify (sink)

.center[![]({{site.baseurl}}/presentation/priority_queues/sink.png)]

---

# Top-down heapify (sink)

```kotlin
private fun sink(k: Int) {
    var pos = k
    while (2 * pos <= size) {
        var j = 2 * pos
        if (j < size && less(j, j + 1)) j++
        if (!less(pos, j)) break
        exch(pos, j)
        pos = j
    }
}
```

---

# Heap-based priority queue

* These **sink()** and **swim()** operations provide the basis for efficient implementation of the priority-queue API
	* *Insert.* We add the new item at the end of the array, increment the size of the heap, and then swim up through the heap with that item to restore the heap condition
	* *Remove the maximum.* We take the largest item off the top, put the item from the end of the heap at the top, decrement the size of the heap, and then sink down through the heap with that item to restore the heap condition

---

# Heap-based priority queue


```kotlin
class HeapPriorityQueue<K>(max: Int, private val comparator: Comparator<K>) : PriorityQueue<K> {

    internal val keys: Array<K?> = Generics.fill(max + 1) // Not zero based!
    private var size = 0

    override fun insert(key: K) {
        keys[++size] = key
        swim(size)
    }

    override fun pop(): K {
        if (isEmpty()) throw NoSuchElementException("Empty priority queue!")
        val result = keys[1]
        exch(1, size--)
        sink(1)
        keys[size+1] = null; // Avoid loitering and help garbage collector
        return result!!
    }

    override fun peek(): K {
        if (isEmpty()) throw NoSuchElementException("Empty priority queue!")
        return keys[1]!!
    }

    override fun size(): Int = size

    ...
}
```
