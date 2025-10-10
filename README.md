# Algoritmos Avanzados
## Práctica 2
## Table of Contents
- [Objetivos](#objetivos)
- [Especificación del Problema](#especificacion-del-problema)
- [Algoritmo Realista](#algoritmo-realista)
- [Complejidad](#complejidad)
- [Uso de IA](#uso-de-la-IA)
- [Conclusiones](#conclusiones)
## Autores
- Alberto Sastre Zorrilla.
- Marc Burgos Ucendo.
## Objetivos 
Para esta Segunda práctica nuestro objetivo es familiarizarnos con el uso de los Algoritmos Heurísticos. Para ello se nos ha propuesto resolver un problema de colocación de hospitales usando dos algoritmos heurísticos distintos.
## Especificación del Problema

### Precondición

**Entrada:**
- `xs`: vector de enteros (longitud `n`) con posiciones posibles (en km) dentro de `[0, K]`.
- `ps`: vector de enteros (longitud `n`) donde `ps[i] > 0` indica centenas de miles de víctimas atendidas en `xs[i]`.
- `n ≥ 0` y `xs` está ordenado crecientemente.

**Restricción:**  
No se pueden construir dos hospitales a una distancia de **5 km o menos**, es decir, si se selecciona `xs[i]`, no puede seleccionarse ningún `xs[j]` tal que `|xs[i] - xs[j]| ≤ 5`.

---

### Poscondición

**Condición de validez:**
- Ningún par de posiciones seleccionadas está a 5 km o menos de distancia.
- La suma de los `ps[i]` seleccionados es máxima entre todas las selecciones válidas.

**Función objetivo:**  
Maximizar la suma de `ps[i]` de las posiciones seleccionadas, representando el número total de víctimas atendidas.

---


## Complejidad
### Algoritmo idealizado (seleccionarActividadesIdeal).
Se supone que las actividades ya vienen ordenadas en orden creciente  de fin.
La reserva de array (boolean[] seleccionadas = new boolean[n]) tiene un coste de O(n).
Las asignaciones (seleccionadas[0] = true y ultimaSeleccionada = 0) tienen un coste de O(1).
El bucle for (for (j = 1; j < n; j++)) al tener dentro asignaciones simples como ifs, tiene una complejidad de O(n).

Siendo inicio.length = n, 
n/j=1 Σ1=n->O(n) esto nos da la complejidad del bucle for.
Y sumando las complejidades del programa nos da: O(n)+O(1) = Complejidad O(n)

(1)
### Algoritmo realista (seleccionarActividadesRealista)
Primero se llama al subprograma 'ordenar(f)'.
Se crea un array booleano (boolean[] seleccionadas = new boolean[c.length];) que tiene una complejidad de O(n).
Las asignaciones lineales como (seleccionadas[indices[0]] = true;) tienen una complejidad de O(1). 
El bucle for (for (i = 1; i < indices.length; i++)) contiene dentro de sí, comparaciones y asignaciones, que tienen una complejidad de O(1).
El subprograma 'ordenar' tiene una inicialización O(1) y un bucle for que tiene una complejidad de O(n).
El bucle principal de inserción contiene un bucle for que llega hasta n-1 iteraciones y en su interior anidado un bucle while. 
La fórmula para calcular la suma de los primeros n-1 enteros es:
T = n-1/i=1 Σi = n(n-1)/2 = (n^2-n)/2 y nos quedamos con el número más grande que es n^2. Por lo tanto al sumar las complejidades del programa al completo sacamos que O(n)+O(1)+O(n^2) = Complejidad O(n^2).

(2)
## Uso de la IA
Para esta práctica hemos usado la IA como consultora, especialmente para que nos ayudase a entender el problema de la ordenación y nos ha ayudado a hacer el Informe más académico.
## Conclusiones
La realización de esta práctica ha permitido profundizar en la técnica de diseño de algoritmos voraces, en particular aplicada al problema clásico de selección de actividades. A través de la implementación de las dos versiones del algoritmo idealizada y realista se ha puesto de manifiesto la importancia que tiene el orden de los datos de entrada en la eficacia y corrección de la solución. Ya que en términos de complejidad, se ha podido comprobar que la fase de ordenación domina el coste total del algoritmo realista, manteniéndose en un orden de eficiencia aceptable para problemas de tamaño grande.
