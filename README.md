# Algoritmos Avanzados
## Práctica 1
## Table of Contents
- [Objetivos](#objetivos)
- [Algoritmo Idealizado](#algoritmo-idealizado)
- [Algoritmo Realista](#algoritmo-realista)
- [Complejidad](#complejidad)
- [Uso de IA](#uso-de-la-IA)
- [Conclusiones](#conclusiones)
## Autores
- Alberto Sastre Zorrilla.
- Marc Burgos Ucendo.
## Objetivos 
Para esta primera práctica nuestro objetivo es familiarizarnos con el uso de los Algoritmos Voraces. Para ello se nos ha propuesto realizar un algoritmo que nos resuelva horarios para que no colisionen dados unos tiempos de Inicio y Fin de las tareas.
## Algoritmo Idealizado
En este caso se nos dice que debemos suponer que los tiempos de las actividades se nos dan ya ordenados, de esta forma podemos realizar la tarea más facilmente. Para poder resolver el ejercicio solo deberemos crear el array de booleanos que deberemos devolver como respuesta y seleccionar el primer elemento. A continuación el bucle servirá para recorrer la lista, este tendrá una condición para asegurarse de que la siguiente accion seleccionada(en nuestro caso la acción j) es factible. Si lo es pondremos a True la posición j de la solución y seleccionaremos el elemento j. En caso contrario se marcará a False y pasaremos a la siguiente.
````java
public static boolean[] seleccionarActividadesIdeal(int[] c, int[] f) {
        boolean[] seleccionadas = new boolean[c.length];
        seleccionadas[0] = true; 
        int ultimaSeleccionada = 0;

        for (int j = 1; j < c.length; j++) {
            if (c[j] >= f[ultimaSeleccionada]) { 
                seleccionadas[j] = true;
                ultimaSeleccionada = j;
            } else {
                seleccionadas[j] = false;
            }
        }
        return seleccionadas;
    }
````
## Algoritmo Realista
Como en la vida misma las cosas nunca suceden como queremos, por tanto en esta versión del algoritmo no tendremos los tiempos ordenados, esto será un inconveniente que resolveremos con un método auxiliar. Este método nos ayudará a ordenar las actividades usando los índices, primero se creará un array con los índices , posteriormente se utiliza un insertion sort para ordenar el array. El resto del código es idéntico al anterior , ya que se ha solucionado el problema que los diferencia con la llamada al método.
````java
public static boolean[] seleccionarActividadesRealista(int[] c, int[] f) {
    int[] indices = ordenar(f);

    boolean[] seleccionadas = new boolean[c.length];
    seleccionadas[indices[0]] = true;
    int ultimaSeleccionada = indices[0];

    for (int i = 1; i < indices.length; i++) {
        int indiceActual = indices[i];
        if (c[indiceActual] >= f[ultimaSeleccionada]) {
            seleccionadas[indiceActual] = true;
            ultimaSeleccionada = indiceActual;
        } else {
            seleccionadas[indiceActual] = false;
        }
    }

    return seleccionadas;
}

private static int[] ordenar(int[] arr) {
    int[] indices = new int[arr.length];
    for (int i = 0; i < arr.length; i++) {
        indices[i] = i;
    }
    for (int i = 1; i < arr.length; i++) {
        int j = i;
        int temp = indices[i];
        while (j > 0 && arr[indices[j - 1]] > arr[temp]) {
            indices[j] = indices[j - 1];
            j--;
        }
        indices[j] = temp;
    }
    return indices;
}
````

## Complejidad
### Algoritmo idealizado (seleccionarActividadesIdeal).
Se supone que las actividades ya vienen ordenadas en orden creciente  de fin.
La reserva de array (boolean[] seleccionadas = new boolean[n]) tiene un coste de O(n).
Las asignaciones (seleccionadas[0] = true y ultimaSeleccionada = 0) tienen un coste de O(1).
El bucle for (for (j = 1; j < n; j++)) al tener dentro asignaciones simples como ifs, tiene una complejidad de O(n).

Siendo inicio.length = n, 
n/j=1 Σ1=n->O(n) esto nos da la complejidad del bucle for.
Y sumando las complejidades del programa nos da: O(n)+O(1) = Complejidad O(n)
![Algoritmo idealizado](https://github.com/CazaMopis43/Algoritmos/blob/main/Ideal.jpg)
### Algoritmo realista (seleccionarActividadesRealista)
Primero se llama al subprograma 'ordenar(f)'.
Se crea un array booleano (boolean[] seleccionadas = new boolean[c.length];) que tiene una complejidad de O(n).
Las asignaciones lineales como (seleccionadas[indices[0]] = true;) tienen una complejidad de O(1). 
El bucle for (for (i = 1; i < indices.length; i++)) contiene dentro de sí, comparaciones y asignaciones, que tienen una complejidad de O(1).
El subprograma 'ordenar' tiene una inicialización O(1) y un bucle for que tiene una complejidad de O(n).
El bucle principal de inserción contiene un bucle for que llega hasta n-1 iteraciones y en su interior anidado un bucle while. 

La fórmula para calcular la suma de los primeros n-1 enteros es:
T = n-1/i=1 Σi = n(n-1)/2 = (n^2-n)/2 y nos quedamos con el número más grande que es n^2. Por lo tanto al sumar las complejidades del programa al completo sacamos que O(n)+O(1)+O(n^2) = Complejidad O(n^2).
![Algoritmo Realista](https://github.com/CazaMopis43/Algoritmos/blob/main/Real.jpg)
## Uso de la IA
Para esta práctica hemos usado la Ia como consultora, especialmente para que nos ayudase a entender el problema de la ordenación y nos ha ayudado a hacer el Informe más académico.
## Conclusiones
La realización de esta práctica ha permitido profundizar en la técnica de diseño de algoritmos voraces, en particular aplicada al problema clásico de selección de actividades. A través de la implementación de las dos versiones del algoritmo idealizada y realista se ha puesto de manifiesto la importancia que tiene el orden de los datos de entrada en la eficacia y corrección de la solución. Ya que en términos de complejidad, se ha podido comprobar que la fase de ordenación domina el coste total del algoritmo realista, manteniéndose en un orden de eficiencia aceptable para problemas de tamaño grande.
