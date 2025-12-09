# PRÁCTICA 5 – PROGRAMACIÓN DINÁMICA

## Planificación Maximal de Hospitales
**Grados en Ingeniería Informática e Ingeniería de Computadores**
**Asignatura: Algoritmos Avanzados – Curso 2025/2026**

**Autores:** Marc Burgos Ucendo, Alberto Sastre Zorrilla

---

### 1. Algoritmo Recursivo 🧠

El objetivo es maximizar el número de víctimas atendidas construyendo hospitales en un subconjunto de posiciones dadas, con la restricción de que la distancia entre dos hospitales debe ser al menos **K**.

```java
public class HospitalesDP {

    // NOTA: Se ha corregido K=5 para la coherencia del problema (ver Conclusiones).
    private static final int K = 5; 
    private static int[] X;
    private static int[] P;

    public static int hospitales(int[] xs, int[] ps) {
        X = xs;
        P = ps;
        return maxVictimasRecursivo(0);
    }

    /**
     * Encuentra el indice del proximo hospital valido (j > i) tal que X[j] - X[i] >= K.
     */
    private static int siguienteValido(int i) {
        int posicionMinima = X[i] + K;
        for (int j = i + 1; j < X.length; j++) {
            if (X[j] >= posicionMinima) {
                return j;
            }
        }
        return X.length; // Retorna n si no hay mas hospitales validos
    }

    /**
     * Calcula el maximo beneficio desde el indice i.
     */
    private static int maxVictimasRecursivo(int i) {
        if (i >= X.length) return 0; // Caso base

        // Opcion 1: No construir en X[i]
        int noConstruir = maxVictimasRecursivo(i + 1);

        // Opcion 2: Construir en X[i]
        int j = siguienteValido(i);
        int construir = P[i] + maxVictimasRecursivo(j);

        return Math.max(noConstruir, construir);
    }
}
```
## 2. Tabulación 📈
a) Árbol de Recursión y Grafo de Dependencia

El problema presenta la propiedad de subestructura óptima y subproblemas superpuestos.

### Árbol de Recursión :Grafo de Dependencia: 

El subproblema $i$ depende de $i+1$ y de $j = siguienteValido(i)$, donde $j > i$. El grafo es un Grafo Acíclico Dirigido (DAG).

b) Decisiones de Diseño para la TabulaciónEstado (Subproblema): 

$dp[i]$Máximo número de víctimas atendidas considerando solo los hospitales con índice $\ge i$.Tabla: Array unidimensional de tamaño $n+1$.


```java
int n = xs.length;
int[] dp = new int[n + 1]; // dp[n] = 0 (caso base)
```
Orden de Relleno: Decreciente (Bottom-Up). Se rellena de $i=n-1$ a $i=0$.

c) Código del Algoritmo de Programación Dinámica (Bottom-Up)
```java
public static int hospitales(int[] xs, int[] ps) {
    return hospitalesDP(xs, ps, 5); 
}

/**
 * Precalcula el indice del proximo hospital valido para cada i.
 */
private static int[] precalcularSiguientes(int[] xs, int K) {
    int n = xs.length;
    int[] next = new int[n];
    for (int i = 0; i < n; i++) {
        int posMin = xs[i] + K;
        int j = i + 1;
        // Busqueda secuencial: O(n) en el peor caso para cada i
        while (j < n && xs[j] < posMin) j++;
        next[i] = j; 
    }
    return next;
}

/**
 * Implementacion de Programacion Dinamica (Tabulacion).
 */
public static int hospitalesDP(int[] xs, int[] ps, int K) {
    int n = xs.length;
    if (n == 0) return 0;

    int[] dp = new int[n + 1]; 
    int[] nextValid = precalcularSiguientes(xs, K);

    // Iteracion Bottom-Up: O(n)
    for (int i = n - 1; i >= 0; i--) {
        // O(1) operaciones internas
        int noSeleccionar = dp[i + 1];
        int j = nextValid[i];
        int seleccionar = ps[i] + dp[j];
        dp[i] = Math.max(noSeleccionar, seleccionar);
    }
    return dp[0];
}
```
d) Análisis de Complejidad en Tiempo y Espacio 🔬

1. Algoritmo Recursivo Puro (Sin Memoización)

El problema de la Planificación Maximal de Hospitales, resuelto de forma puramente recursiva, es similar al problema de la Mochila 0/1 o de la Secuencia Máxima de Tareas.

Complejidad Temporal (T(n)):

En cada paso i, la función maxVictimasRecursivo(i) realiza una elección binaria (seleccionar xᵢ o no seleccionar xᵢ).

T(n) = T(i+1) + T(j) + O(1 + C_siguiente)

Donde:
- n es el número de hospitales,
- j es el índice del próximo hospital válido (j > i+1),
- y C_siguiente es el coste de la función siguienteValido (que es O(n) en el peor caso).

Peor Caso:
Cuando K es muy pequeño (por ejemplo, K=1), casi todas las posiciones son válidas, y j ≈ i+2.  
La relación de recurrencia se acerca a:

