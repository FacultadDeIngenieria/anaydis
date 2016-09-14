class: center, middle, inverse

# Tries

---

# Trie

* Guardar caracteres en los nodos intermedios en lugar de la clave
* Guardar el contenido en los nodos hoja
* Usar los caracteres para guiar la búsqueda
* El nombre viene de reTRIEval (Se pronuncia como "try")

---

# Trie

Ejemplo: *TRES TIGRES COMEN TRIGO EN UN TRIGAL*

.center[![]({{site.baseurl}}/presentation/tries/trestigrestrigal.png)]

---

# R-Way Trie

.center[![]({{site.baseurl}}/presentation/tries/rway.png)]

---

# R-Way Trie
### Implementación

```kotlin
/**
 * R-Way Trie Implementation
 */
class RWayTrieMap<V:Any> : Map<String,V> {

    private var head: Node<V>? = null;
    private var size: Int = 0;

    private class Node<V:Any>(var value: V,
                                val next: Array<Node<V>?> = arrayOfNulls(256))

    ...

}
```

---

# R-Way Trie

```kotlin
override fun get(key: String) : V? {
    val node = find(head, key, 0)
    return node?.value
}

override fun contains(key: String) = find(head, key, 0) != null

private fun find(node: Node<V>?, key: String, level: Int): Node<V>? {
    if(node == null) return null
    if(level == key.length) return node
    val next : Int = key[level].toInt()
    return find(node.next[next], key, level+1)
}
```

---

# R-Way Trie

```kotlin
override fun put(key: String, value: V) {
    head = put(head, key, value, 0)
}

private fun put(node: Node<V>?, key: String, value: V, level : Int): Node<V>? {
    return when {
        node == null -> {
            val result = Node(value)
            if (level < key.length) {
                val next = key[level].toInt()
                result.next[next] = put(result.next[next], key, value, level+1)
            } else size++
            result
        }
        level == key.length -> {
            node.value = value
            node
        }
        else -> {
            val next = key[level].toInt()
            node.next[next] = put(node.next[next], key, value, level+1)
            node
        }
    }
}
```
---

# Ternary Search Tree

Similar al R-Way Trie pero con menos referencias nulas

### Cada Nodo contiene:
* El caracter
* El valor (si corresponde)
* El link del medio (cuando el caracter corresponde)
* Los links a izquierda y derecha cuando el caracter buscado es menor o mayor respectivamente


---

# Ternary Search Tree

.center[![]({{site.baseurl}}/presentation/tries/tst.png)]

---

# Ternary Search Tree
### Implementación

```kotlin
/**
 * TST Map implementation
 */
class TSTMap<V:Any> : Map<String,V> {

    private var head: Node<V>? = null;
    private var size: Int = 0;

    private class Node<V>(val chr: Char, var value: V? = null,
                          var left: Node<V>? = null,
                          var middle: Node<V>? = null,
                          var right: Node<V>? = null)

    ...

}
```

---

# Ternary Search Tree

```kotlin
override fun get(key: String): V? {
    val node = find(head, key, 0)
    return node?.value
}

override fun contains(key: String) = find(head, key, 0) != null

private fun find(node: Node<V>?, key: String, level: Int): Node<V>? {
    if (node == null || level >= key.length) return null
    return when (key[level]) {
        node.chr -> when {
            key.length == level+1 -> node
            else -> find(node.middle, key, level+1)
        }
        else -> {
            val next = if (key[level] > node.chr) node.right else node.left
            find(next, key, level)
        }
    }
}
```

---

```kotlin
override fun put(key: String, value: V) {
    head = put(head, key, value, 0)
}

private fun put(node: Node<V>?, key: String, value: V, level: Int): Node<V> {
    return when {
        node == null -> {
            val result = Node<V>(key[level])
            if (level+1 < key.length) {
                result.middle = put(result.middle, key, value, level+1)
            } else {
                result.value = value
                size++
            }
            result
        }
        key[level] == node.chr -> {
            if (level+1 == key.length) {
                if (node.value == null) size++
                node.value = value
            } else {
                node.middle = put(node.middle, key, value, level+1)
            }
            node
        }
        key[level] > node.chr -> {
            node.right = put(node.right, key, value, level)
            node
        }
        else -> {
            node.left = put(node.left, key, value, level)
            node
        }
    }
}
```

---

# Ternary Search Tree
##Análisis

* Muy rápido. Depende de la longitud de la clave (simil hashing)
* Ocupación de memoria proporcional a la cantidad de Items
* Las claves estan ordenadas ☞ Se puede hacer búsqueda por rangos
* Se pueden implementar búsquedas parciales ☞ "p..e" (pepe, pide, pose, ...)

---

# Binary Trie

* Bifurcación en función de los bits de la clave
* No guardo nada en los nodos intermedios
* Solo hay claves y datos en las hojas

---

# Binary Trie

.center[![]({{site.baseurl}}/presentation/tries/btrie.png)]

---

# Binary Trie

```kotlin
/**
 * Binary Trie Map implementation
 */
class BinaryTrieMap<V:Any> : Map<String,V> {

    private var head: Node<V>? = null
    private var size = 0

    private class Node<V>(val key: String, var value: V? = null,
                          var left: Node<V>? = null,
                          var right: Node<V>? = null) {
        fun isLeaf(): Boolean = left == null && right == null
    }

    ...

}
```

---

# Binary Trie

```kotlin
override operator fun get(key: String): V? {
    val node = find(head, key, 0)
    return node?.value
}

override fun contains(key: String) = find(head, key, 0) != null

/**
 * This code assumes that the keys are distinct and (if the keys may be
 * of different length) that no key is prefix of another.
 */
private fun find(node: Node<V>?, key: String, level: Int): Node<V>? {
    if (node == null) return null
    if (node.isLeaf()) return if (key == node.key) node else null
    return find(if (bitAt(key, level)) node.right else node.left, key, level + 1)
}
```

---

# Binary Trie

```kotlin
override fun put(key: String, value: V) {
    head = put(head, Node(key, value), 0)
}

private fun put(node: Node<V>?, value: Node<V>, level: Int): Node<V>? {
    return when {
        node == null -> {
            size++
            return value
        }
        node.isLeaf() -> when {
            node.key == value.key -> {
                node.value = value.value // Override key-matching previous node with updated value
                node
            }
            else -> split(value, node, level)
        }
        else -> {
            if (bitAt(value.key, level)) node.right = put(node.right, value, level + 1)
            else node.left = put(node.left, value, level + 1)
            size++
            node
        }
    }
}
```

---

# Binary Trie

```kotlin
private fun split(a: Node<V>, b: Node<V>, level: Int): Node<V> {
    val result = Node<V>("")

    when (bitAt(a.key, level).toInt() * 2 + bitAt(b.key, level).toInt()) {
        0 -> result.left = split(a, b, level + 1)
        3 -> result.right = split(a, b, level + 1)
        1 -> {
            result.left = a
            result.right = b
        }
        2 -> {
            result.left = b
            result.right = a
        }
    }
    return result
}

private fun bitAt(s: String, nth: Int): Boolean {
    val pos = nth / 8
    return pos < s.length && (s[pos].toInt() shr (nth % 8) and 1) != 0
}
```
