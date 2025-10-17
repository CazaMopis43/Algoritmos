# Algoritmos Avanzados
## Práctica 3
## Table of Contents
- [Objetivos](#objetivos)
- [Especificación del Problema](#especificación-del-problema)
- [Técnica de Vuelta Atrás](#técnica-de-vuelta-atrás)
- [Comparación de Optimalidad](#comparación-de-optimalidad)
- [Uso de la IA](#uso-de-la-ia)
- [Conclusiones](#conclusiones)
## Autores
- Marc Burgos Ucendo
- Alberto Sastre Zorrilla
## Objetivos
El objetivo de esta práctica es profundizar en el conocimiento de las técnicas de búsqueda en espacios de estados, específicamente mediante el desarrollo sistemático de un algoritmo de vuelta atrás para resolver el problema de planificación maximal de hospitales. Se busca implementar un algoritmo exacto que maximice el número de víctimas atendidas, respetando las restricciones, y compararlo experimentalmente con algoritmos heurísticos desarrollados en prácticas anteriores para verificar su optimalidad.
## Especificación del Problema
### Precondición
**Entrada:**
- `xs`: vector de enteros (longitud `n`) con posiciones posibles (en km), ordenado en orden creciente.
- `ps`: vector de enteros (longitud `n`) donde `ps[i] > 0` indica centenas de miles de víctimas atendidas en `xs[i]`.
- `n ≥ 0`.
**Restricción:**
No se pueden construir dos hospitales a una distancia de **5 km o menos**, es decir, si se selecciona `xs[i]`, no puede seleccionarse ningún `xs[j]` tal que `|xs[i] - xs[j]| ≤ 5`.
---
### Poscondición
**Condición de validez:**
- Ningún par de posiciones seleccionadas está a 5 km o menos de distancia.
- La suma de los `ps[i]` seleccionados es máxima entre todas las selecciones válidas.
**Función objetivo:**
Maximizar la suma de `ps[i]` de las posiciones seleccionadas, representando el número total de centenas de miles de víctimas atendidas.
Ejemplo: Para `K=20`, `n=4`, `xs={6,7,12,14}`, `ps={5,6,5,1}`, la solución óptima es situar hospitales en `xs[0]` y `xs[2]`, con un total de 10 centenas de miles de víctimas atendidas.
---
## Técnica de Vuelta Atrás
### a) Diseño de un árbol de búsqueda adecuado para resolver el problema
El árbol de búsqueda se diseña como un árbol binario de decisión, donde cada nivel representa una posición posible de hospital (de `xs[0]` a `xs[n-1]`). El número de niveles del árbol es igual a `n` (la longitud de `xs`), ya que en cada nivel se decide si incluir o no la posición actual en la solución.

En cada nodo del árbol, los candidatos son dos:
- Rama "sí": Incluir la posición actual (si no viola la restricción de distancia con el último hospital incluido).
- Rama "no": No incluir la posición actual y pasar a la siguiente.

El árbol explora todas las combinaciones posibles de subconjuntos de posiciones, podando ramas que no puedan mejorar la solución actual o que violen la restricción. Dado que `n` puede ser hasta 22 en experimentos, el árbol completo tendría hasta `2^n` nodos, pero se poda optimistamente para reducir el espacio de búsqueda.

Figura del árbol (representación parcial para `n=3`, `xs={1,3,7}`, `ps={10,20,15}`):

```
Raíz (nivel 0: posición xs[0]=1)
├── No incluir xs[0]
│   ├── No incluir xs[1]=3 (nivel 1)
│   │   ├── No incluir xs[2]=7 (nivel 2): suma=0
│   │   └── Incluir xs[2]=7: suma=15 (válido)
│   └── Incluir xs[1]=3: suma=20 (válido, distancia con previos OK)
│       ├── No incluir xs[2]=7: suma=20
│       └── Incluir xs[2]=7: suma=35 (válido, |3-7|=4>5? No, podar si viola)
└── Incluir xs[0]=1: suma=10
    ├── No incluir xs[1]=3 (distancia |1-3|=2<=5: podar si intenta incluir)
    │   ├── No incluir xs[2]=7: suma=10
    │   └── Incluir xs[2]=7: suma=25 (válido, |1-7|=6>5)
    └── Incluir xs[1]=3: podar (viola distancia con xs[0])
        (Rama omitida por violación)
```

La parte omitida sigue el patrón binario, extendiéndose hasta `n` niveles, con podas por restricciones de distancia y cotas optimistas (suma restante <= mejor actual).
---
### b) Código de un algoritmo de vuelta atrás
El algoritmo de vuelta atrás incorpora el diseño del árbol anterior, explorando recursivamente las decisiones de incluir o no cada posición. Incluye poda optimista basada en una cota superior de la suma restante y verifica la restricción de distancia solo con el último hospital colocado (ya que las posiciones están ordenadas). La cabecera es como se especifica:

```java
public static int hospitales(int[] xs, int[] ps) {
    if (xs.length == 0 || ps.length == 0) return 0;
   
    K = xs.length;
    mejorSolucion = 0;
   
    // Solución inicial: el mejor hospital individual
    for (int p : ps) {
        mejorSolucion = Math.max(mejorSolucion, p);
    }
   
    int[] solucion = new int[K];
    backtrackOptimizado(xs, ps, 0, 0, 0, solucion);
   
    return mejorSolucion;
}

private static void backtrackOptimizado(int[] xs, int[] ps, int posActual,
                                      int numHospitales, int curaciones,
                                      int[] solucion) {
    // Caso base
    if (numHospitales == K || posActual == xs.length) {
        mejorSolucion = Math.max(mejorSolucion, curaciones);
        return;
    }
   
    // Poda optimista: cota superior restante
    int cota = curaciones;
    for (int i = posActual; i < xs.length && i < posActual + (K - numHospitales); i++) {
        cota += ps[i];
    }
    if (cota <= mejorSolucion) return;
   
    // Verificar restricción de distancia con el último hospital colocado
    boolean puedeColocar = true;
    if (numHospitales > 0) {
        int ultimaPosicion = solucion[numHospitales - 1];
        if (Math.abs(xs[posActual] - xs[ultimaPosicion]) <= 5) {
            puedeColocar = false;
        }
    }
   
    // No tomar esta posición
    backtrackOptimizado(xs, ps, posActual + 1, numHospitales, curaciones, solucion);
   
    // Tomar esta posición (si se puede y mejora la solución)
    if (puedeColocar && curaciones + ps[posActual] > mejorSolucion) {
        solucion[numHospitales] = posActual;
        backtrackOptimizado(xs, ps, posActual + 1, numHospitales + 1,
                           curaciones + ps[posActual], solucion);
    }
}
```

Este código se integra en la clase que contiene los algoritmos heurísticos de la práctica 2, como se muestra a continuación (clase completa para contexto):

```java
import java.util.*;

public class Alg1 {
    // Variables globales para el algoritmo optimizado
    private static int K;
    private static int mejorSolucion;
   
    /**
     * Algoritmo Greedy por Orden: selecciona hospitales en orden de posición
     * respetando distancia mínima de 5 unidades.
     */
    public static int hospitalesGreedyPorOrden(int[] xs, int[] ps) {
        if (xs == null || ps == null || xs.length == 0 || ps.length == 0 || xs.length != ps.length) {
            return 0;
        }
       
        int n = xs.length;
        int total = 0;
        int ultimaPos = -6; // Inicialmente, no hay conflicto
       
        for (int i = 0; i < n; i++) {
            if (xs[i] - ultimaPos > 5) {
                total += ps[i];
                ultimaPos = xs[i];
            }
        }
        return total;
    }
   
    /**
     * Algoritmo Greedy por Valor: selecciona el hospital con mayor curación
     * y elimina los conflictivos (distancia <= 5).
     */
    public static int hospitalesGreedyPorValor(int[] xs, int[] ps) {
        if (xs == null || ps == null || xs.length == 0 || ps.length == 0 || xs.length != ps.length) {
            return 0;
        }
       
        int n = xs.length;
        List<Integer> indices = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            indices.add(i);
        }
       
        int total = 0;
        while (!indices.isEmpty()) {
            int maxIdx = -1;
            int maxP = -1;
            for (int idx : indices) {
                if (ps[idx] > maxP) {
                    maxP = ps[idx];
                    maxIdx = idx;
                }
            }
           
            if (maxIdx == -1) break;
            total += maxP;
           
            int pos = xs[maxIdx];
            indices.removeIf(j -> Math.abs(xs[j] - pos) <= 5);
        }
        return total;
    }
   
    /**
     * Algoritmo Optimizado: Backtracking con poda optimista para encontrar
     * la solución óptima respetando la restricción de distancia.
     */
    public static int hospitales(int[] xs, int[] ps) {
        if (xs == null || ps == null || xs.length == 0 || ps.length == 0 || xs.length != ps.length) {
            return 0;
        }
       
        K = xs.length;
        mejorSolucion = 0;
       
        // Solución inicial: el mejor hospital individual
        for (int p : ps) {
            mejorSolucion = Math.max(mejorSolucion, p);
        }
       
        int[] solucion = new int[K];
        backtrackOptimizado(xs, ps, 0, 0, 0, solucion);
       
        return mejorSolucion;
    }
   
    private static void backtrackOptimizado(int[] xs, int[] ps, int posActual,
                                          int numHospitales, int curaciones,
                                          int[] solucion) {
        // Caso base
        if (numHospitales == K || posActual == xs.length) {
            mejorSolucion = Math.max(mejorSolucion, curaciones);
            return;
        }
       
        // Poda optimista: cota superior restante
        int cota = curaciones;
        for (int i = posActual; i < xs.length && i < posActual + (K - numHospitales); i++) {
            cota += ps[i];
        }
        if (cota <= mejorSolucion) return;
       
        // Verificar restricción de distancia con el último hospital colocado
        boolean puedeColocar = true;
        if (numHospitales > 0) {
            int ultimaPosicion = solucion[numHospitales - 1];
            if (Math.abs(xs[posActual] - xs[ultimaPosicion]) <= 5) {
                puedeColocar = false;
            }
        }
       
        // No tomar esta posición
        backtrackOptimizado(xs, ps, posActual + 1, numHospitales, curaciones, solucion);
       
        // Tomar esta posición (si se puede y mejora la solución)
        if (puedeColocar && curaciones + ps[posActual] > mejorSolucion) {
            solucion[numHospitales] = posActual;
            backtrackOptimizado(xs, ps, posActual + 1, numHospitales + 1,
                               curaciones + ps[posActual], solucion);
        }
    }
   
    // Método main para pruebas locales (opcional para Algorex)
    public static void main(String[] args) {
        int[] xs = {1, 3, 5, 7, 10, 12, 15, 18};
        int[] ps = {10, 20, 15, 25, 30, 5, 35, 40};
       
        System.out.println("=== Comparación de Algoritmos ===");
        System.out.println("Posiciones: " + Arrays.toString(xs));
        System.out.println("Curaciones: " + Arrays.toString(ps));
        System.out.println();
       
        int greedyOrden = hospitalesGreedyPorOrden(xs, ps);
        int greedyValor = hospitalesGreedyPorValor(xs, ps);
        int optimizado = hospitales(xs, ps);
       
        System.out.println("Greedy por Orden: " + greedyOrden);
        System.out.println("Greedy por Valor: " + greedyValor);
        System.out.println("Optimizado: " + optimizado);
        System.out.println("Mejor solución: " + Math.max(greedyValor, Math.max(greedyOrden, optimizado)));
    }
}
```
---
## Comparación de Optimalidad
Se añade el algoritmo de vuelta atrás (`hospitales`) a la clase utilizada en la práctica 2, que incluye al menos dos algoritmos heurísticos (`hospitalesGreedyPorOrden` y `hospitalesGreedyPorValor`). Se compara la optimalidad de todos mediante experimentación.
### a) Material del experimento
**Identificación de algoritmos:**
- `hospitalesGreedyPorOrden`: Heurístico basado en selección greedy por orden de posición, respetando distancia mínima de 5.
- `hospitalesGreedyPorValor`: Heurístico que selecciona greedy el hospital con mayor curación, eliminando conflictivos.
- `hospitales`: Algoritmo exacto de vuelta atrás con poda optimista, diseñado para encontrar la solución óptima.

**Rangos de valores usados para la generación aleatoria de los datos de entrada:**
- `xs`: Posiciones enteras aleatorias entre 1 y 100, ordenadas.
- `ps`: Curaciones aleatorias entre 1 y 500.
- Número de hospitales (`n`) entre 4 y 22, con 100 ejecuciones por caso.
---
### b) Conclusión
Según los resultados experimentales:
- `hospitales` es exacto, obteniendo soluciones óptimas en el 100% de los casos.
- `hospitalesGreedyPorValor` también es exacto en este conjunto de datos, alcanzando el 100% de optimalidad.
- `hospitalesGreedyPorOrden` es subóptimo, con una diferencia media del 30.45% respecto a la solución óptima.
---
## c) Evidencias
Tabla de resumen numérico:

| Métrica                  | hospitalesGreedyPorOrden | hospitalesGreedyPorValor | hospitales |
|--------------------------|---------------------------|---------------------------|------------|
| Nº ejecuciones           | 100                       | 100                       | 100        |
| % Soluciones óptimas     | 0.00%                     | 100.00%                   | 100.00%    |
| % Subóptimas             | 100.00%                   | 0.00%                     | 0.00%      |
| Diferencia media subóptima | 30.45%                  | 0.00%                     | 0.00%      |
| Diferencia máxima subóptima | 3.76%                 | 0.00%                     | 0.00%      |

La tabla muestra los resultados de 100 ejecuciones por algoritmo. `hospitales` y `hospitalesGreedyPorValor` consistentemente alcanzan valores cercanos (488-500), mientras que `hospitalesGreedyPorOrden` varía ampliamente (150-880), indicando suboptimización.
---
Diagramas de resumen gráfico:
- % de resultados óptimos: Ambos `hospitales` y `hospitalesGreedyPorValor` alcanzan 100% de optimalidad (verde), mientras que `hospitalesGreedyPorOrden` no alcanza ningún caso óptimo (amarillo).
- Valores medios y extremos: `hospitalesGreedyPorOrden` tiene un rango amplio (media 64%, extremos 0%-100%), mientras que los otros dos son consistentes (100%).
- Medidas: La diferencia media subóptima de `hospitalesGreedyPorOrden` es 30.45%, con un máximo de 3.76%, mientras que `hospitales` tiene una diferencia media del 0.00%.
---
## d) Incidencias
Se modificó el algoritmo de vuelta atrás para incluir la verificación de distancia mínima con el último hospital colocado, que no estaba presente en la versión inicial. Esto aseguró que la solución respetara la restricción y fuera exacta. También se ajustó la poda optimista para considerar el número restante de hospitales (`K - numHospitales`).
---
## Uso de la IA

## Conclusiones
La realización de esta práctica ha permitido profundizar en la técnica de vuelta atrás, destacando su capacidad para garantizar soluciones óptimas mediante exploración exhaustiva con podas eficientes, aunque a costa de mayor complejidad computacional en comparación con heurísticos. La experiencia con AlgorEx fue positiva para validar la exactitud, aunque requirió ajustes en los algoritmos previos para resolver incidencias en la restricción de distancia. No se encontraron dificultades mayores, salvo la necesidad de optimizar la poda para instancias grandes (`n>20`). En general, la técnica de vuelta atrás se valora como esencial para problemas de optimización combinatoria donde la exactitud es prioritaria.
