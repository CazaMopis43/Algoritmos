# 📝 Informe de Práctica 3b: Planificación Maximal de Hospitales

**Asignatura:** Algoritmos Avanzados  
**Curso:** 2025/2026

**Marc Burgos Ucendo,Alberto Sastre Zorrilla**
---
## 1. Backtracking
Después de los comentarios del profesor hemos modificado nuestro codigo para que cumpla con lo que se comentó
```java
public static int backtracking(int[] xs, int[] ps) {
    if (xs == null || ps == null || xs.length != ps.length || xs.length == 0) return 0;
    int n = xs.length;
    int[] siguienteValido = new int[n];
    for (int i = 0; i < n; i++) {
        int j = i + 1;
        while (j < n && Math.abs(xs[j] - xs[i]) <= 5) j++;
        siguienteValido[i] = j;
    }
    return buscarBacktracking(ps, 0, siguienteValido);
}

private static int buscarBacktracking(int[] ps, int indice, int[] siguienteValido) {
    if (indice >= ps.length) return 0;
    int sinColocar = buscarBacktracking(ps, indice + 1, siguienteValido);
    int conColocar = ps[indice] + buscarBacktracking(ps, siguienteValido[indice], siguienteValido);
    return Math.max(sinColocar, conColocar);
}
```
## 2. Función de Cota Superior para la Poda

Para resolver este problema de **maximización** (maximizar la suma de valores), la estrategia de poda requiere definir una **Cota Superior ($\text{UB}$)**. Esta cota debe representar el máximo valor que la solución óptima de la subrama actual podría alcanzar.

#### (1) Definición de la función de Cota Superior

Se utiliza una **relajación** del problema que ignora las restricciones de incompatibilidad para todos los elementos futuros.

Sea:

* $\text{acumulado}$: La suma de valores de los elementos ya seleccionados en el camino al nodo actual.

* $\text{sumaSufijos}[i]$: La suma total de los valores $ps[k]$ para todos los índices $k \ge i$ (es decir, la suma de todos los valores restantes, independientemente de su posición).

La cota superior $\text{UB}(i)$ para un nodo en el índice $i$ se define como:

$$
\text{UB}(i) = \text{acumulado} + \text{sumaSufijos}[i]
$$

**Criterio de Poda:** Se poda la rama si la cota superior del nodo ($\text{UB}$) es menor o igual que la mejor solución (`mejor`) encontrada hasta el momento:

$$
\text{Si } \text{UB}(i) \le \text{mejor} \text{, podar.}
$$
```java
public static int branchAndBound(int[] xs, int[] ps) {
    if (xs == null || ps == null || xs.length != ps.length || xs.length == 0) return 0;
    int n = xs.length;

    int[] siguienteValido = new int[n];
    for (int i = 0; i < n; i++) {
        int j = i + 1;
        while (j < n && Math.abs(xs[j] - xs[i]) <= 5) j++;
        siguienteValido[i] = j;
    }

    int[] sumaSufijos = new int[n + 1];
    sumaSufijos[n] = 0;
    for (int i = n - 1; i >= 0; i--) {
        sumaSufijos[i] = sumaSufijos[i + 1] + ps[i];
    }

    int[] mejor = new int[]{0};
    explorarBnB(ps, 0, 0, siguienteValido, sumaSufijos, mejor);
    return mejor[0];
}

private static void explorarBnB(int[] ps, int indice, int acumulado,
                               int[] siguienteValido, int[] sumaSufijos, int[] mejor) {
    if (acumulado > mejor[0]) mejor[0] = acumulado;

    if (indice >= ps.length) return;

    int cotaSuperior = acumulado + sumaSufijos[indice];
    if (cotaSuperior <= mejor[0]) {
        return;
    }

    explorarBnB(ps, siguienteValido[indice], acumulado + ps[indice], siguienteValido, sumaSufijos, mejor);
    explorarBnB(ps, indice + 1, acumulado, siguienteValido, sumaSufijos, mejor);
}
```
## 3. Comparación de optimalidad

