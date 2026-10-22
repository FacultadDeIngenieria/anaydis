class: center, middle, inverse

# Criptografía

---

# Criptografía

* Alice quiere mandarle un mensaje a Bob por un canal inseguro.
* Eve escucha todo lo que pasa por el canal.

--

```
               clave K                          clave K
                  │                                │
   m ──► [ Cifrar E(K, m) ] ──► c ─────────► [ Descifrar D(K, c) ] ──► m
                                    │
                                   Eve
```

--

* **m**: texto plano (plaintext). **c**: texto cifrado (ciphertext).
* **E** y **D** son públicos; lo único secreto es la **clave K**.

???

Esta es la idea que recorre toda la clase: E y D son algoritmos conocidos, y toda la seguridad está en la clave.

---

# ¿Qué buscamos?

* **Confidencialidad**: sólo quien tiene la clave puede leer el mensaje.
* **Integridad**: si el mensaje se modifica, se detecta.

--

* **Autenticación**: verificar quién mandó el mensaje.
* **No repudio**: quien lo mandó no puede negar haberlo mandado.

--

* La seguridad de la información suele resumirse en la tríada **C.I.A.**: **C**onfidencialidad, **I**ntegridad y **A**vailability (disponibilidad).
  * La autenticación y el no repudio los da la criptografía, pero **no** son parte de la tríada.
  * La criptografía **no** ayuda con la disponibilidad: si se pierde la clave, se pierden los datos.

???

Ejemplo de disponibilidad: un ransomware cifra los datos de una empresa. La confidencialidad y la integridad del cifrado están intactas, pero la empresa ya no puede usar sus datos.

---

# Identificación, autenticación, autorización, accountability

| Concepto | Pregunta | Ejemplos |
|---|---|---|
| **Identificación** | ¿Quién decís que sos? | Usuario, email |
| **Autenticación** | ¿Lo podés probar? | Contraseña, código de un solo uso, huella, certificado |
| **Autorización** | ¿Qué podés hacer? | Permisos, roles |
| **Accountability** | ¿Qué hiciste? | Logs que se pueden atribuir a un usuario |

--

* Cada paso depende del anterior: no se puede autorizar a alguien que no se autenticó.
* Factores de autenticación: algo que **sabés**, algo que **tenés**, algo que **sos**.

???

- Ejemplo con GitHub:
  - Identificación: el usuario de GitHub.
  - Autenticación: la contraseña + el segundo factor (o la clave SSH).
  - Autorización: tener permiso de push sobre el repositorio del TP.
  - Accountability: el historial muestra quién hizo cada push.
- Identificarse no es autenticarse: decir "soy pcolunga" no prueba nada.
- El autor de un commit en git log se puede falsificar (es sólo un campo de texto). Por eso existen los commits firmados (GPG o SSH): la firma digital da no repudio. Lo vemos al final.

---

# Principio de Kerckhoffs (1883)

* Un sistema criptográfico tiene que ser seguro **aunque el enemigo conozca el algoritmo**.
  * Todo el secreto está en la **clave**.

--

* ¿Por qué?
  * Un algoritmo se filtra, se copia o se descompila. Una clave se cambia.
  * Un algoritmo público puede ser analizado por todos: AES, RSA y SHA-256 son públicos.

--

* Entonces la seguridad depende de:
  * **Keyspace**: la cantidad de claves posibles. Cuanto más grande, mejor.
  * **Work factor**: el costo de romper el sistema sin la clave.

---

# Breve historia

* **~ 50 AC**: Julio César cifra mensajes desplazando las letras del alfabeto.
* **1553**: Bellaso describe el cifrado que hoy se conoce como de Vigenère. Se lo consideró "indescifrable" durante tres siglos.
* **1917**: Vernam cifra con XOR. Base del one-time pad.

--

* **1939-1945**: Enigma. Turing y el equipo de Bletchley Park construyen la *Bombe* para romperla.
* **1976**: Diffie y Hellman publican *New Directions in Cryptography*: criptografía de clave pública.
* **1977**: DES, primer estándar público de cifrado. RSA (Rivest, Shamir, Adleman).
* **2001**: AES reemplaza a DES.

---

class: center, middle, inverse

# Cifrados clásicos

---

# Cifrado César

* **Sustitución**: cada letra se reemplaza por la que está K posiciones más adelante.

```
Plano:    A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
Cifrado:  D E F G H I J K L M N O P Q R S T U V W X Y Z A B C     (K = 3)
```

--

```java
static String caesar(String text, int shift) {
    StringBuilder out = new StringBuilder();
    for (char c : text.toCharArray()) {
        if (Character.isUpperCase(c))
            out.append((char) ('A' + (c - 'A' + shift + 26) % 26));
        else
            out.append(c);
    }
    return out.toString();
}
```

* `caesar("ATAQUEN AL AMANECER", 3)` → `"DWDTXHQ DO DPDQHFHU"`
* Para descifrar: `caesar(c, -3)`.

