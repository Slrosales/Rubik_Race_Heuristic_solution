
<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Logo_uninorte_colombia.jpg/1200px-Logo_uninorte_colombia.jpg" width=90%>

# Evaluación y Aplicación de la Heurística de Manhattan en la Búsqueda A*

**Autores**

*   Laura Sofía Gómez Rosales  [![GitHub Slrosales](https://img.shields.io/badge/by-Slrosales-purple)](https://github.com/Slrosales)
*   Juan David Maestre Ramírez [![GitHub JuandiGo1](https://img.shields.io/badge/by-JuandiGo1-red)](https://github.com/JuandiGo1)
*   Fernando Mateo Valencia Gómez [![GitHub jfbenitezz](https://img.shields.io/badge/by-FernandoMVG-green)]((https://github.com/FernandoMVG))

# Metodología Implementada

El código se estructura en varias secciones funcionales para abordar la resolución del *Rubik's Race* de manera modular y eficiente. A continuación, se describe la metodología paso a paso:

1.  **Representación del Estado:**

    *   **Cadenas de Texto en lugar de Matrices:** Una decisión clave en este código es representar el estado del tablero de *Rubik's Race* como una **cadena de texto** en lugar de una matriz bidimensional. Esta elección se fundamenta en la **eficiencia de memoria** y la **simplicidad en la manipulación** de los estados.

        *   **Eficiencia de Memoria:** Las cadenas de texto son más ligeras en términos de memoria en comparación con las matrices, especialmente cuando se manejan grandes cantidades de estados durante la búsqueda. Esto es crucial en algoritmos de búsqueda como A* que pueden explorar un espacio de estados considerable.

        *   **Manipulación Simplificada para Movimientos:** Las operaciones de movimiento (arriba, abajo, izquierda, derecha) se implementan de forma directa manipulando los índices de la cadena. Encontrar la posición de la casilla vacía ('\*') y realizar el intercambio con casillas adyacentes se convierte en una manipulación de índices dentro de la cadena, lo que simplifica la lógica de los operadores de movimiento.

    *   **Formato de la Cadena:** Para el estado inicial (tablero de 5x5), se utiliza una cadena de 25 caracteres. Para el estado meta (centro 3x3), se utiliza una cadena de 9 caracteres. Cada carácter en la cadena representa una casilla del tablero, utilizando los caracteres '*BRVNAZ' para denotar la casilla vacía y las fichas de colores (Blanco, Rojo, Verde, Naranja, Amarillo, Azul, respectivamente).

2.  **Lectura y Validación de Archivos de Entrada:**

    *   **leer\_estado(nombre\_archivo):** Esta función se encarga de leer el estado del puzzle desde un archivo de texto. Lee cada línea del archivo, elimina espacios en blanco y concatena las líneas para formar una cadena representando el estado.
    *   **validar\_archivo\_inicial(estado) y validar\_archivo\_meta(estado):** Estas funciones validan que los archivos de entrada (inicial y meta) cumplan con los siguientes criterios:

        *   **Longitud Correcta:** El estado inicial debe tener 25 caracteres y el estado meta 9 caracteres.
        *   **Carácter Vacío ('\*'):** El estado inicial debe contener el carácter '\*' que representa la casilla vacía.
        *   **Caracteres Válidos:** Ambos estados deben contener únicamente los caracteres válidos definidos ('\*BRVNAZ').
        *   **Cantidad de Colores en el Estado Inicial:** El estado inicial debe contener exactamente 4 caracteres de cada color ('BRVNAZ') y un carácter '\*'. El estado meta no tiene restricciones estrictas en la cantidad de colores, pero se valida que no contenga más de 4 de cada color.

    *   La validación de los archivos de entrada asegura que el programa trabaje con estados correctamente formateados, evitando errores durante la ejecución del algoritmo de búsqueda.

3.  **Operadores de Movimiento:**

    *   **encontrar\_casilla\_vacia(estado):** Localiza la posición de la casilla vacía ('\*') en el estado actual y retorna sus coordenadas (fila, columna).
    *   **mover\_arriba(estado), mover\_abajo(estado), mover\_izquierda(estado), mover\_derecha(estado):** Estas funciones implementan los cuatro posibles movimientos en el juego. Cada función:

        *   Verifica si el movimiento es válido (por ejemplo, no se puede mover hacia arriba si la casilla vacía ya está en la primera fila).
        *   Calcula el nuevo índice de la casilla vacía después del movimiento.
        *   Convierte la cadena de estado a una lista para poder modificar los caracteres (las cadenas en Python son inmutables).
        *   Intercambia la posición de la casilla vacía con la casilla adyacente correspondiente al movimiento.
        *   Reconvierte la lista a una cadena y retorna el nuevo estado. Si el movimiento no es válido, retorna `None`.

    *   **movimiento(estado):** Genera una lista de los números de movimiento (1, 2, 3, 4) que son válidos para el estado actual.
    *   **sucesor(estado, movimiento\_numero):** Aplica el movimiento especificado (1-4) al estado actual y retorna el estado resultante.

4.  **Árbol de Solución y Algoritmo A*:**

    *   **Clase Nodo:** Representa un nodo en el árbol de búsqueda. Cada nodo contiene:

        *   `estado`: El estado del tablero.
        *   `costo_g`: El costo del camino desde el estado inicial hasta este nodo (profundidad del nodo).
        *   `costo_h`: El valor heurístico estimado para alcanzar el estado meta desde este nodo.
        *   `costo_f`: El costo total, calculado como `costo_g + costo_h`.
        *   `padre`: El nodo padre en el camino de búsqueda.
        *   `movimiento`: El número de movimiento que llevó a este estado desde el estado padre.

        El método `__lt__` permite comparar nodos en la cola de prioridad, priorizando nodos con menor `costo_f` y luego menor `costo_h` en caso de empate en `costo_f`.

    *   **meta(estado\_actual, estado\_meta):** Verifica si el centro 3x3 del `estado_actual` coincide con el `estado_meta`, determinando si se ha alcanzado el estado objetivo.
    *   **reconstruir\_camino(nodo\_meta):** Reconstruye el camino de la solución desde el nodo meta hasta el nodo inicial, retrocediendo a través de los punteros padre de cada nodo. Retorna una lista de strings, donde cada string representa un paso del camino, incluyendo información sobre la heurística, el movimiento realizado y el estado del tablero.
    *   **imprimir\_tablero(estado):** Formatea un estado de cadena en una representación visual de tablero de 5x5 (o 3x3 para el meta) para facilitar la lectura de los estados en el camino de solución.
    *   **a\_estrella(estado\_inicial, estado\_meta, heuristica=heuristica\_filas\_completas, archivo\_salida="salida.txt"):** Implementa el algoritmo A*.

        *   **Cola de Prioridad (PriorityQueue):** Utiliza una `PriorityQueue` para gestionar los nodos a explorar, ordenada por el costo `costo_f`.
        *   **Conjunto de Estados Visitados (estados\_visitados):** Mantiene un registro de los estados ya visitados para evitar ciclos y redundancia en la búsqueda.
        *   **Bucle Principal:**
            *   Extrae el nodo de menor costo de la cola de prioridad.
            *   Verifica si el estado del nodo es el estado meta. Si es así, reconstruye el camino, lo guarda en el archivo de salida y retorna `True` (solución encontrada).
            *   Genera los posibles movimientos válidos desde el estado actual.
            *   Para cada movimiento válido:
                *   Crea un nuevo estado sucesor.
                *   Verifica si el nuevo estado no ha sido visitado.
                *   Calcula el costo `costo_g` (incrementado en 1 por cada movimiento) y el costo heurístico `costo_h` para el nuevo estado.
                *   Crea un nuevo nodo con el nuevo estado, costos, nodo padre y movimiento.
                *   Añade el nuevo nodo a la cola de prioridad y marca el nuevo estado como visitado.
        *   Si la cola de prioridad se vacía y no se encuentra una solución, escribe "No se encontro una solucion." en el archivo de salida y retorna `False`.

5.  **Funciones Heurísticas:**

    *   **heuristica\_manhattan(estado\_actual, estado\_meta):** Calcula la heurística de Manhattan. Para cada ficha en el centro 3x3 del `estado_meta`, calcula la distancia Manhattan a su posición más cercana en el centro 3x3 del `estado_actual`. Suma estas distancias para obtener el valor heurístico.
    *   **heuristica\_filas\_completas(estado\_actual, estado\_meta):** Implementa una heurística combinada que utiliza la heurística de Manhattan y añade una bonificación por filas completas en el centro 3x3 que coinciden con el estado meta. Resta una bonificación (2 puntos por fila completa) a la distancia de Manhattan. Esta heurística busca guiar la búsqueda hacia estados que no solo minimicen la distancia de Manhattan, sino que también tengan una estructura más cercana a la solución final al completar filas.

# Evaluación y Aplicación de la Heurística de Manhattan en la Búsqueda A*

## 1. Introducción

La heurística juega un papel fundamental en la búsqueda A-estrella, ya que permite guiar el algoritmo hacia soluciones óptimas con menor esfuerzo computacional. En este documento, se describe la implementación de la heurística de Manhattan y su extensión con una bonificación por filas completas, aplicada al problema de resolución del juego *Rubik’s Race*.

## 2. Heurística de Manhattan

La heurística de Manhattan estima el costo mínimo requerido para alcanzar el estado meta desde el estado actual. Para cada ficha en el centro 3x3 del tablero, se calcula la distancia Manhattan, definida como:

$$ d_{Manhattan}((r_1, c_1), (r_2, c_2)) = |r_1 - r_2| + |c_1 - c_2| $$

donde $(r_1, c_1)$ representa la posición actual de la ficha y $(r_2, c_2)$ su posición en el estado meta.

### 2.1. Cálculo de la heurística

La función heurística $h_{Manhattan}$ se define como la suma de todas las distancias Manhattan de las fichas en el centro del tablero:

$$ h_{Manhattan}(estado) = \sum_{i=0}^{N} (|fila_{actual}(i) - fila_{meta}(i)| + |columna_{actual}(i) - columna_{meta}(i)|) $$

donde:

*   $N = 8$, el número de fichas en el área de interés (excluyendo la casilla vacía).
*   $fila_{actual}(i)$ y $columna_{actual}(i)$ son las coordenadas actuales de la ficha $i$.
*   $fila_{meta}(i)$ y $columna_{meta}(i)$ son las coordenadas objetivo de la ficha $i$.
*   $|x|$ denota el valor absoluto de $x$.

### 2.2. Propiedades

*   **Admisibilidad:** Esta heurística es admisible porque nunca sobreestima el costo real del camino óptimo.
*   **Consistencia:** Dado que cada movimiento en el juego tiene un costo uniforme y la heurística siempre subestima o iguala el costo real, se mantiene la propiedad de consistencia.

## 3. Heurística Combinada: Manhattan con Bonificación por Filas Completas

Para mejorar la eficiencia de la búsqueda, se introduce una heurística combinada que incorpora un término de bonificación por filas completamente resueltas. La idea es fomentar estados intermedios que no solo minimicen la distancia Manhattan, sino que también tengan una estructura más cercana a la solución final.

### 3.1. Fórmula de la heurística combinada

La heurística combinada se define como:

$$ h_{Combinada}(estado) = h_{Manhattan}(estado) - \alpha \cdot \sum_{i=0}^{2} \mathbb{I} (\text{fila}_{\text{actual},i} == \text{fila}_{\text{meta},i}) $$

donde:

*   $h_{Manhattan}(estado)$ es la heurística de Manhattan.
*   $\mathbb{I}(\cdot)$ es la función indicadora, que devuelve 1 si la condición es verdadera y 0 en caso contrario.
*   $\text{fila}_{\text{actual},i}$ representa la $i$-ésima fila del centro 3x3 en el estado actual.
*   $\text{fila}_{\text{meta},i}$ representa la $i$-ésima fila del centro 3x3 en el estado meta.
*   $\alpha$ es un factor de ponderación ajustable.

Esta función descuenta una bonificación por cada fila del centro que coincida exactamente con el estado meta.

### 3.2. Justificación

Esta extensión mejora la guía de la búsqueda al reducir la cantidad de estados explorados innecesariamente. Sin embargo, un valor excesivo de $\alpha$ puede hacer que la heurística se vuelva no admisible, lo que podría comprometer la optimalidad de A*.