###  Material del experimento
Se han comparado cuatro algoritmos, detallados a continuación:

| Algoritmo         | Técnica de Diseño                     | Elemento Diferenciador                     |
|------------------|--------------------------------------|-------------------------------------------|
| backtracking      | Vuelta atrás (Backtracking)          | -                                         |
| branchAndBound    | Ramificación y Poda (Branch and Bound) | Incorporación de una Cota Superior       |
| greedyPorOrden    | Voraz (Greedy)                       | Criterio de selección por Orden           |
| greedyPorValor    | Voraz (Greedy)                       | Criterio de selección por Valor           |

---

###  Conclusión
Según los resultados de la experimentación:

- **Algoritmos exactos:** `backtracking` y `branchAndBound`, ambos alcanzan el resultado óptimo en el **100,00 %** de las ejecuciones.  
- **Algoritmos heurísticos:** `greedyPorOrden` (0,00 % óptimo) y `greedyPorValor` (1,00 % óptimo), no garantizan optimalidad.

---

###  Evidencias

#### Resumen Numérico

| Medida                     | backtracking | branchAndBound | greedyPorOrden | greedyPorValor |
|----------------------------|-------------|----------------|----------------|----------------|
| % Soluciones óptimas       | 100,00 %    | 100,00 %       | 0,00 %         | 1,00 %         |
| % Soluciones subóptimas    | 0,00 %      | 0,00 %         | 100,00 %       | 99,00 %        |
| % Diferencia media subóptima | 0,00 %    | 0,00 %         | 24,58 %        | 77,37 %        |

La tabla demuestra la exactitud de las técnicas de búsqueda exhaustiva (`backtracking` y `branchAndBound`). Los algoritmos voraces (`greedy`) muestran un rendimiento pobre en optimalidad, incurriendo en grandes diferencias medias (hasta 77,37 %) respecto al óptimo.

#### Resumen Gráfico
- El gráfico de barras sobre el % de resultados óptimos corrobora la información:  
  - Backtracking y Branch and Bound: 100 % óptimos (barras verdes).  
  - Algoritmos voraces: dominan los casos subóptimos (barras amarillas).

---

## 4. Comparación de eficiencia en tiempo

###  Conclusión
- **Algoritmos Voraces:** Más rápidos (~0,002 ms a 0,032 ms), pero no exactos.  
- **Branch and Bound:** Algoritmo exacto más eficiente, tiempo medio 0,013 ms.  
- **Backtracking:** Algoritmo exacto más lento, tiempo medio 0,348 ms.  

> La técnica de Ramificación y Poda reduce el tiempo medio de ejecución en más de 25 veces respecto a Vuelta Atrás, demostrando una poda efectiva del árbol de búsqueda.

---

### b) Evidencias

| Algoritmo         | Tiempo máximo        | Tiempo medio       | Tiempo mínimo       |
|------------------|-------------------|-----------------|-----------------|
| backtracking      | 0,881 ms           | 0,348 ms        | 0,163 ms        |
| branchAndBound    | 0,071 ms           | 0,013 ms        | 0,003 ms        |
| greedyPorOrden    | 0,013 ms           | 0,002 ms        | 0,000 ms        |
| greedyPorValor    | 0,246 ms           | 0,032 ms        | 0,010 ms        |

 La gran diferencia entre los tiempos medios de `backtracking` (0,348 ms) y `branchAndBound` (0,013 ms) evidencia la mejora de eficiencia lograda por la Ramificación y Poda.

---

## 5. Conclusiones
- La práctica permitió contrastar teoría con implementación.  
- `backtracking` y `branchAndBound` son exactos (100 % óptimos).  
- La eficiencia de **Branch and Bound** fue notablemente superior.  
- La mejora de eficiencia se debe a **una generación de sucesores optimizada**, no únicamente a la cota rigurosa, subrayando la importancia de un diseño coherente y completo del algoritmo.
## 6. Uso de la IA
Se ha utlilizado la ia para mejorar la redacción del informe y la creación del árbol de forma que sea más fácil entenderlo.
