class: center, middle, inverse

# Mergesort

---

# Conceptos

* Algoritmo tipo ‘Divide & Conquer’
    * Resuelvo un problema mas grande dividiendolo en partes
    * Resolviendo esas partes 
    * Y combinando esas soluciones para resolver el problema original 

* Lineal-logarítmico (n ⋅ log2n) garantizado

* Estable

* Práctico para listas

* Desventaja: espacio extra proporcional a `n`

---

# Merging

.center[![]({{site.baseurl}}/presentation/mergesort/merging.png)]

---

# Código

```java
int[] merge(int[] a, int[] b) {
	int[] result = new int[a.length + b.length];

	for (int i = 0, j = 0, k = 0; k < result.length; k++) {
		if(i >= a.length) {
			result[k] = b[i++];
		}
		else if (j >= b.length) {
			result[k] = a[j++];
		}
		else {
			result[k] = a[i] < b[j] ? a[i++] : b[j++];
		}
	}
	return result;
}
```

---

# Merge "In-Place"

* ¿Por qué “In-Place”?

* Asume que los arreglos a ser “mergeados” están ambos en el mismo arreglo

* Usa un arreglo temporal

* Copia el resultado sobre el arreglo original

* Trick:
  * Copiar el segundo arreglo en forma invertida
  * Evito los tests de fin del arreglo

---

# Código "In-Place" Merge

```java
void merge(int[] array, int low, int middle, int high) {
	for (int i = low; i <= middle; i++) {
		copy(aux, i, array, i);
	}
	for (int j = middle + 1; j <= high; j++) {
		copy(aux, high + (middle + 1) - j, array, j);
	}
	for (int k = low, i = low, j = high; k <= high; k++) {
		if(greater(aux, i, j)) {
			copy(array, k, aux, j--);
		} else {
			copy(array, k, aux, i++);
		}
	}
}
```

---

# Top-Down Merge Sort

```java
void sort(int[] array, int low, int high) {
	if(low < high) {
		int mid = (low + high) / 2;
		sort(array, low, mid);
		sort(array, mid + 1, high);
		merge(array, low, mid, high);
	}
}
```

* Implementación recursiva, basada en el abstract merge
 
* Para entender como ordena, hay que analizar las llamadas recursivas
    * De ahí lo de "top-down"
 
* Se puede usar insertion (o selection) para subsets chicos
    * Para < 15 -> mejora 10-15% en el running time

---

# Top-Down

.center[![]({{site.baseurl}}/presentation/mergesort/top_down_trace.png)]

---

# Top-Down

.center[![]({{site.baseurl}}/presentation/mergesort/topdown.gif)]

---

# Bottom-Up Merge Sort

* Recorrer el arreglo

* Hacer merge de a un elemento

* Luego de a 2, 4, 8, m

* Mientras que m ≤ n∕2

---

# Código Bottom-Up

```java
void sort(int[] array, int low, int high) {
	for(int middle = 1; middle <= high - low; middle *= 2) {
		final int middleX2 = middle * 2;
		for (int low1 = low; low1 <= high - middle; low1 += middleX2) {
			final int high1 = Math.min(low1 - low + middleX2 - 1, high);
			merge(array, low1, low1 + middle - 1, high1);
		}
	}
}
```

---

# Bottom-Up

.center[![]({{site.baseurl}}/presentation/mergesort/bottomup.gif)]

---

# Merge de Listas

.center[![]({{site.baseurl}}/presentation/mergesort/mergelistas.gif)]

---

# Merge de Listas

```java
Node merge(Node a, Node b) {
	Node c, result = new Node();

	for(result = c; a != null && b != null; c = c.next) {
		if (a.value < b.value) {
			c.next = a;
			a = a.next;
		} else {
			c.next = b;
			b = b.next;
		}
	}
	c.next = a == null ? b : a;
	return result.next;
}
```

- What if we use `c = result`  at the beginning of the `for` loop?

---

# Mergesort de Listas

```java
Node sort(Node list) {
	if(list == null || list.next == null)
		return list;

	Node a = list;

	for(Node c = list.next; c != null && c.next != null; c = c.next.next) {
		list = list.next;
	}

	Node b = list.next;
	list.next = null;
	return merge(sort(a), sort(b));
}
```
