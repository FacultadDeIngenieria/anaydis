class: center, middle, inverse

# Repaso Primer Parcial

## Wildcard Search en TST y Selection Sort en listas enlazadas

???

Esta clase trabaja solamente dos ejercicios: búsqueda con comodines sobre un TST y Selection Sort sobre una lista simplemente enlazada.

---

# Los dos problemas

## Wildcard Search

Recorrer un TST sin perder las alternativas que permite `*`.

## Selection Sort con Linked Lists

Ordenar cambiando enlaces, sin intercambiar valores ni copiar la lista a otra estructura.

???

En ambos ejercicios importa entender qué representa cada referencia. El código sale después de identificar qué caminos o enlaces deben cambiar.

---

# Wildcard Search

Un nodo del TST contiene:

```java
class Node<V> {
    char chr;
    V value;
    Node<V> left;
    Node<V> middle;
    Node<V> right;
}
```

* `left`: caracteres menores para la misma posición
* `right`: caracteres mayores para la misma posición
* `middle`: siguiente carácter de la clave
* `value != null`: el recorrido forma una clave almacenada

???

Left y right no avanzan en el patrón. Middle sí avanza. Esta distinción controla toda la solución.

---

# Regla del comodín

El significado de `*` depende de su posición:

* En una posición intermedia representa exactamente un carácter
* Como último carácter acepta cualquier continuación no vacía

```text
d**o  → dado, dedo, dejo, dijo
HOL*  → HOLA, HOLANDA, HOLAAAAAAA
```

`d**o` exige claves de cuatro caracteres.

`HOL*` exige al menos un carácter después de `HOL`.

---

# Firma y estado de la búsqueda

```java
public List<String> wildcard(Node<V> root, String pattern)
```

El método recursivo necesita conservar:

```java
Node<V> node       // nodo actual del TST
String pattern     // patrón completo
int index          // posición actual del patrón
String prefix      // caracteres ya aceptados
List<String> result
```

???

El índice sólo cambia al seguir middle. El prefijo sólo cambia cuando aceptamos el carácter del nodo actual.

---

# Paso 1: validar y preparar el resultado

```java
public List<String> wildcard(Node<V> root, String pattern) {
    final List<String> result = new ArrayList<>();

    if (root == null || pattern == null || pattern.isEmpty()) {
        return result;
    }

    wildcard(root, pattern, 0, "", result);
    return result;
}
```

La búsqueda comienza:

* En la raíz
* En la posición `0` del patrón
* Con un prefijo vacío

---

# Paso 2: identificar el caso actual

```java
private void wildcard(
        Node<V> node,
        String pattern,
        int index,
        String prefix,
        List<String> result
) {
    if (node == null) return;

    final char expected = pattern.charAt(index);
    final boolean isWildcard = expected == '*';
    final boolean isLast = index == pattern.length() - 1;

    // Continúa la búsqueda...
}
```

Cada llamada debe decidir si compara una letra, procesa un `*` intermedio o expande un `*` final.

---

# Paso 3: resolver el asterisco final

```java
if (isWildcard && isLast) {
    collect(node, result, prefix);
    return;
}
```

`collect` agrega todas las claves existentes desde ese nivel:

```java
private void collect(Node<V> node,
                     List<String> result,
                     String key) {
    if (node.left != null) collect(node.left, result, key);
    if (node.value != null) result.add(key + node.chr);
    if (node.middle != null) collect(node.middle, result, key + node.chr);
    if (node.right != null) collect(node.right, result, key);
}
```

???

El asterisco final evita seguir interpretando el patrón. Desde este punto aceptamos cualquier continuación almacenada.

---

# Paso 4: explorar caracteres alternativos

```java
if (isWildcard || expected < node.chr) {
    wildcard(node.left, pattern, index, prefix, result);
}

if (isWildcard || expected > node.chr) {
    wildcard(node.right, pattern, index, prefix, result);
}
```

Con una letra concreta, el orden del TST permite elegir un solo lado.

Con un `*` intermedio, cualquier carácter sirve y hay que explorar ambos lados.

El índice no cambia porque `left` y `right` siguen representando la misma posición de la clave.

---

# Paso 5: aceptar el carácter actual