???

ROT13 es César con K = 13. Como 13 + 13 = 26, aplicarlo dos veces devuelve el original: cifrar y descifrar son la misma operación.

---

# Cifrado César: análisis

* ¿Cuántas claves hay?

--

  * **25**. Se prueban todas a mano en un minuto: **fuerza bruta**.

--

* ¿Y si en lugar de desplazar usamos una **permutación cualquiera** del alfabeto?

```
Plano:    A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
Cifrado:  Q W E R T Y U I O P A S D F G H J K L Z X C V B N M
```

--

  * Keyspace: 26! ≈ 4 · 10²⁶ claves. La fuerza bruta es imposible...

--

  * ...pero se rompe con **análisis de frecuencias**: en castellano la E y la A aparecen más de 12% cada una. La letra más frecuente del cifrado es casi seguro una E o una A.

???

Conexión con compresión: el análisis de frecuencias funciona por la redundancia del lenguaje, lo mismo que aprovecha un compresor. Un texto con poca redundancia (por ejemplo, comprimido) es mucho más difícil de atacar así.

Un keyspace grande es necesario pero no suficiente.

---

# Cifrado de Vigenère

* **Polialfabético**: la clave es una palabra que se repite a lo largo del texto.
* Cada letra se desplaza según la letra de la clave que le toca (A = 0, B = 1, ...).

```
Plano:    COMPUTING GIVES INSIGHT
Clave:    LUCKYLUCK YLUCK YLUCKYL
Cifrado:  NIOZSECPQ ETPGC GYMKQFE
```

--

* C + L = N, O + U = I, M + C = O, ...
* La misma letra se cifra distinto según la posición: las cuatro I del texto plano quedan como C, T, G y K.
* El análisis de frecuencias directo ya no funciona.

???

Repito la clave tantas veces como para completar el largo del texto. Sumo la letra del texto plano a la letra que le corresponde de la clave.

---

# Cifrado de Vigenère

```java
static String vigenere(String text, String key, int sign) {
    StringBuilder out = new StringBuilder();
    int j = 0;
    for (char c : text.toCharArray()) {
        if (!Character.isUpperCase(c)) { out.append(c); continue; }
        int k = key.charAt(j++ % key.length()) - 'A';
        out.append((char) ('A' + (c - 'A' + sign * k + 26) % 26));
    }
    return out.toString();
}
```

* Cifrar: `vigenere(m, "LUCKY", 1)`. Descifrar: `vigenere(c, "LUCKY", -1)`.

--

* ¿Cómo se rompe? (Kasiski, 1863)
  * Se averigua el **largo de la clave** buscando repeticiones en el texto cifrado.
  * Con largo k, el texto son **k cifrados César** intercalados: cada uno cae con análisis de frecuencias.

---

# Cifrado por transposición

* No cambia las letras: cambia su **orden**. La clave es una permutación.
  * Clave (3, 4, 5, 2, 1): "WORLD" → "RLDOW"

--

* Variante con matriz: se escribe el texto por filas y se lee por columnas.

```
ANALIS        AIIDOS
IS_Y_D        NSSER_
ISENO_        A_E_I2
DE_ALG        LYNAT0
ORITMO        I_OLM2
S_2026        SD_GO6
```

* "ANALISIS Y DISENO DE ALGORITMOS 2026" → "AIIDOSNSSER_A_E_I2LYNAT0I_OLM2SD_GO6"

--

* Las frecuencias de las letras **no cambian**: un análisis de frecuencias revela que es una transposición.
* Por eso los cifrados reales combinan **sustitución** y **transposición** (lo vemos en cifrados de bloque).

???

Para descifrar se hace lo inverso: se escribe el cifrado por columnas y se lee por filas. La clave es el tamaño de la matriz (y, en la variante más común, el orden en que se leen las columnas).

---

# Ocultar no es cifrar

* **Esteganografía**: esconder la **existencia** del mensaje.
  * Tinta invisible, microfilm, bits menos significativos de una imagen o un audio.
  * Marcas de agua digitales.

--

* **Mensaje dentro de un mensaje**: la clave es "tomar cada sexta palabra".

"I have been trying to **buy** you a nice gift like **gold** or an antique but prices **now** are really high."

--

* No usan algoritmos de cifrado ni claves: si se descubre el método, se lee todo.
* Se pueden combinar: cifrar el mensaje y después esconderlo.

---

class: center, middle, inverse

# Cifrado con bits

---

# XOR

* La operación clave de la criptografía moderna (ver la clase de Bits).

| a | b | a ^ b |
|:-:|:-:|:-:|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

--

* Propiedad fundamental: **(m ^ k) ^ k = m**. La misma operación cifra y descifra.

```
Mensaje      01011010   'Z'
Clave        01100011   'c'
             --------   XOR
Cifrado      00111001   '9'
Clave        01100011   'c'
             --------   XOR
Mensaje      01011010   'Z'
```

---

# One-time pad

