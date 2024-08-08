---
title: Práctica 2
layout: practice
permalink: /practice/2
---

# Práctica 2: Shellsort y Quicksort

* Fecha: 8 de agosto, 2024
* Fecha de Entrega: 16 de agosto, 2024

## Ejercicio 1

1. ¿Es shellsort estable? Justificar y dar un ejemplo.
2. ¿Es quicksort estable? Justificar y dar un ejemplo.
3. La siguiente secuencia muestra el proceso de particionamiento para un array ordenado al azar. Explique el gráfico y qué sucede. ¿Por qué queda así en la última escena de la secuencia?

![]({{site.baseurl}}/practice/tp2_1.jpg)

Las respuestas deben entregarse en un informe en formato PDF dentro de la carpeta `/informes` y con el nombre `tp2.pdf`.

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
