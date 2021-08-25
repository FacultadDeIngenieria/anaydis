---
title: Práctica 3
layout: practice
permalink: /practice/3
---

# Práctica 3: Shellsort y Quicksort

* Fecha: 26 de agosto, 2021
* Fecha de Entrega: 2 de septiembre, 2021

## Ejercicio 1

1. ¿Es shellsort estable? Justificar y dar un ejemplo.
2. ¿Es quicksort estable? Justificar y dar un ejemplo.
3. La siguiente secuencia muestra el proceso de particionamiento para un array ordenado al azar. Explique el gráfico y qué sucede. ¿Por qué queda así en la última escena de la secuencia?

![]({{site.baseurl}}/practice/tp3_1.jpg)

## Ejercicio 2

1. Agregar a nuestros Sorters, la clase QuickSorter implementando el quicksort.
2. Agregar a nuestros Sorters, la clase HSorter implementando h-sort, debería quedar así:

```java
public class HSorter extends AbstractSorter
{
	public HSorter() {
		super(SorterType.H);
	}
	
	public <T> void sort(Comparator<T> comparator, List<T> list){ 
		sort(comparator, list, 1);
	} 

	/**
	* H-Sort list. Basically a BubbleSort in sets of elements separated by h
	*/ 
	public <T> void sort(Comparator<T> comparator, List<T> list, int h) 
	{ 
		// Your  implementation!
	}
}
```

3. Agregar a nuestros Sorters, la clase ShellSorter implementando el shellsort (recordar que ya tenemos implementado el HSorter del punto [b])

## Ejercicio 3

Desarrollar un programa que sirva para comparar secuencias de incrementos de h para Shellsort. 
Realizar un informe similar al de la Práctica 2, sobre la performance de shellsort para distintas secuencias de incrementos.
Los gráficos deben mostrar cantidad de comparaciones y running-time en función de N. 
Utilizar sets de datos ordenados al azar para cada tamaño N.
Realizar las pruebas para N = {100, 1000, 10000}.
Las secuencias a utilizar para las pruebas son:

1 8 23 77 281 1073 4193 16577 ....
1 4 13 40 121 364 1093 3280 9841 ...

Notemos que es necesario que el código del shellsort no esté atado a una secuencia específica. 


Un ejemplo que utiliza una secuencia dada es:

```java
void shellsort(Item a[], int l, int r)
{
	int i, j;
	int incs[16] = { 1391376, 463792, 198768, 86961, 33936, 13776, 4592, 1968, 861, 336, 112, 48, 21, 7, 3, 1 };

	for ( k = 0; k < 16; k++) {
		int h = incs[k];
		for (i = l+h; i <= r; i++) {
		 	Item v = a[i];
		 	j = i;
		 	
		 	while (j >= h && less(v, a[j-h])) { 
		 		a[j] = a[j-h]; j -= h; 
		 	}
				
			a[j] = v;
		}
	}
}
```