* Se cifra con XOR usando una clave **aleatoria**, **tan larga como el mensaje** y que se usa **una sola vez**.

```java
static byte[] xor(byte[] data, byte[] key) {
    byte[] out = new byte[data.length];
    for (int i = 0; i < data.length; i++)
        out[i] = (byte) (data[i] ^ key[i]);
    return out;
}
```

--

* **Secreto perfecto** (Shannon, 1949): el cifrado no da **ninguna** información sobre el mensaje.
  * Para cualquier mensaje del mismo largo existe una clave que lo produce. Con fuerza bruta aparecen todos los mensajes posibles y no hay forma de saber cuál era.

--

* ¿El problema? Hay que compartir una clave del tamaño de todo lo que se va a mandar.

---

# One-time pad: ¿y si reuso la clave?

* c₁ = m₁ ^ k, c₂ = m₂ ^ k

--

* c₁ ^ c₂ = m₁ ^ k ^ m₂ ^ k = **m₁ ^ m₂**

--

* La clave desaparece. Lo que queda es la mezcla de dos textos con mucha redundancia, que se separa con análisis estadístico.

???

Caso real: el proyecto Venona. Los soviéticos reutilizaron páginas de sus one-time pads en los años 40 y el contraespionaje estadounidense pudo descifrar parte de sus mensajes.

---

# Stream ciphers

* Idea: generar un **keystream** largo a partir de una clave corta (**seed**) y cifrar con XOR.

```
 seed ──► [ Generador ] ──► keystream   1101000111010001
                                              │
 mensaje  1111001111110011 ──────────────► ( XOR ) ──► 0010001000100010
```

--

* Emisor y receptor comparten la seed: generan el mismo keystream.
* Ya no hay secreto perfecto: la seguridad depende de que el keystream sea **impredecible**.

???

La gracia del algoritmo es cómo a partir de una clave transmisible se genera un keystream lo suficientemente complejo y aleatorio.

---

# ¿Cualquier generador sirve?

* `java.util.Random` es un **generador lineal congruencial**:

```
X(n+1) = (a · X(n) + b) mod m           // a = 0x5DEECE66D, b = 11, m = 2⁴⁸
```

--

* Con la misma seed genera siempre la misma secuencia: `new Random(42)` es reproducible.
* Viendo unas pocas salidas se puede reconstruir el estado y **predecir todas las siguientes**.

--

* Para criptografía: `java.security.SecureRandom`.
  * Impredecible aunque se conozcan salidas anteriores.

--

* RC4 fue el stream cipher más usado (WEP, TLS). Hoy está roto: prohibido en TLS desde 2015.

???

**Por qué Random no sirve.** El estado interno es de 48 bits y `nextInt()` devuelve los 32 bits altos. Si veo una salida, me faltan 16 bits: pruebo las 2¹⁶ = 65.536 posibilidades y me quedo con la que genera la segunda salida que también vi. Con dos `nextInt()` consecutivos reconstruyo el estado en milisegundos y a partir de ahí conozco todo el keystream. Es un buen generador para simulaciones o tests (rápido y reproducible), pero no para seguridad.

**SecureRandom** se alimenta de entropía que junta el sistema operativo (en Linux y macOS, `/dev/urandom`) y está diseñado para que conocer salidas pasadas no ayude a predecir las siguientes. Es más lento, pero es el que hay que usar para generar claves y cualquier valor que tenga que ser impredecible.

**RC4** era muy simple y rápido (un swap en un arreglo de 256 bytes por cada byte generado), pero los primeros bytes del keystream tienen sesgos estadísticos. WEP (el Wi-Fi viejo) se rompió en 2001 aprovechando esos sesgos; en TLS los sesgos alcanzaron para recuperar cookies. RFC 7465 (2015) lo prohíbe en TLS.

**¿Entonces los stream ciphers son malos?** No. Lo que estaba mal eran RC4 y los generadores débiles, no la idea. Hoy se usa mucho **ChaCha20** (Bernstein, 2008): es una de las dos opciones de TLS 1.3, y lo usan WireGuard, SSH y Android en celulares sin instrucciones de hardware para AES, donde es más rápido que AES por software.

Tienen dos cuidados, que valen para cualquier esquema basado en XOR:

* Nunca generar dos veces el mismo keystream (misma seed): sería reusar el one-time pad. Por eso, en la práctica, a la clave se le agrega un valor distinto en cada mensaje.
* Son maleables: si alguien cambia un bit del cifrado, cambia el mismo bit del texto plano sin que nadie lo note. Por eso siempre van acompañados de algo que detecte modificaciones (ChaCha20-Poly1305; con AES lo vemos en el modo GCM).

**¿Por qué pasamos a cifrado de bloque?** Por varias razones:

