---
title: Práctica 1
layout: practice
permalink: /practice/1
---

# Práctica 1: Algoritmos de Sorting Elementales

* Fecha: 3 de agosto, 2023
* Fecha de Entrega: 17 de agosto, 2023

## Ejercicio 1

Desarrollar los algoritmos BubbleSorter, SelectionSorter e InsertionSorter implementando la interfaz Sorter que se encuentra en las librerías de la materia.

## Ejercicio 2

Ejecutar pruebas sobre los algoritmos implementados para los casos mejor, peor y promedio. 
Graficar los resultados de cada algoritmo para cada caso comparándolos. 
Comparar los resultados con los órdenes y tiempos de ejecución calculados teóricamente.
Presentar un informe con los resultados, gráficos y el análisis de los mismos.
Para cada escenario y algoritmo, obtener data points para N = {10, 50, 500, 1000, 5000}.

## Ejercicio 3

Desarrollar un programa que utilice alguno de los algoritmos implementados para ordenar una lista de objetos del tipo:

```java
public class FullName {
	private String firstname;
	private String lastname;
	public String getFirstname() {
		return firstname;
	}
	public String getLastname() {
		return lastname;
	}
}
```

La lista debe ser ordenada dos veces, la primera por apellido y la segunda por nombre. 
Ejemplo:

Lista sin ordenar:

* Alonso, Paula
* Diaz, Diego
* Alonso, Diego

Lista ordenanda:

* Alonso, Diego
* Alonso, Paula
* Diaz, Diego

Sólo un algoritmo debe utilizarse. Elegir uno y justificar por qué se eligió ese.

## NOTAS

Para el Ejercicio 2, deben implementarse las interfaces del jar, las cuales le servirán para desarrollar el programa necesario para generar los resultados del benchmark.
Las interfaces a implementar son:
SorterListener / ObservableSorter
DataSetGenerator
SorterProvider

IMPORTANTE:
No modificar los sources del jar entregado.
No extraer los sources para desarrollar el programa, debe utilizar el jar como librería.
