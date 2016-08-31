---
title: Práctica 4
layout: practice
permalink: /practice/4
---

# Práctica 4: Quicksort y Mergesort

* Fecha: 31 de Agosto, 2016
* Fecha de Entrega: 7 de Septiembre, 2016

## Ejercicio 1

1. Implementar quicksort NO recursivo básico.
2. Implementar un quicksort recursivo con cutoff por insertion sort para subfiles de tamaño menor a M.
3. Determinar, empíricamente, el valor de M para el cual la implementación se ejecuta en menor tiempo, ordenando files de N enteros random, para N = 10<sup>3</sup>, 10<sup>4</sup>, 10<sup>5</sup>, y 10<sup>6</sup>. Explicar el procedimiento mostrando un detalle de los resultados obtenidos.

## Ejercicio 2

1. Implementar quicksort no recursivo, median-of-three partitioning y three-way partition.
2. Realizar un análisis empírico comparativo de performance entre las distintas variantes de quicksort. 

Analizar las 5 variantes implementadas. Para la versión híbrida (cutoff), utilizar el M óptimo hallado en 1.c)
Tabular el tiempo de ejecución y cantidad de comparaciones necesarios para ordenar las N = 12500, 25000, 50000, 100000.

## Ejercicio 3

1. Implementar mergesort top-down y bottom-up.
2. Realizar un análisis comparativo tabulando el tiempo de ejecución para ordenar files de N enteros random, para N = 100, 500, 1000, 2500, 5000.

NOTAS:
Si durante las pruebas se llega a un java.lang.StackOverflowError, utilizar el parámetro de la JVM –Xss para aumentar el thread stack size y así poder finalizar los experimentos.