* Fueron los estándares: DES desde 1977 y AES desde 2001. Están muy estudiados y los procesadores modernos traen instrucciones para AES (AES-NI).
* Un cifrado de bloque es una pieza más versátil. Con un modo de operación se cifran mensajes de cualquier largo y hasta se arma un stream cipher: el modo CTR cifra un contador 0, 1, 2… y usa el resultado como keystream. GCM, que vemos más adelante, es exactamente CTR más un tag.

La distinción entre bloque y stream es más de construcción que de calidad: en la práctica, AES-GCM y ChaCha20 se usan para lo mismo.

---

class: center, middle, inverse

# Cifrado de bloque

---

# Cifrado de bloque

* Cifra **bloques de tamaño fijo**: n bits de texto plano → n bits de texto cifrado.
  * DES: bloques de 64 bits. AES: bloques de 128 bits.

--

* Shannon (1949): un buen cifrado necesita
  * **Confusión**: la relación entre la clave y el texto cifrado tiene que ser lo más compleja posible. Se logra con **sustitución** (S-boxes: tablas de lookup).
  * **Difusión**: cambiar un bit del texto plano tiene que cambiar, en promedio, la mitad de los bits del texto cifrado. Se logra con **permutaciones**.

--

* Una sola ronda es débil. Se aplican **muchas rondas**, cada una con una subclave derivada de la clave.

---

# Red de Feistel

* Se parte el bloque en dos mitades **L** y **R**. En cada ronda, con una función **F** y la subclave Kᵢ:

```
L(i+1) = R(i)
R(i+1) = L(i) ^ F(R(i), K(i))
```

--

* Truco: **F no necesita ser invertible**. Para descifrar se aplica la misma red con las subclaves en orden inverso.

```java
static int feistel(int block, int[] keys) {   // bloque de 32 bits
    int l = block >>> 16, r = block & 0xFFFF;
    for (int k : keys) {
        int next = l ^ f(r, k);
        l = r;
        r = next;
    }
    return (r << 16) | l;                      // swap final
}
```

* `feistel(0xCAFEBABE, keys)` → `0xE9844A1E` → `feistel(0xE9844A1E, reversed)` → `0xCAFEBABE`

???

En el ejemplo, f(half, k) = ((half · 0x9E37 + k) ^ (half >>> 7)) & 0xFFFF: una función cualquiera, no invertible. Las subclaves son {0x1234, 0xBEEF, 0x0F0F, 0x7777}. Probado con 100.000 bloques al azar.

¿Por qué funciona? En cada ronda, conociendo L(i+1) = R(i) se puede recalcular F(R(i), K(i)) y deshacer el XOR. Nunca hace falta invertir F.

---

# DES y AES

* **DES** (1977): red de Feistel, 16 rondas, clave de **56 bits**.
  * 2⁵⁶ ≈ 7 · 10¹⁶ claves. En 1998 la EFF construyó una máquina que la rompía por fuerza bruta en 56 horas.
  * **3DES**: DES tres veces con dos o tres claves. Parche mientras llegaba un reemplazo.

--

* **AES** (2001): concurso público del NIST, gana **Rijndael** (Daemen y Rijmen).
  * Bloques de 128 bits. Claves de 128, 192 o 256 bits (10, 12 o 14 rondas).
  * **No** es Feistel: es una red de sustitución-permutación.
  * Cada ronda: **SubBytes** (confusión), **ShiftRows** y **MixColumns** (difusión), **AddRoundKey** (mezcla la subclave).

--

* 2¹²⁸ ≈ 3,4 · 10³⁸ claves. Probando 10¹² claves por segundo: 10¹⁹ años.

???

Animación de AES: https://web.archive.org/web/20251012175018/http://formaestudio.com/rijndaelinspector/archivos/rijndaelanimation.html

Video: https://www.youtube.com/watch?v=gP4PqVGudtg

Para comparar: la edad del universo es de unos 1,4 · 10¹⁰ años.

---

# Modos de operación

* ¿Cómo se cifra un mensaje más largo que un bloque?

--

* **ECB**: cada bloque por separado.
  * Bloques iguales → cifrados iguales. En una imagen cifrada así se sigue viendo el dibujo.

--

* **CBC**: antes de cifrar cada bloque se le hace XOR con el cifrado del bloque anterior.
  * El primer bloque se combina con un **vector de inicialización (IV)** aleatorio.
  * El mismo mensaje cifrado dos veces da resultados distintos.

--

* **GCM**: además de cifrar, agrega un **tag** que detecta modificaciones (confidencialidad + integridad).

???

El IV no es secreto: se manda junto con el mensaje. Lo importante es que no se repita con la misma clave.

El ejemplo clásico de ECB es el pingüino de Linux (Tux) cifrado con ECB: se sigue reconociendo.

---

# AES en Java