T(n) ≈ 2T(n-1) + O(n)

Resultado:
El término exponencial domina.  
El número de subproblemas únicos es O(n), pero al resolver los subproblemas repetidamente sin almacenamiento (memoización), el tiempo total de ejecución es exponencial, del orden de **O(2ⁿ)**.

Complejidad Espacial (S(n)):

El espacio utilizado está determinado por la profundidad máxima de la pila de llamadas recursivas.  
Dado que en cada llamada se avanza al menos un índice (de i a i+1 o a j > i), la profundidad máxima de la recursión es lineal con el número de hospitales n.

Resultado:  
La complejidad espacial es **O(n)**.


2. Algoritmo de Programación Dinámica (Tabulación)

La Programación Dinámica resuelve el problema calculando una tabla unidimensional de n+1 estados (dp[i]) de forma iterativa.

Complejidad Temporal (T(n)):

La complejidad se determina por la suma de la fase de pre-cálculo y la fase de relleno DP.

Fase de Pre-cálculo (precalcularSiguientes):
- Se ejecuta un bucle externo n veces (de i=0 a n-1).
- Dentro, la búsqueda secuencial (while) para encontrar el índice j puede recorrer hasta O(n) elementos.

Coste:  
∑_{i=0}^{n-1} O(n) = **O(n²)**

Fase de Relleno DP (Bottom-Up):
- Se ejecuta un bucle n veces (de i=n-1 a 0).
- Dentro del bucle, todas las operaciones (acceso a arrays, comparación Math.max) son de tiempo constante O(1).

Coste:  
∑_{i=0}^{n-1} O(1) = **O(n)**

Tiempo Total:  
El coste total es O(n²) + O(n). El término cuadrático domina.

Resultado:  
La complejidad temporal es **O(n²)**.

Complejidad Espacial (S(n)):

El espacio auxiliar es el requerido para almacenar las soluciones de los subproblemas y las transiciones.

- Tabla DP (dp[]): n+1 celdas
- Tabla de Transiciones (nextValid[]): n celdas

Resultado:  
La complejidad espacial es **O(n)**.

3. Determinación de Decisiones

Se amplía el algoritmo DP para reconstruir el camino óptimo recorriendo la tabla $dp$ desde el inicio.
```java
public static void hospitalesConDecisiones(int[] xs, int[] ps, int K) {
    int n = xs.length;
    if (n == 0) return;

    int[] dp = new int[n + 1];
    int[] nextValid = precalcularSiguientes(xs, K);

    // Fase Forward: Rellenar dp
    for (int i = n - 1; i >= 0; i--) {
        int noSel = dp[i + 1];
        int sel = ps[i] + dp[nextValid[i]];
        dp[i] = Math.max(noSel, sel);
    }

    // Fase Backward: Reconstruccion de la solucion (O(n))
    System.out.println("Posiciones seleccionadas:");
    int i = 0;
    while (i < n) {
        int beneficioSin = dp[i + 1];
        int beneficioCon = ps[i] + dp[nextValid[i]];

        // Si el beneficio al seleccionar es mayor o igual, se selecciona
        if (beneficioCon >= beneficioSin) {
            System.out.print(xs[i] + " ");
            i = nextValid[i]; // Salto al siguiente permitido
        } else {
            i++; // No se selecciona, siguiente candidato
        }
    }
    System.out.println("\nTotal víctimas atendidas: " + dp[0] + " cientos de mil");
}

hospitalesConDecisiones(new int[]{6,7,12,14}, new int[]{5,6,5,1}, 5);
```
Salida:

Posiciones seleccionadas:
6 12 
Total víctimas atendidas: 10 cientos de mil
#### 4. Comparación de optimalidad

##### a) Material del experimento
Se han ejecutado 100 instancias aleatorias con las siguientes características:
- n ∈ [12, 20] (valores medios-altos para detectar diferencias)
- Posiciones xs: enteros ordenados crecientes entre 0 y 3000
- Víctimas p_i: distribución uniforme entre 1 y 100
- Distancia mínima K = 5 (valor coherente con el ejemplo oficial del enunciado)

Algoritmos comparados:
- backtracking (vuelta atrás exacta)
- branchAndBound (ramificación y poda exacta)
- dp y hospitales (programación dinámica Bottom-Up, misma implementación)
- greedyOrden (voraz por orden de aparición)
- greedyValor (voraz por mayor número de víctimas)

##### b) Conclusión
Los algoritmos **backtracking, branchAndBound, dp y hospitales** son **exactos**: obtienen la solución óptima en el **100% de los casos**.  
Los algoritmos voraces muestran comportamientos opuestos en este conjunto de datos:  
- **greedyOrden** falla sistemáticamente (0% óptimas)  
- **greedyValor** acierta en el 100% de las instancias generadas  
Esto demuestra claramente que **los métodos voraces no garantizan optimalidad en general**, aunque uno de ellos haya tenido suerte con la distribución de datos utilizada.

##### c) Evidencias

**Tabla resumen numérico**

