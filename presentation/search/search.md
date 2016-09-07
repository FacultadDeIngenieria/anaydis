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

```kotlin
class ArrayMap<K, V>(max : Int) : Map<K, V>
{
    private val keys: Array<K?> = fill(max)
    private val values: Array<V?> = fill(max)
    private var size : Int = 0

    override fun size() = size

    override fun contains(key: K) = indexOf(key) != -1

    private fun indexOf(key : K) : Int {
        for(index in keys.indices) {
            if(key!!.equals(keys[index])) return index
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
        size--;
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