```java
KeyGenerator kg = KeyGenerator.getInstance("AES");
kg.init(256);
SecretKey key = kg.generateKey();

byte[] iv = new byte[12];
new SecureRandom().nextBytes(iv);           // IV nuevo para cada mensaje

Cipher cipher = Cipher.getInstance("AES/GCM/NoPadding");
cipher.init(Cipher.ENCRYPT_MODE, key, new GCMParameterSpec(128, iv));
byte[] encrypted = cipher.doFinal("Hola mundo".getBytes(UTF_8));

cipher.init(Cipher.DECRYPT_MODE, key, new GCMParameterSpec(128, iv));
String back = new String(cipher.doFinal(encrypted), UTF_8);
```

* "Hola mundo" (10 bytes) → 26 bytes: 10 de cifrado + 16 de tag.
* Si se modifica un solo bit de `encrypted`, `doFinal` tira `AEADBadTagException`.

???

UTF_8 es StandardCharsets.UTF_8 (import static).

---

class: center, middle, inverse

# Funciones de hash

---

# Funciones de hash criptográficas

* Toman una entrada de **cualquier tamaño** y devuelven un valor de **tamaño fijo**.

```
SHA-256("abc") = ba7816bf8f01cfea414140de5dae2223b00361a396177a9cb410ff61f20015ad
SHA-256("abd") = a52d159f262b2c6ddb724a61840befc36eb30c88877a4030b65cbe86298449c9
```

--

* Propiedades:
  * **Resistencia a preimagen** (one-way): dado h, no se puede encontrar m con hash(m) = h.
  * **Resistencia a segunda preimagen**: dado m, no se puede encontrar otro m' con el mismo hash.
  * **Resistencia a colisiones**: no se pueden encontrar m ≠ m' con el mismo hash.

--

* Cambia una letra y cambia todo el hash: **difusión**.

???

Que no se pueda volver al original y que no produzca "sinónimos". Las colisiones existen (hay infinitas entradas y finitos hashes); lo que se pide es que sea imposible encontrarlas en la práctica.

---

# Hash de tablas vs hash criptográfico

* En **Rabin-Karp** usamos `hash = x mod 97`.
  * Rápido y bien distribuido: alcanza para buscar strings.
  * Encontrar colisiones es trivial: x y x + 97 tienen el mismo hash.

--

* Un hash criptográfico tiene que resistir a un **adversario** que busca colisiones a propósito.

---

# Paradoja del cumpleaños

* Dos ataques distintos sobre un hash de n bits:
  * **Segunda preimagen**: dado m, encontrar otro m' con el mismo hash → ≈ 2ⁿ intentos.
  * **Colisión**: encontrar *cualquier* par m ≠ m' con el mismo hash → ≈ 2^(n/2) intentos.

--

* ¿Por qué? Con k intentos no comparo contra uno solo: comparo **todos los pares**, k(k−1)/2 ≈ k²/2.
  * Cada par choca con probabilidad 1/2ⁿ. Espero una colisión cuando k²/2 ≈ 2ⁿ → k ≈ 2^(n/2).
  * Con 23 personas hay 253 pares: la probabilidad de que dos cumplan el mismo día ya supera el 50%.

???

**La confusión típica.** Si busco un mensaje que choque con *uno dado* (segunda preimagen), cada intento acierta con probabilidad 1/2ⁿ y necesito del orden de 2ⁿ intentos. Pero si me alcanza con que choquen *dos cualesquiera*, cada intento nuevo se compara contra todos los anteriores: con k intentos tengo ≈ k²/2 pares, y los pares crecen cuadráticamente.

**El cumpleaños.** Contra mí (que alguien cumpla el mismo día que yo) necesito ≈ 253 personas para llegar al 50% (casualmente, el mismo número que los pares entre 23). Entre cualquier par, alcanzan 23. Es la misma diferencia: 365 contra √365 ≈ 19.

---

# Paradoja del cumpleaños: ejemplo

* SHA-256 truncado a 32 bits (2³² ≈ 4.300 millones de valores):

```
SHA-256("mensaje77585")  = ac00e6d0 c7a71da0 ...
SHA-256("mensaje105613") = ac00e6d0 9ce99919 ...
```

* Colisión en el intento 105.614, del orden de 2¹⁶ = 65.536.

--

* Por eso SHA-256 da 128 bits de seguridad contra colisiones, y MD5 (128 bits) sólo 64.

???

**Números más precisos.** La probabilidad llega al 50% con ≈ 1,18 · 2^(n/2) intentos, y el promedio es ≈ 1,25 · 2^(n/2). Para 32 bits el promedio da ≈ 82.000. Medido con 200 corridas: 85.000. El ataque es trivial de programar: genero mensajes, guardo cada hash en un `HashMap` y paro cuando uno ya está.

**Por qué importa.** El atacante prepara dos contratos, uno inofensivo y uno trucho, y genera muchas variantes de cada uno cambiando detalles invisibles (espacios, comas). Cuando una variante de cada uno tiene el mismo hash, hace firmar la inofensiva y la firma también sirve para la trucha. Por eso la longitud de un hash se elige pensando en n/2: SHA-256 da 128 bits de seguridad contra colisiones.

