class: center, middle, inverse

# Search

---

# ADT Map

* Arreglo Asociativo / Tabla Símbolos
* Operaciones
    * Buscar una entrada existente
```kotlin
/** Returns the value to which the specified key is mapped */
fun get(key : K) : V?
```
    * Agregar / Modificar una entrada
```kotlin
/** Associates the specified value with the specified key in this map */
fun put(key : K, value : V)
```
    * Remover una entrada
```kotlin
/** Removes the mapping for a key from this map if it is present */
fun remove(key : K)
```

---

# ADT Map

```kotlin
/**
 * Map interface. Defines an object that maps keys to values.
 */
interface Map<K, V> {

    /** Returns the value to which the specified key is mapped */
    fun get(key : K) : V?

    /** Associates the specified value with the specified key in this map */
    fun put(key : K, value : V)

    /** Removes the mapping for a key from this map if it is present */
    fun remove(key : K)

    /** Returns the number of key-value mappings in this map */
    fun size() : Int

    /** Returns true if this map contains no key-value mappings */
    fun isEmpty() : Boolean = size() == 0

    /** Returns true if this map contains a mapping for the specified key */
    fun contains(key : K) : Boolean
}
```

---

# Array based implementation

[Kotlin Array Doc](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-array/)

```kotlin
class ArrayMap<K, V>(max : Int) : Map<K, V>
{
    private val keys: Array<K?> = fill(max)
    private val values: Array<V?> = fill(max)
    private var size : Int = 0

    override fun size() = size

    override fun contains(key: K) = indexOf(key) != -1

    private fun indexOf(key : K) : Int {
        for(index in 0..size-1) {
            if(key.equals(keys[index])) return index
        }
        return -1
    }

    override fun get(key: K): V? {
        val index = indexOf(key)
        return when {
            index != -1 -> values[index]
            else -> null
        }
    }
```

???

Null safety on Kotlin: https://kotlinlang.org/docs/reference/null-safety.html

---

# Array based implementation

```kotlin
override fun put(key: K, value: V) {
    var index = indexOf(key)
    if(index == -1) {
        index = size++
        keys[index] = key
    }
    values[index] = value
}

override fun remove(key: K) {
    val index = indexOf(key)
    if(index != -1) {
        for(i in index+1..size-1) {
            keys[i-1] = keys[i]
            values[i-1] = values[i]
        }
        size--
    }
}
```

---

# Array based with binary search

```kotlin
private fun indexOf(key : K) : Int {
    val index = find(key, 0, size-1)
    return if(index < 0) -1 else index
}

private fun find(key: K, low: Int, high: Int): Int {
    if(low > high) return -(low+1)

    val middle = (low + high) / 2
    val cmp = comparator.compare(key, keys[middle])
    return when {
        cmp == 0 -> middle
        cmp < 0 -> find(key, 0, middle-1)
        else -> find(key, middle+1, high)
    }
}

override fun put(key: K, value: V) {
    var index = find(key, 0, size-1)
    if(index < 0) {
        index = (-index) -1
        for(i in size++.downTo(index+1)) {
            keys[i] = keys[i-1]
            values[i] = values[i-1]
        }
        keys[index] = key
    }
    values[index] = value
}
```

---

# Análisis

* Búsqueda: O(log<sub>2</sub>N)
* Inserción: O(N)
* Ideal para pocas actualizaciones

---

# Búsqueda por Interpolación

* Idea: usar el **valor** de la clave para estimar la posición

.center[![]({{site.baseurl}}/presentation/search/interpolacion.png)]

* Orden inserción (para distribución uniforme): O(log<sub>2</sub> log<sub>2</sub> N)

---

# Binary Trees

.center[![]({{site.baseurl}}/presentation/search/arbol_binario.gif)]

---

# Tree Map

```kotlin
/**
 * Tree map
 */
abstract class TreeMap<K, V> : Map<K, V> {

    protected var head : Node<K, V>? = null;
    protected var size : Int = 0;

    override fun size(): Int = size

    protected class Node<K, V>(var key : K, var value : V,
                               var left : Node<K, V>? = null,
                               var right : Node<K, V>? = null)
    {
        override fun toString() = "$key[$value]"
    }
}
```