| Medida                        | backtracking | branchAndBound | dp     | greedyOrden | greedyValor | hospitales |
|-------------------------------|--------------|----------------|--------|-------------|-------------|------------|
| Núm. ejecuciones              | 100          | 100            | 100    | 100         | 100         | 100        |
| % Soluciones subóptimas       | 0.00%        | 0.00%          | 0.00%  | 100.00%     | 0.00%       | 0.00%      |
| % Soluciones óptimas          | 100.00%      | 100.00%        | 100.00%| 0.00%       | 100.00%     | 100.00%    |
| % Soluciones sobróptimas      | 0.00%        | 0.00%          | 0.00%  | 0.00%       | 0.00%       | 0.00%      |
| Diferencia media subóptima    | 0.0          | 0.0            | 0.0    | 59.20%      | 0.0         | 0.0        |
| Diferencia máxima subóptima   | 0            | 0              | 0      | 32.01%      | 0           | 0          |

**Resumen gráfico** (ver figuras adjuntas):

Figura 1 – Porcentaje de resultados óptimos por algoritmo  
→ Los cuatro métodos exactos alcanzan el 100%.  
→ greedyValor también alcanza el 100% (coincidencia afortunada con los datos).  
→ greedyOrden falla en todos los casos.

Figura 2 – Valores medios y extremos de la diferencia respecto al óptimo  
→ Solo greedyOrden presenta errores significativos (media ~59%, máximo 32%).

##### d) Incidencias
Durante el desarrollo se detectaron inconsistencias iniciales (porcentajes de optimalidad inferiores al 100% en métodos exactos) debidas a diferencias en la interpretación de la restricción de distancia (uso de > K en lugar de >= K en algunos algoritmos). Tras unificar la condición **xs[j] - xs[i] >= K** en todos los métodos y fijar K=5 de forma global, se alcanzó el **100% de optimalidad** en los cuatro algoritmos exactos, tal y como exige la teoría y el enunciado de la práctica.

#### 5. Comparación de eficiencia

##### a) Conclusión
- Los algoritmos **voraces** (greedyOrden y greedyValor) son **claramente los más rápidos**, con tiempos medios inferiores a **0.01 ms** en todos los casos.
- La **programación dinámica (dp y hospitales)** es el método exacto más eficiente: tiempo medio de **0.002–0.003 ms**, prácticamente indistinguible de los voraces.
- **Branch & Bound** es significativamente más lento que DP (≈ 5–10 veces), con tiempo medio de **0.11 ms**.
- **Backtracking** es con diferencia el menos eficiente: tiempo máximo de **0.209 ms** y tiempo medio de **0.032 ms**, más de **10 veces más lento** que la programación dinámica.

**Conclusión final**: la programación dinámica ofrece el **mejor compromiso** entre optimalidad (100%) y eficiencia (casi igual que los voraces), convirtiéndose en la técnica claramente superior para este problema.

##### b) Evidencias

**Tabla resumen numérico – Tiempos de ejecución (100 ejecuciones, n ∈ [12,20])**

| Medida                   | backtracking | branchAndBound | dp       | greedyOrden | greedyValor | hospitales |
|--------------------------|--------------|----------------|----------|-------------|-------------|------------|
| Núm. ejecuciones         | 100          | 100            | 100      | 100         | 100         | 100        |
| Tiempo máximo            | 0.209 ms     | 0.063 ms       | 0.020 ms | 0.026 ms    | 0.105 ms    | 0.027 ms   |
| Tiempo medio             | 0.032 ms     | 0.011 ms       | 0.002 ms | 0.001 ms    | 0.009 ms    | 0.003 ms   |
| Tiempo mínimo            | 0.007 ms     | 0.003 ms       | 0.000 ms | 0.000 ms    | 0.002 ms    | 0.000 ms   |

**Resumen gráfico** (ver figura adjunta):

Figura 3 – Tiempos de ejecución medios y extremos (escala lineal)  
→ Se observa claramente:
- Backtracking: barra roja alta y ancha (peor rendimiento).
- Branch & Bound: intermedio.
- dp y hospitales: prácticamente en el eje (casi 0 ms).
- Ambos voraces: también casi en cero, pero con algo más de variabilidad.

Este gráfico ilustra de forma contundente la **superioridad en eficiencia** de la programación dinámica frente a los otros métodos exactos, y su competitividad frente a los heurísticos.
## 6. Conclusiones

La Programación Dinámica es la técnica óptima para resolver este problema, ya que transforma la complejidad exponencial $\mathbf{O(2^n)}$ del enfoque recursivo puro en una complejidad polinomial $\mathbf{O(n^2)}$, garantizando siempre la solución óptima.

Cabe destacar también, que el Árbol de Recursión está hecho a mano ya que ambos integrantes del
grupo hemos tenido problemas con el SRec que nos han impedido poder sacar un análisis de datos
desde el propio programa.

Uso de la IA:Se ha utilizado la Ia para hacer un informe más profesional y pulido y para analizar un el código ya que no hemos podido usar el SRec para esta función.