**Conexión con hash tables.** Es la misma cuenta: en una tabla de M buckets, la primera colisión aparece después de ≈ √M inserciones. Con un millón de buckets, alrededor del elemento 1.250. Por eso toda tabla de hash tiene que manejar colisiones, aunque esté casi vacía.

---

# Usos de los hashes

* **Integridad**: publicar el hash de un archivo para verificar la descarga.
* **Git**: cada commit, árbol y archivo se identifica por su hash (SHA-1). Si cambia un byte, cambia el id.

--

* **Contraseñas**: se guarda el hash, no la contraseña.
  * Con una **salt** aleatoria por usuario: dos usuarios con la misma contraseña tienen hashes distintos.
  * Con funciones lentas a propósito (bcrypt, scrypt, Argon2): la fuerza bruta se vuelve cara.

--

* **MAC**: hash con una clave secreta (HMAC). Da **integridad y autenticación**: sólo quien tiene la clave pudo generarlo.

--

* **MD5** y **SHA-1** ya no son seguros: hay colisiones prácticas (MD5 desde 2004, SHA-1 desde 2017). Hoy: SHA-256 o SHA-3.

???

Salt vs IV: los dos son valores aleatorios no secretos que hacen que la misma entrada dé salidas distintas. La salt se usa en hashes de contraseñas y el IV en cifrado.

Git usa una versión de SHA-1 que detecta los ataques de colisión conocidos y desde la versión 2.29 soporta repositorios con SHA-256.

---

# SHA-256 en Java

```java
MessageDigest sha = MessageDigest.getInstance("SHA-256");
byte[] digest = sha.digest("abc".getBytes(UTF_8));
String hex = HexFormat.of().formatHex(digest);
// ba7816bf8f01cfea414140de5dae2223b00361a396177a9cb410ff61f20015ad
```

---

class: center, middle, inverse

# Criptografía de clave pública

---

# El problema de la distribución de claves

* Con criptografía simétrica, cada par de usuarios necesita su propia clave.
  * n usuarios → n · (n − 1) / 2 claves. Con 100 usuarios: **4.950 claves**.

--

* Y cada clave hay que compartirla **por un canal seguro**... que es justamente lo que no tenemos.

--

* ¿Se puede acordar una clave secreta hablando sólo por un canal público?

---

# Diffie-Hellman (1976)

* Públicos: un primo **p** y un generador **g**. Ej: p = 23, g = 5.

--

| Alice | Canal público | Bob |
|---|:-:|---|
| Elige a = 6 (secreto) | | Elige b = 15 (secreto) |
| A = 5⁶ mod 23 = **8** | A = 8 → | |
| | ← B = 19 | B = 5¹⁵ mod 23 = **19** |
| s = 19⁶ mod 23 = **2** | | s = 8¹⁵ mod 23 = **2** |

--

* Los dos llegan a s = g^(ab) mod p. Eve vio p, g, A y B.
* Para obtener s, Eve necesita a o b: es el problema del **logaritmo discreto**. Con p de 2048 bits no se conoce un algoritmo eficiente.

???

Diffie-Hellman no cifra nada: sólo acuerda una clave. Después se usa esa clave con AES.

Ojo: así como está, no autentica. Un atacante en el medio puede hacer un Diffie-Hellman con cada uno. Por eso se combina con firmas digitales.

---

# Clave pública

* Cada usuario tiene **dos claves**: una **pública** (se publica) y una **privada** (sólo la tiene el dueño).
  * Lo que cifra una, sólo lo descifra la otra.
  * No se puede deducir la privada a partir de la pública: **función trampa** (trapdoor).

--

* **Cifrar**: Alice cifra con la clave **pública de Bob**. Sólo Bob puede descifrar.

--

* **Firmar**: Bob cifra con su clave **privada**. Cualquiera verifica con la pública de Bob, y sólo Bob pudo haberlo generado.

--

* Problema: es mucho más lento que el cifrado simétrico (del orden de 100 a 1.000 veces).

---

# RSA: notación

* **a mod n**: el resto de dividir a por n. Ej: 561 mod 280 = 1. En Java, `a % n`.
* **a ≡ b (mod n)**: a y b dan el mismo resto al dividir por n. Ej: 561 ≡ 1 (mod 280).

--

* **mcd(a, b)**: máximo común divisor. Si mcd(a, b) = 1, a y b son **coprimos**.
* **Inverso modular** de e (mod n): el d tal que e · d ≡ 1 (mod n). Existe sólo si mcd(e, n) = 1.

--

* **φ(N)** (función de Euler): cuántos números entre 1 y N son coprimos con N.
  * Si N = p · q, con p y q primos: φ(N) = (p − 1)(q − 1).
* **Teorema de Euler**: si mcd(m, N) = 1, entonces m^φ(N) ≡ 1 (mod N).

???

**≡ no es =.** 561 ≡ 1 (mod 280) quiere decir que 561 y 1 están "en el mismo lugar del reloj" de 280 horas: su diferencia (560) es múltiplo de 280. Todas las cuentas de RSA se hacen en ese reloj: los resultados siempre quedan entre 0 y n − 1.

