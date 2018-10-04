class: center, middle, inverse

# Immutable Collections

---

# Agenda

* Threads
* Immutable Linked List
* Immutable Banker’s Queue
* Immutable Trees

???

https://www.youtube.com/watch?v=pNhBQJN44YQ

---

# Threads

* Tasks you want to execute at roughly the same time.
* Why not just write multiple applications?
  * Threads running in the same application, share the same memory space in the computer.
  * Share information seamlessly.

.center[![]({{site.baseurl}}/presentation/immutables/threads.png)]

???

http://book.javanb.com/java-threads-3rd/jthreads3-CHP-2-SECT-1.html

---

# Memory Sharing

* Threads seem to be the same as programs.
* The key difference is that the global memory is the entire Java heap:
  * threads can transparently share access between any object in the heap.
  * Each thread still has its own space for local variables (variables specific to the method the thread is executing).
* But objects are shared automatically and transparently.

---

# Functional Data Struct

* Immutable.
* What we want…
  * Comparable asymptotic performance to equivalent mutable structures...
* Structural sharing
  * the main tool to achieve performance.

---

# Linked List

.center[![]({{site.baseurl}}/presentation/immutables/linked_list.png)]

---

# Complexity

.center[![]({{site.baseurl}}/presentation/immutables/list_complexity.png)]

---

# Anatomy

* A list is either…
  * A “cons” cell with a value and a tail
  * An empty list, called “nil”
* These are the only cases!

---

# Implementation

```kotlin
/** An immutable list.  */
interface List<T> {

    /** Returns head element.  */
    fun head(): T

    /** Returns tail elements.  */
    fun tail(): List<T>

    /** Returns true if empty.  */
    val isEmpty: Boolean

    /** Returns a new list with all elements of this list in reversed order.  */
    fun reverse(): List<T>

    companion object {

        /** Empty list.  */
        fun <T> nil(): List<T> = cast(Node.NIL)

        /** Adds an element to the list.  */
        fun <T> cons(head: T, tail: List<T>): List<T> = Node(head, tail)

    }
}

```

---

# Usage

```kotlin
fun main(args : Array<String>) {
    val list = cons(3, cons(2, cons(1, nil())))
    System.out.println(list) // (3) -> (2) -> (1) -> Nil

    val length = length(list)
    println(length) // => 3
}

private fun <T> length(list: List<T>): Int {
    return if (list.isEmpty) 0 else 1 + length(list.tail())
}
```

???

Notar que en Kotlin el main está declarado en una clase.

---

layout: true

# Structural Sharing

---

```kotlin
val foo = cons("a", cons("b", cons("c", cons("d", nil()))))
```

.center[![]({{site.baseurl}}/presentation/immutables/structural_sharing_01.png)]

---

```kotlin
val bar = cons("h", foo.tail())
```

.center[![]({{site.baseurl}}/presentation/immutables/structural_sharing_02.png)]

---

```kotlin
val baz = cons("m", cons("n", foo))
```

.center[![]({{site.baseurl}}/presentation/immutables/structural_sharing_03.png)]

---

```kotlin
val raz = cons("q", cons("r", baz.tail()))
```

.center[![]({{site.baseurl}}/presentation/immutables/structural_sharing_04.png)]

---

layout: false

# Banker’s Queue

.center[![]({{site.baseurl}}/presentation/immutables/queue.png)]

---

# Motivation

* We want a functional queue.
  * a functional FIFO.
* Linked list is obvious
  * prepend and last are opposing.
  * One will be O(1), the other O(n).
* Can we have our cake and eat it too?

---

# Complexity

.center[![]({{site.baseurl}}/presentation/immutables/queue_complexity_01.png)]

---

# Complexity

.center[![]({{site.baseurl}}/presentation/immutables/queue_complexity_02.png)]

---

# Anatomy

* Naïve persistent queue.
* Two lazy singly-linked lists:
  * Front list (for dequeue).
  * Rear list (for enqueue).
* Periodically reverse rear into the front.
* Lazy amortization.

---

# Amortization

* Most operations are legitimately fast.
  * Few operations are very slow.
* Laziness distributes the work.
* Net result: constant factor degradation.
  * Translation: the net average is fast.
* Also works without laziness!

---

# Implementation

```kotlin
interface Queue<T> {

    /** Enqueue given value and return the new queue.  */
    fun enqueue(value: T): Queue<T>

    /** Dequeue last value and rest of the queue.  */
    fun dequeue(): Result<T>

    /** Returns true if empty.  */
    val isEmpty: Boolean

    /** Result containing a value and a Queue.  */
    class Result<T>(val value: T, val queue: Queue<T>)
}
```

---

# Trees

* Consider a binary tree where every node has the recursive invariant that subnodes on the left are less than the node, and subnodes on the right are greater than the node.

* For instance, the set of data xs = [a, b, c, d, f, g, h]

.center[![]({{site.baseurl}}/presentation/immutables/binary_tree.png)]

???

http://en.wikipedia.org/wiki/Persistent_data_structure

---

# Trees

* After executing: ys = insert ("e", xs) we end up with the following:

.center[![]({{site.baseurl}}/presentation/immutables/binary_tree_insertion.png)]

---

# Trees

* Firstly the original tree (xs) persists.
* Secondly many common nodes are shared between the old tree and the new tree.