```java
if (isWildcard || expected == node.chr) {
    final String current = prefix + node.chr;

    if (isLast) {
        if (node.value != null) {
            result.add(current);
        }
    } else {
        wildcard(node.middle, pattern, index + 1,
                 current, result);
    }
}
```

Al aceptar `node.chr`:

* Se agrega el carácter al prefijo
* Se avanza el índice al entrar por `middle`
* Si terminó el patrón, sólo se agrega una clave completa

---

# Wildcard completo

```java
private void wildcard(Node<V> node, String pattern, int index,
                      String prefix, List<String> result) {
    if (node == null) return;

    final char expected = pattern.charAt(index);
    final boolean wildcard = expected == '*';
    final boolean last = index == pattern.length() - 1;

    if (wildcard && last) {
        collect(node, result, prefix);
        return;
    }

    if (wildcard || expected < node.chr)
        wildcard(node.left, pattern, index, prefix, result);

    if (wildcard || expected == node.chr) {
        final String current = prefix + node.chr;
        if (last) {
            if (node.value != null) result.add(current);
        } else {
            wildcard(node.middle, pattern, index + 1, current, result);
        }
    }

    if (wildcard || expected > node.chr)
        wildcard(node.right, pattern, index, prefix, result);
}
```

---

# Traza de `d**o`

```text
Patrón:  d  *  *  o
Índice:  0  1  2  3
```

1. `d` elige el nodo que contiene `d`
2. El primer `*` prueba todos los caracteres del segundo nivel
3. El segundo `*` prueba todos los caracteres del tercer nivel
4. `o` vuelve a elegir una coincidencia exacta
5. La palabra se agrega sólo si el nodo de `o` tiene valor

```text
dado  ✓
dedo  ✓
dejo  ✓
dijo  ✓
dedos ✗  el patrón ya terminó
```

---

# Traza de `HOL*`

```text
H → O → L → *
```

1. `H`, `O` y `L` avanzan por enlaces `middle`
2. La llamada correspondiente a `*` comienza después de `L`
3. Como `*` es el último carácter, se ejecuta `collect`
4. `collect` recupera todas las continuaciones existentes

```text
HOLA        ✓
HOLANDA     ✓
HOLAAAAAAA  ✓
HOL         ✗  falta el carácter representado por *
HOJA        ✗  no coincide el prefijo HOL
```

---

# Selection Sort con listas enlazadas

La lista se ordena reutilizando sus nodos.

```java
public interface LinkedListSorter {
    <T> Node<T> sort(Node<T> list, Comparator<T> comparator);
}
```

Restricciones del ejercicio:

* No copiar los elementos a otra colección
* No intercambiar los valores de los nodos
* Cambiar únicamente los enlaces `next`

---

# Estrategia

En cada iteración:

1. Buscar el máximo de la lista de entrada
2. Desenlazarlo
3. Insertarlo al comienzo de la lista de salida

```text
entrada:  4 → 2 → 7 → 3
salida:   null

extraer 7

entrada:  4 → 2 → 3
salida:   7
```

???

Los máximos se extraen de mayor a menor, pero se agregan al frente. La salida termina ordenada de menor a mayor.

---

# El nodo cabecera

```java
final Node<T> head = new Node<>(null, list);
```

`head` es un nodo auxiliar anterior al primer elemento real.

```text
head → 4 → 2 → 7 → 3 → null
```

Permite eliminar cualquier elemento con la misma operación, incluso cuando el máximo ocupa la primera posición.

???

Sin el nodo cabecera, extraer el primer nodo exigiría un caso especial y cambiar la referencia original de la lista.

---

# Buscar el predecesor del máximo

Para extraer un nodo de una lista simplemente enlazada necesitamos conocer el nodo anterior.

```text
... → anterior → máximo → siguiente → ...
```

La operación será:

```java
anterior.setNext(maximo.next());
```

Por eso `findMax` devuelve el predecesor del máximo, no el máximo.

---

# Paso 1: recorrer los candidatos

```java
private static <T> Node<T> findMax(
        Node<T> h,
        Comparator<T> comparator
) {
    for (Node<T> t = h;
         t.next() != null;
         t = t.next()) {

        // Comparar h.next() contra t.next()
    }

    return h;
}
```