**Ojo con Java:** `%` devuelve negativo si a es negativo (`-7 % 3 == -1`, pero -7 mod 3 = 2). Por eso `modInverse` termina con `(oldS % phi + phi) % phi`.

**Inverso modular.** Es la "división" en aritmética modular: no existe 1/3 entre enteros, pero módulo 280 el 187 cumple ese papel, porque 3 · 187 = 561 ≡ 1. Si e y n tienen un factor común, no hay inverso. Por ejemplo, 2 no tiene inverso módulo 280: 2 · d siempre es par y nunca deja resto 1.

**Por qué φ(p · q) = (p − 1)(q − 1).** De los números entre 1 y pq, los que no son coprimos con pq son los múltiplos de p (hay q) y los múltiplos de q (hay p), y pq se contó dos veces: pq − q − p + 1 = (p − 1)(q − 1). Para 319: 319 − 29 − 11 + 1 = 280. Calcularlo es fácil *si conozco p y q*. Sólo con N, hay que factorizar.

**Teorema de Euler.** Elevar a la φ(N) "da la vuelta completa" y vuelve a 1. Ej: 2²⁸⁰ mod 319 = 1. Es la razón por la que descifrar devuelve el mensaje original (siguiente slide).

---

# RSA: generación de claves

1. Elegir dos primos grandes **p** y **q**. Ej: p = 11, q = 29.
2. **N** = p · q = 319 (el **módulo**). **φ** = φ(N) = (p − 1)(q − 1) = 280.
3. Elegir **e** (exponente **público**) con mcd(e, φ) = 1. Ej: e = 3.
4. Calcular **d** (exponente **privado**): el inverso de e (mod φ). Ej: d = 187 (3 · 187 = 561 ≡ 1).

--

* Clave pública: **(e, N)** = (3, 319). Clave privada: **(d, N)** = (187, 319).

--

* El mensaje **m** es un número menor que N; **c** es el cifrado.
* Cifrar: **c = mᵉ mod N**. Descifrar: **m = cᵈ mod N**.
  * m = 100 → c = 100³ mod 319 = **254** → 254¹⁸⁷ mod 319 = **100**

???

Por qué funciona: como e · d = 1 + k · φ, por el teorema de Euler (mᵉ)ᵈ = m · (m^φ)ᵏ ≡ m (mod N). Vale para todo m < N (con el teorema chino del resto, incluso si m no es coprimo con N). En el ejemplo lo verificamos para los 319 valores posibles de m.

Seguridad: N y e son públicos. Para calcular d hace falta φ, y φ sólo se calcula fácil si se conocen p y q. Factorizar N es el problema difícil. Una vez generadas las claves, p, q y φ se descartan (o se guardan como parte de la clave privada).

¿Por qué e = 3? Porque es chico y coprimo con 280. En la práctica se usa casi siempre e = 65537 = 2¹⁶ + 1: es primo y tiene sólo dos bits en 1, así que cifrar requiere pocas multiplicaciones. Con mensajes de texto, m se obtiene interpretando los bytes como un número (en la práctica, con padding).

---

# RSA: ¿cómo se calcula d?

* Hay que encontrar el **inverso modular** de e módulo φ: **Euclides extendido**.

```java
static long modInverse(long e, long phi) {
    long oldR = e, r = phi, oldS = 1, s = 0;
    while (r != 0) {
        long q = oldR / r;
        long t = r;  r = oldR - q * r;  oldR = t;
        t = s;       s = oldS - q * s;  oldS = t;
    }
    if (oldR != 1) throw new ArithmeticException("no existe el inverso");
    return (oldS % phi + phi) % phi;
}
```

* `modInverse(3, 280)` → `187`

--

* Mantiene el invariante oldS · e ≡ oldR (mod φ). Cuando oldR llega a mcd(e, φ) = 1, oldS es el inverso.
* Cantidad de iteraciones: **O(log φ)**.

???

Probado contra BigInteger.modInverse con 10.000 casos al azar.

---

# RSA: ¿de dónde salen los primos?

* ¿Qué tamaño tiene que tener N? Hoy, **al menos 2048 bits** (3072 para seguridad más allá de 2030).

--

* ¿Cómo se elige un primo grande al azar? **Probar y verificar**.
  * **Teorema de los números primos**: hay ≈ N / ln N primos menores que N.
  * Con 512 bits hay ≈ 4 · 10¹⁵¹ primos: nunca se acaban y dos personas no eligen los mismos.

--

* ¿Cómo se verifica que es primo?
  * En la práctica: **Miller-Rabin**, probabilístico. `BigInteger.probablePrime` garantiza una probabilidad de error menor a 2⁻¹⁰⁰.
  * Agrawal-Kayal-Saxena (2002): primalidad está en **P**. Importante en teoría, demasiado lento en la práctica.

---

# RSA en Java

