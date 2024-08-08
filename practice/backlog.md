---
title: Backlog practice
layout: practice
permalink: /practice/backlog
---

## H-Sort increment sequence analysis

Desarrollar un programa que sirva para comparar secuencias de incrementos de h para Shellsort.
Realizar un informe similar al de la Práctica 1, sobre la performance de shellsort para distintas secuencias de incrementos.
Los gráficos deben mostrar cantidad de comparaciones y running-time en función de N.
Utilizar sets de datos ordenados al azar para cada tamaño N.
Realizar las pruebas para N = {100, 1000, 10000}.
Las secuencias a utilizar para las pruebas son:

1 8 23 77 281 1073 4193 16577 ....
1 4 13 40 121 364 1093 3280 9841 ...

Notemos que es necesario que el código del shellsort no esté atado a una secuencia específica.
El informe tiene que entregarse en formato PDF dentro de la carpeta `/informes` y con el nombre `tp2.pdf`.


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
