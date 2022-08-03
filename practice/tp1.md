---
title: Práctica 1
layout: practice
permalink: /practice/1
---

# Práctica 1: Complejidad de Algoritmos

* Fecha: 4 de Agosto, 2022
* Fecha de Entrega: 11 de Agosto, 2022

## Ejercicio 1

Realizar un análisis de tiempo de ejecución T(n) y un análisis asintótico O(n) para los siguientes algoritmos:

```java
for (int i = 0; i < n; ++i) 
	++k


for (int i = 1; i < n; i *= 2)
	++k;


for (int i = 0; i < n; ++i)
 	for (int j = 0; j < n; ++j)        
 		++k;


for (int i = 0; i < n; ++i)
	for (int j = 0; j < i * i; ++j)
		++k;
```

* Aclaraciones:
  * Utilizar las convenciones establecidas en la práctica.
  * Lo más importante es hallar el orden correcto, no el valor preciso de la cantidad de ciclos de ejecución.

## Ejercicio 2

1. Sea la función f(n) = 3n2 – n + 4 . Utilizando la definición, muestre que f(n) = O(n2)
2. Sean f(n) = 3n2 – n + 4 y g(n) = n log n + 5 . Utilizando los teoremas relacionados con la notación O-Grande, muestre que f(n) + g(n) = O(n2).

## Ejercicio 3

Para cada una de las siguientes funciones, determine el valor que computan (expresar el valor en función de n) y encuentre una expresión ajustada del orden de complejidad para el peor caso. Justifique brevemente.

* a)

```java
static int f (int n)
{
	int sum = 0;
	for (int i = 1; i <= n; ++i)
	    sum = sum + i;
	return sum;
}
```

* b)

```java
static int g (int n)
{
	int sum = 0;
	for (int i = 1; i <= n; ++i)
	    sum = sum + i + f (i);
	return sum;
}
```

* c)

```java
static int h (int n)
{ 
	return f (n) + g (n); 
}
```