```java
SecureRandom random = new SecureRandom();

BigInteger p   = BigInteger.probablePrime(n / 2, random);
BigInteger q   = BigInteger.probablePrime(n / 2, random);
BigInteger phi = p.subtract(BigInteger.ONE).multiply(q.subtract(BigInteger.ONE));

BigInteger N = p.multiply(q);                // módulo
BigInteger e = BigInteger.valueOf(65537);    // clave pública
BigInteger d = e.modInverse(phi);            // clave privada

BigInteger c    = m.modPow(e, N);            // cifrar
BigInteger back = c.modPow(d, N);            // descifrar
```

???

Probado con n = 2048: cifrar y descifrar m = 100 devuelve el mensaje original.

65537 = 2¹⁶ + 1: tiene sólo dos bits en 1, así que cifrar es muy rápido. `BigInteger.modPow` no multiplica b veces: eleva al cuadrado y multiplica según los bits del exponente, O(log b) multiplicaciones.

modInverse tira ArithmeticException si 65537 divide a p − 1 o a q − 1, porque ahí no existe el inverso. Es muy raro; en ese caso se eligen otros primos.

---

# Ataques a RSA

* **Factorizar N**: con p y q se calcula φ y de ahí d. No se conoce un algoritmo eficiente.

--

* **RSA "crudo" es determinístico**: si Alice sólo puede mandar "ATACAR" o "HUIR", Eve cifra las dos con la clave pública de Bob y compara.
  * Solución: agregar **padding aleatorio** antes de cifrar (OAEP).

--

* **Timing attack**: medir cuánto tarda Bob en descifrar da información sobre los bits de d (por cada bit en 1 se hace una multiplicación más).
  * Solución: implementaciones de tiempo constante.

--

* **Computación cuántica**: el algoritmo de Shor factoriza en tiempo polinomial. Por eso ya hay estándares **post-cuánticos** (ML-KEM, NIST 2024).

???

El timing attack lo publicó Paul Kocher en 1996.

Otro error clásico: dos usuarios que comparten el mismo N. Cada uno puede calcular la clave privada del otro.

---

class: center, middle, inverse

# Juntando todo

---

# Criptografía híbrida

* La clave pública resuelve la **distribución de claves**, pero es lenta.
* La simétrica es rápida, pero necesita una clave compartida.

--

* Solución: usar **las dos**. Así funciona HTTPS (TLS):
  1. Con criptografía asimétrica (Diffie-Hellman + firmas) se acuerda una **clave de sesión**.
  2. Todos los datos se cifran con esa clave usando **AES-GCM**.

--

| | Simétrica | Asimétrica |
|---|---|---|
| Claves | Una clave compartida | Par pública / privada |
| Distribución | Difícil: canal seguro | La pública se publica |
| Velocidad | Rápida | 100 a 1.000 veces más lenta |
| Tamaño de clave | 128 a 256 bits | 2048 a 4096 bits (RSA) |
| Uso | Cifrar los datos | Acordar claves y firmar |

---

# Firma digital

* Firmar el mensaje entero con RSA es lento. Se firma su **hash**:

```
 Bob:    m ──► [ SHA-256 ] ──► h ──► [ cifrar con privada de Bob ] ──► firma

 Alice:  m ──► [ SHA-256 ] ──► h₁
                                    ¿h₁ == h₂?
         firma ──► [ descifrar con pública de Bob ] ──► h₂
```

--

* Da **integridad** (si cambia m, cambia el hash), **autenticación** y **no repudio** (sólo Bob tiene la privada).

--

* ¿Cómo sabe Alice que esa clave pública es realmente de Bob?
  * **Certificados**: una autoridad de confianza firma "esta clave pública es de Bob". Es lo que valida el navegador en cada sitio HTTPS.

???

Ejemplo cotidiano: los commits firmados de GitHub (la marca "Verified"). Git firma el commit con la clave privada del autor y GitHub lo verifica con la clave pública que el autor subió.

---

# Resumen

| Algoritmo | Tipo | Uso | Estado |
|---|---|---|---|
| César, Vigenère | Clásico | Histórico | Roto |
| DES | Simétrico, bloque | Cifrado | Roto (clave de 56 bits) |
| 3DES | Simétrico, bloque | Cifrado | Obsoleto |
| AES | Simétrico, bloque | Cifrado | Seguro |
| RC4 | Simétrico, stream | Cifrado | Roto |
| MD5, SHA-1 | Hash | Integridad | Rotos (colisiones) |
| SHA-256, SHA-3 | Hash | Integridad, firmas, MAC | Seguros |
| Diffie-Hellman | Asimétrico | Acuerdo de claves | Seguro con claves grandes |
| RSA | Asimétrico | Cifrado de claves, firmas | Seguro con ≥ 2048 bits |

???

"Seguro" quiere decir: sin ataques prácticos conocidos hoy, con computadoras clásicas. RSA y Diffie-Hellman caerían con una computadora cuántica suficientemente grande.
