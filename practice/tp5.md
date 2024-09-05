---
title: Práctica 5
layout: practice
permalink: /practice/5
---

# Práctica 5: Priority Queues

* Fecha: 5 de septiembre 2024
* Fecha de entrega: 20 de septiembre 2024

## Ejercicio 1
Implementar la clase anaydis.search.UnorderedArrayPriorityQueue que implemente la interfaz anaydis.search.PriorityQueue
* Debe utilizar un array nativo para su implementación
* No debe ordenar internamente las claves en el array
* Debe ser Iterable
* Debe tener un constructor que reciba un Comparator<K> para poder comparar las keys
* Debe detectar y soportar underflow (arrojar una NoSuchElementException)

## Ejercicio 2

Implementar la clase anaydis.search.OrderedArrayPriorityQueue que implemente la interfaz anaydis.search.PriorityQueue
* Debe utilizar un array nativo para su implementación
* Debe mantener todas las claves ordenadas en el array interno
* Debe ser Iterable
* Debe tener un constructor que reciba un Comparator<K> para poder comparar las keys
* Debe detectar y soportar underflow (arrojar una NoSuchElementException)

## Ejercicio 3

Implementar la clase anaydis.search.HeapPriorityQueue que implemente la interfaz anaydis.search.PriorityQueue
* Debe utilizar un array nativo para su implementación
* Debe utilizar un Heap representado en el array interno
* Debe implementar las operaciones de swim y sink cuando corresponda
* Debe ser Iterable
* Debe tener un constructor que reciba un Comparator<K> para poder comparar las keys
* Debe detectar y soportar underflow (arrojar una NoSuchElementException)

## Ejercicio 4

Elaborar un informe comparando empíricamente la performance de las operaciones *pop()* e *insert(K key)* en las tres implementaciones de PriorityQueue realizadas. Luego, comparar los resultados con los órdenes teóricos vistos en clase. Utilizar queues de tamaño N = {100, 1000, 5000, 10000}.
