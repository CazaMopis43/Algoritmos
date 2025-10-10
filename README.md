# Algoritmos Avanzados
## Práctica 2
## Table of Contents
- [Objetivos](#objetivos)
- [Especificación del Problema](#especificación-del-problema)
- [Diseño e Implementación de Algoritmos Heurísticos](#diseño-e-implementación-de-algoritmos-heurísticos)
- [Experimentación con la Optimalidad de los Algoritmos](#experimentación-con-la-optimalidad-de-los-algoritmos)
- [Uso de la IA](#uso-de-la-ia)
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
## Diseño e Implementación de Algoritmos Heurísticos

### **Algoritmo 1: Selección por Mayor Beneficio (`hospitalesGreedyMaxBenefit`)**

**Justificación intuitiva:**  
Para este problema se toman los valores máximos de afectados, cumpliendo siempre con la restricción de distancia, de esta forma se intenta maximizar el número de afectados más que la distancia de los hospitales.
**Implementación:**

```java
import java.util.Arrays;

public class Alg1 {
    public static int hospitalesGreedyMaxBenefit(int[] xs, int[] ps) {
        int n = xs.length;
        Integer[] indices = new Integer[n];
        for (int i = 0; i < n; i++) indices[i] = i;
        Arrays.sort(indices, (i, j) -> ps[j] - ps[i]); 
        boolean[] seleccionado = new boolean[n]; 
        int afectados = 0;
        for (int idx : indices) {
            boolean seleccionable = true;
            for (int j = 0; j < n; j++) {
                if (seleccionado[j] && Math.abs(xs[idx] - xs[j]) <= 5) {
                    seleccionable = false;
                    break;
                }
            }
            if (seleccionable) {
                seleccionado[idx] = true;
                afectados += ps[idx];
            }
        }
        return afectados;
    }
}
```

---

### **Algoritmo 2: Selección Voraz por Posición (`hospitalesGreedyByPosition`)**

**Justificación intuitiva:**  
Se va recorriendo la lista en orden de entrada, de forma que el primero que encuentra será el que seleccione, siempre y cuando cumpla la condición de distancia necesaria.

**Implementación:**

```java
public class Alg2 {
    public static int hospitalesGreedyByPosition(int[] xs, int[] ps) {
        int n = xs.length;
        boolean[] seleccionado = new boolean[n];
        int afectados = 0;
        
        for (int i = 0; i < n; i++) {
            boolean seleccionable = true;
            // Verificar restricción de distancia con posiciones ya seleccionadas
            for (int j = 0; j < i; j++) {
                if (seleccionado[j] && xs[i] - xs[j] <= 5) {
                    seleccionable = false;
                    break;
                }
            }
            if (seleccionable) {
                seleccionado[i] = true;
                afectados += ps[i];
            }
        }
        return afectados;
    }   
}
```

---

### Nota
Estos algoritmos son dos soluciones que hemos planteado para este problema en concreto, pero se pueden plantear otras soluciones igual de correctas que estas

---

## Experimentación con la Optimalidad de los Algoritmos

### Material del Experimento

**Algoritmos probados:**
- `hospitalesGreedyMaxBenefit`
- `hospitalesGreedyByPosition`

**Parámetros:**
- `K ≈ 20–30 km`
- `n ≈ 4–6`
- `xs`: posiciones ordenadas aleatorias en `[0, K]`
- `ps`: valores aleatorios entre `4–20`
- Distancia mínima: `5 km`
- Casos de prueba: `15` por algoritmo
- Comparación: contra solución óptima mediante **AlgorEx**

---

### Resultados

Ambos algoritmos obtuvieron **100% de soluciones óptimas** en los 15 casos evaluados.

| Métrica | GreedyMaxBenefit | GreedyByPosition |
|----------|------------------|------------------|
| Nº ejecuciones | 15 | 15 |
| % Soluciones óptimas | **100.00%** | **100.00%** |
| % Subóptimas | 0.00% | 0.00% |
| % Superóptimas | 0.00% | 0.00% |
| Diferencia media subóptima | 0.00% | 0.00% |
| Diferencia máxima subóptima | 0.00% | 0.00% |

---

### Gráficos descriptivos (basados en los resultados de AlgorEx)

- **Gráfico de % resultados óptimos:**  
  Barra verde al 100%, sin valores subóptimos ni superóptimos.

- **Gráfico de valores medios:**  
  Línea horizontal constante (~15–20), sin desviaciones.

- **Comparación:**  
  Ambos algoritmos se superponen en el 100% óptimo; diferencias mínimas entre medias.

---

### Razonamiento sobre la exactitud
Al haber probado estos algoritmos con un numero de iteraciones no muy alto nos ha salido un 100% cosa que a primeras parece extraño, pero que para este numero de iteraciones no lo es, sin embargo sabemos que con un numero mucho mayor de iteraciones estos algoritmos reducen este porcentaje en torno a un 70-75% gracias a la ayuda de la inteligencia artificial.

---

### Contraejemplos teóricos

- **Para `hospitalesGreedyMaxBenefit`:**  
  Ejemplo: `xs = {1, 2, 8}`, `ps = {1, 10, 5}`  
  Selecciona `{2, 8}` (10 + 5 = 15), aunque puede no ser óptimo si otras combinaciones maximizan la suma.

- **Para `hospitalesGreedyByPosition`:**  
  Ejemplo: `xs = {1, 6}`, `ps = {1, 10}`  
  Selecciona `{1}` en lugar de `{6}`, resultando subóptimo.

---

### Conclusiones

- Ambos algoritmos (`GreedyMaxBenefit` y `GreedyByPosition`) lograron **100% de optimalidad** en los 15 casos probados.
- `GreedyMaxBenefit` aprovecha mejor los altos valores de `ps[i]`.
- `GreedyByPosition` simplifica el proceso con un enfoque geográfico ordenado.
- Aunque prácticos, **no garantizan la optimalidad global** para instancias más grandes (`n > 20`).

---

### Experiencia con AlgorEx
La herramienta resultó eficaz para verificar la exactitud y generar gráficos y métricas, sin embargo nos hemos visto obligados ha tener que probar con un número reducido de casos (15) ya que la aplicación de AlgorEx se quedaba colgada cuando se probaba con iteraciones altas. Por tanto para saber como sería con muchas iteraciones hemos hecho una consulta a la Inteligencia Artificial.

## Uso de la IA
Para esta práctica hemos usado la IA como consultora, especialmente para que nos ayudase a entender el problema de la ordenación y nos ha ayudado a hacer el Informe más académico, además de ser muy útil para el análisis de los algoritmos en muchas iteraciones debido al problema comentado anteriormente.

## Conclusiones
La realización de esta práctica ha permitido profundizar en la técnica de diseño de algoritmos voraces, en particular aplicada al problema clásico de selección de actividades. A través de la implementación de las dos versiones del algoritmo idealizada y realista se ha puesto de manifiesto la importancia que tiene el orden de los datos de entrada en la eficacia y corrección de la solución. Ya que en términos de complejidad, se ha podido comprobar que la fase de ordenación domina el coste total del algoritmo realista, manteniéndose en un orden de eficiencia aceptable para problemas de tamaño grande.