Durante el recorrido:

* `t.next()` es el candidato actual
* `h.next()` es el mayor encontrado hasta el momento
* `t` y `h` son los predecesores de esos elementos

---

# Paso 2: actualizar el máximo

```java
if (comparator.compare(
        h.next().value(),
        t.next().value()
) < 0) {
    h = t;
}
```

Si `h.next()` es menor que `t.next()`, el candidato actual pasa a ser el nuevo máximo.

```java
private static <T> Node<T> findMax(
        Node<T> h,
        Comparator<T> comparator
) {
    for (Node<T> t = h; t.next() != null; t = t.next())
        if (comparator.compare(h.next().value(),
                               t.next().value()) < 0)
            h = t;
    return h;
}
```

---

# Traza de `findMax`

```text
head → 4 → 2 → 7 → 3 → null
```

| Candidato | Mayor actual | Acción |
|-----------|--------------|--------|
| 4 | 4 | mantener |
| 2 | 4 | mantener |
| 7 | 4 | actualizar |
| 3 | 7 | mantener |

Resultado: `findMax` devuelve el nodo que contiene `2`, porque es el predecesor de `7`.

---

# Paso 3: repetir mientras queden nodos

```java
final Node<T> head = new Node<>(null, list);
Node<T> out = null;

while (head.next() != null) {
    final Node<T> max = findMax(head, comparator);
    final Node<T> t = max.next();

    // Extraer t y agregarlo a out.
}
```

El ciclo termina cuando la lista de entrada queda vacía.

* `max` es el predecesor
* `t` es el nodo máximo

---

# Paso 4: desenlazar el máximo

Antes:

```text
max → t → siguiente
```

Operación:

```java
max.setNext(t.next());
```

Después:

```text
max ─────→ siguiente

t
```

El nodo `t` queda disponible para insertarlo en la salida.

---

# Paso 5: agregar el máximo al frente

Antes:

```text
t          out → ...
```

Operaciones:

```java
t.setNext(out);
out = t;
```

Después:

```text
out → t → salida anterior
```

El algoritmo reutiliza el nodo extraído. No crea una copia.

---

# Selection Sort completo

```java
@Override
public <T> Node<T> sort(Node<T> list,
                        Comparator<T> comparator) {
    final Node<T> head = new Node<>(null, list);
    Node<T> out = null;

    while (head.next() != null) {
        final Node<T> max = findMax(head, comparator);
        final Node<T> t = max.next();

        max.setNext(t.next());
        t.setNext(out);
        out = t;
    }

    return out;
}
```

---

# Traza completa

```text
Entrada inicial: 4 → 2 → 7 → 3
```

| Máximo extraído | Entrada restante | Salida |
|-----------------|-------------------|--------|
| 7 | 4 → 2 → 3 | 7 |
| 4 | 2 → 3 | 4 → 7 |
| 3 | 2 | 3 → 4 → 7 |
| 2 | vacía | 2 → 3 → 4 → 7 |

Resultado:

```text
2 → 3 → 4 → 7
```

---

# Invariante y costo

Después de cada iteración:

* La entrada tiene un nodo menos
* La salida contiene los máximos ya encontrados
* La salida permanece ordenada en forma ascendente

Para `N` nodos se realizan aproximadamente:

```text
(N - 1) + (N - 2) + ... + 1 = N(N - 1) / 2
```

El tiempo es `O(N²)` y la memoria auxiliar es `O(1)`.

---

# Errores frecuentes

## Wildcard Search

* Avanzar el índice al recorrer `left` o `right`
* Tratar todos los `*` como sufijos completos
* Agregar un resultado aunque `value == null`
* Hacer que `HOL*` también acepte `HOL`

## Selection Sort

* Devolver el máximo en lugar de su predecesor
* Perder `t.next()` antes de desenlazar
* Olvidar conectar `t` con la salida anterior
* Buscar el mínimo y mantener la misma reconstrucción

---

class: center, middle, inverse

# Repaso Primer Parcial

## Dos ideas para conservar

En el TST, `middle` avanza al siguiente carácter.

En la lista enlazada, ordenar significa reconstruir enlaces.