---

# Binary Tree

```kotlin
override fun get(key: K): V? {
    val node = find(head, key)
    return node?.value
}

override fun contains(key: K): Boolean = find(head, key) != null

private fun find(node: Node<K, V>?, key: K): Node<K, V>? {
    if(node == null) return null
    val cmp = comparator.compare(key, node.key)
    return when {
        cmp == 0 -> node
        cmp < 0 -> find(node.left, key)
        else -> find(node.right, key)
    }
}
```

---

# Binary Tree

```kotlin
override fun put(key: K, value: V) {
    head = put(head, key, value)
}

private fun put(node : Node<K, V>?, key: K, value: V): Node<K, V> {
    return when (node) {
        null -> {
            size++
            Node(key, value)
        }
        else -> {
            val cmp = comparator.compare(key, node.key)
            when {
                cmp < 0 -> node.left = put(node.left, key, value)
                cmp > 0 -> node.right = put(node.right, key, value)
                else -> node.value = value
            }
            node
        }
    }
}
```

---

```kotlin
override fun remove(key: K) {
    head = remove(head, key)
}

private fun remove(node : Node<K, V>?, key: K): Node<K, V>? {
    if(node == null) return null
    val cmp = comparator.compare(key, node.key)
    return when {
        cmp < 0 -> {
            node.left = remove(node.left, key)
            node
        }
        cmp > 0 -> {
            node.right = remove(node.right, key)
            node
        }
        else -> {
            size--
            when {
                node.left == null -> node.right
                node.right == null -> node.left
                else -> {
                    val next : Node<K, V> = first(node.right)
                    node.key = next.key
                    node.value = next.value
                    next.key = key
                    node.right = remove(node.right, key)
                    node
                }
            }
        }
    }
}
```

---

# Árboles con inserción en raíz

* Insertar de forma que el nuevo nodo pase a ser la raíz del árbol
* Búsqueda más rápida para los nodos más recientes
* Implementación:
    * Inserto a derecha -> roto a izquierda
    * Inserto a izquierda -> roto a derecha

---

# Rotaciones

.center[![]({{site.baseurl}}/presentation/search/rotations.png)]

---

# Rotaciones

```kotlin
private fun rotateLeft(node : Node<K, V>) : Node<K, V> {
    val result = node.right!!
    node.right = result.left
    result.left = node
    return result
}

private fun rotateRight(node : Node<K, V>) : Node<K, V> {
    val result = node.left!!
    node.left = result.right
    result.right = node
    return result
}
```

---

# Root insertion tree

.center[![]({{site.baseurl}}/presentation/search/root_insertion_tree.gif)]


---

# Root insertion tree

```kotlin
override fun put(key: K, value: V) {
    head = rootPut(head, key, value)
}

private fun rootPut(node: Node<K, V>?, key: K, value: V): Node<K, V>? {
    return when (node) {
        null -> {
            size++
            Node(key, value)
        }
        else -> {
            val cmp = comparator.compare(key, node.key)
            when {
                cmp < 0 -> {
                    node.left = rootPut(node.left, key, value)
                    rotateRight(node)
                }
                cmp > 0 -> {
                    node.right = rootPut(node.right, key, value)
                    rotateLeft(node)
                }
                else -> {
                    node.value = value
                    node
                }
            }
        }
    }
}
```

---

# Árboles Balanceados

* Objetivo: Garantizar O(log<sub>2</sub>N)
* Probabilístico
* Amortizado (promedio)

---

# Randomized Tree

* Insertar por raíz en forma random
* Probabilístico

---

# Splay Trees

* El árbol se reajusta con cada operación
* Cuando agrego o busco X muevo a X hacia la raiz usando una rotación “doble”
* Mantengo el árbol balanceado sin usar información adicional
* Amortizado
    * Una operación puede tardar: N
    * N operaciones tardan: N * log<sub>2</sub>N
