class: center, middle, inverse

# String Searching

---

# String Searching

* Texto con N caracteres // Pattern con M caracteres

--

* Operaciones:
    * **Existencia**: ¿existe el pattern en el texto?
    * **Enumeración**: ¿cuántas ocurrencias?
    * **Match**: retornar el índice de cualquiera de las ocurrencias
    * **All matches**: retornar el índice de todas las ocurrencias

--

* Asumimos que N >> M >> número de ocurrencias
    * Ej: N = 100.000, M = 100, 5 ocurrencias.

---

# String Searching

## Ejemplo

--

Encontrar **'avoctdfytvv'** en

kvjlixapejrbxeenpphkhthbkwyrwamnugzhppfxiyjyanhapfwbghx mshrlyujfjhrsovkvveylnbxnawavgizyvmfohigeabgksfnbkmffxj
ffqbualeytqrphyrbjqdjqavctgxjifqgfgydhoiwhrvwqbxgrixydz
bpajnhopvlamhhf**avoctdfytvv**ggikngkwzixgjtlxkozjlefilbrbo
gnbzsudssvqymnapbpqvlubdoyxkkwhcoudvtkmikansgsutdjythzl
apawlvliygjkmxorzeoafeoffbfxuhkzukeftnrfmocylculksedgrd
ivayjpgkrtedehwhrvvbbltdkctq

---

# Aplicaciones

* Parsers
* Spam filters
* Screen scrapers
* Word processors
* Web search engines
* Natural language processing
* Computational molecular biology
* Image processing

---

# Aplicaciones

## Ejemplo: Screen Scraping

--

Buscar patrón: “Last Trade” + “< b >” + “< / b >”

.center[![]({{site.baseurl}}/presentation/string_search/example.png)]

---

# Fuerza Bruta

--

.center[![]({{site.baseurl}}/presentation/string_search/fuerza_bruta.png)]

---

# Fuerza Bruta

Tiempo de ejecución: depende del largo del texto (N) y del pattern (M)

--

.center[![]({{site.baseurl}}/presentation/string_search/bf_worst_case.png)]

---

# Fuerza Bruta

.center[![]({{site.baseurl}}/presentation/string_search/bf_avg_case.png)]

---

# Fuerza Bruta

## Análisis

* Comparaciones: **O(N\*M)** (¡demasiado si M y N son grandes!)

--

* Si el texto lo leo de un archivo preciso mantener un buffer de M caracteres para retroceder en el texto

---

# Rabin Karp

* Idea: **usar hashing**
    * Calcular la función de hash para cada posición del texto
    * Compararlo con el hash del pattern
    * No necesito guardarlo (no uso una hashtable)

--

* Ejemplo: buscar 59265 en 31415926535897932384626433
    * Hash del pattern 59265 = 95 (mod 97)
    * Hashes del texto: 31415926535897932384626433
        * 31415 = 84 (mod 97)
        * 14159 = 94 (mod 97)
        * 41592 = 76 (mod 97)
        * 15926 = 18 (mod 97)
        * 59265 = 95 (mod 97)

--

* Problema: hash trabaja sobre M caracteres
* Orden: N\*M

---

# Rabin Karp

.center[![]({{site.baseurl}}/presentation/string_search/rk.png)]

---

# Rabin Karp

* Idea: usar el hash de la posición previa para calcular el de la nueva posición

--

    * 14159 = ( 31415 - 30000) \* 10 + 9
    * 14159 mod 97 = (31415 mod 97 - 30000 mod 97) \*10 + 9 (mod 97)
    * 14159 mod 97 = ( 84 - 3 \* (10000 mod 97)) \* 10 + 9 (mod 97)

---

# Rabin Karp

* Ejemplo: buscar 59265 en 31415926535897932384626433
    * Hash del pattern 59265 = 95 (mod 97)
    * Hashes del texto: 31415926535897932384626433
        * Precomputo 10000 mod 97 = 9
        * 31415 mod 97 = 84
        * 14159 mod 97 = (84 - 3\*9)\*10 + 9 (mod 97) = 94
        * 41592 mod 97 = (94 - 1\*9)\*10 + 2 (mod 97) = 76
        * 15926 mod 97 = (76 - 4\*9)\*10 + 6 (mod 97) = 18
        * 59265 mod 97 = (18 - 1\*9)\*10 + 5 (mod 97) = 95

--

* Un problema: ¡todavía preciso hacer una comparación completa por los sinónimos!
    * Solución: reducir probabilidad con un module >>

---

# Rabin Karp

.center[![]({{site.baseurl}}/presentation/string_search/rk_impl.png)]


---

# Rabin Karp

## Notas de implementación

* Se utiliza un radix (base) de 256
    * Se considera un alfabeto de 256 caracteres o símbolos

* Se utiliza un módulo primo grande

* Los valores se calculan aplicando el método de Horner

* En cada iteración se aplica el módulo para evitar overflow

* Se utiliza RADIX\*MODULE para evitar valores negativos


.right[
**Radix**: base de la representación numérica del alfabeto dado
]

.right[
**Module**: cociente sobre el cual se calcula el resto de la división entera
]

---

layout: true

# Boyer Moore

---

* Idea (algoritmo sublineal):
    * Me muevo en el texto de izquierda a derecha
    * Me muevo en el pattern de derecha a izquierda

---

* Ejemplo: buscar 000000000 en 100111010010100010100111000111
    * ¡Solo 6 comparaciones!
    * Tiempo de búsqueda proporcional a N/M
    * Tiempo de búsqueda ¡disminuye cuando la longitud del pattern crece!

---

## Skip table

Ejemplo: **people**

.center[![]({{site.baseurl}}/presentation/string_search/bm_skip_table.png)]

---

layout: false

.center[![]({{site.baseurl}}/presentation/string_search/bm.png)]

---

layout: true

# Boyer Moore

---

## Heurísticas

* Bad Character
* Good Suffix

---

## Bad Character Heuristic

### Case 1 – Mismatch become match

.center[![]({{site.baseurl}}/presentation/string_search/bad-character-case-1.png)]

---

## Bad Character Heuristic

### Case 2 – Pattern move past the mismatch character

.center[![]({{site.baseurl}}/presentation/string_search/bad-character-case-2.png)]

---

## Good Suffix Heuristic

### Case 1: Another occurrence of t in P matched with t in T

.center[![]({{site.baseurl}}/presentation/string_search/good-suffix-case-1.png)]

---

## Good Suffix Heuristic

### Case 2: A prefix of P, which matches with suffix of t in T

.center[![]({{site.baseurl}}/presentation/string_search/good-suffix-case-2.png)]

---

## Good Suffix Heuristic

### Case 3: No other occurrence and no prefix

.center[![]({{site.baseurl}}/presentation/string_search/good-suffix-case-3.png)]

