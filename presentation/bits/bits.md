class: center, middle, inverse

# Bit Manipulation

---

# Shift Happens

### shift left << y >> shift right

```java
/* 00000001 << 1 = 00000010 */
1 << 1 == 2

/* 00000001 << 3 = 00001000 */
1 << 3 == 8

/* 11111111 11111111 11111111 11110000 >> 4 = 11111111 11111111 11111111 11111111 */
0xFFFFFFF0 >> 4 == 0xFFFFFFFF

/* 00001111 11111111 11111111 11111111 >> 4 = 00000000 11111111 11111111 11111111 */
0x0FFFFFFF >> 4 == 0x00FFFFFF
```


El operador **shift right** es **signed**. Java, igual que muchos otros lenguajes, usa el bit más significativo como signo.

Un **signed shift-right** va a ***shiftear*** utilizando el valor del signo. Así, un binario que comienza en '1' agregará '1's y un binario que comienza en '0' agregará '0's.

---

# Shift Happens

### unsigned shift right >>>

Se puede utilizar un tercer operador llamado **unsigned shift right**. Este operador siempre agregará '0's sin importar el signo.

```java
/* 10000000 00000000 00000000 00000000 >>> 1 = 01000000 00000000 00000000 00000000 */
0x80000000 >>> 1 == 0x40000000

/* 10000000 00000000 00000000 00000000 >> 1 = 11000000 00000000 00000000 00000000 */
0x80000000 >> 1  == 0xC0000000
```

Usos comunes de **shifting** incluyen la creación de potencias de dos (1<<2, 1<<3, ...) y similarmente ***shifteando*** a derecha dividimos un número por dos. También se utiliza para la creación de **bitmasks** (máscaras) utilizadas para aislar o alterar una parte específica de un número binario.

```java
/* bitmask 00001000 */
int bitmask = 1 << 3;
```
---

# BitWise Operations

#### & *** and***
```java
1010 & 0101 == 0000
1100 & 0110 == 0100
```
#### ~ *** unary complement***
```java
~1111 == 0000
~0011 == 1100
```
#### | *** inclusive or***
```java
1010 | 0101 == 1111
1100 | 0110 == 1110
```
#### ^ *** exclusive or***
```java
1010 ^ 0101 == 1111
1100 ^ 0110 == 1010
```
---

# BitWise Operations

Se puede ***prender*** un bit en un número mediante una operación de **or** con otro número definido por ese bit.

```java
10000001 | 00100000 = 10100001 /* prendimos el bit 5 */
10000001 | 1 << 5 = 10100001 /* prendimos el bit 5 */
00000000 | 1 << 2 | 1 << 5 = 00100100 /* prendimos los bits 2 y 5 */
```

Se puede ***apagar*** un bit en un número mediante una operación de **and** con otro número definido únicamente por '1's excepto el bit en cuestión.

```java
01010101 & ~(1<<2) == 01010101 & 11111011 == 01010001 /* apagamos el bit 2 */
```

---

# Bit Order

Asumiendo que el bit más significativo es el de la izquierda:

```java
10010110
^      ^
|      |------- bit 0
|
|-------------- bit 7
```

Así, el valor del bit 0 es 2^0, bit 1 es 2^1, ..., bit 7 es 2^7.

---

# bitAt

Implementación de un método que devuelve el enésimo **bit** de un **String**.

```java
boolean bitAt(String s, int nth) { ... }
```

Ejemplo: el bit #11 en el String "ana" -> [01100001,01101110,01100001]
```java
|---------------------- byte 0 ("a")
|       |-------------- byte 1 ("n")
|       |       |------ byte 2 ("a")
|       |       |
011000010110111001100001
^      ^    ^
|      |    |----------- bit 11
|      |---------------- bit 0
|----------------------- bit 7
```
Para obtener el caracter correspondiente al enésimo bit: **11 / 8 == 1**

Para obtener el bit dentro del caracter correspondiente: **11 % 8 == 3**

---

# bitAt

Obtenemos el char correspondiente
```java
"ana".charAt(11 / 8) == 01101110
```

**Right shift** para dejar el enésimo como primer bit
```java
01101110 >> (11 % 8) == 00001101
```

**And** con la máscara de primer bit
```java
00001101 & 00000001 == 00000001 // Bit prendido si es distinto a cero
```

### Implementación

```java
private boolean bitAt(String s, int nth) {
    final int pos = nth / 8;
    return pos < s.length() && (s.charAt(pos) >> (nth % 8) & 1) != 0;
}
```
