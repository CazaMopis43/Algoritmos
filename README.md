# Algoritmos Avanzados
## Práctica 1
## Authors
- Alfonso Rodríguez Gutt.
- Marc Burgos Ucendo.
## Objetivos 
Para esta primera práctica nuestro objetivo es familiarizarnos con el uso de los Algoritmos Voraces. Para ello se nos ha propuesto relizar un algoritmo que nos resuelva horarios para que no colisionen dados unos tiempos de Inicio y Fin de las tareas.
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
