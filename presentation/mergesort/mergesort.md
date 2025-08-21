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
			result[k] = b[j++];
		}
		else if (j >= b.length) {
			result[k] = a[i++];
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
// Merge a[lo..mid] with a[mid+1..hi].
void merge(Comparable[] a, int lo, int mid, int hi)
{ 
	int i = lo, j = mid+1;
	for (int k = lo; k <= hi; k++) // Copy a[lo..hi] to aux[lo..hi].
		aux[k] = a[k];

	for (int k = lo; k <= hi; k++) // Merge back to a[lo..hi].
		if (i > mid) a[k] = aux[j++];
		else if (j > hi ) a[k] = aux[i++];
		else if (less(aux[j], aux[i])) a[k] = aux[j++];
		else a[k] = aux[i++];

}
```
???

This method merges by first copying into the auxiliary array aux[] then merging back to a[]. In the
merge (the second for loop), there are four conditions: left half exhausted (take from the right), right
half exhausted (take from the left), current key on right less than current key on left (take from the
right), and current key on right greater than or equal to current key on left (take from the left).

---

# Example

.center[![]({{site.baseurl}}/presentation/mergesort/example.png)]

---

# Top-Down Merge Sort

```java
public class Merge
{
	private static Comparable[] aux; // auxiliary array for merges

	public static void sort(Comparable[] a)
	{
		aux = new Comparable[a.length]; // Allocate space just once.

		sort(a, 0, a.length - 1); 
	}
	
	private static void sort(Comparable[] a, int lo, int hi) // Sort a[lo..hi].
	{ 
		if (hi <= lo) return;
		int mid = lo + (hi - lo)/2;
		sort(a, lo, mid); // Sort left half.
		sort(a, mid+1, hi); // Sort right half.
		merge(a, lo, mid, hi); // Merge results.
	}
}
```

---

# Top-Down Merge Sort

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
public class Merge 
{
	private static Comparable[] aux; // auxiliary array for merges
	
	void sort(int[] array, int low, int high) {
		aux = new Comparable[N];

		for(int middle = 1; middle <= high - low; middle *= 2) {
			final int middleX2 = middle * 2;
			for (int low1 = low; low1 <= high - middle; low1 += middleX2) {
				final int high1 = Math.min(low1 - low + middleX2 - 1, high);
				merge(array, low1, low1 + middle - 1, high1);
			}
		}
	}
}
```

---

# Example

.center[![]({{site.baseurl}}/presentation/mergesort/buexample.png)]

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
