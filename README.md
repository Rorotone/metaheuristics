# Comparación experimental de algoritmos exactos y metaheurísticos para el ATSP

## Resumen

Este repositorio presenta una implementación y evaluación experimental de cinco métodos de optimización aplicados al **Problema del Viajante Asimétrico** (*Asymmetric Traveling Salesman Problem*, ATSP):

1. Branch and Bound.
2. Tabu Search.
3. Ant Colony Optimization.
4. Genetic Algorithm.
5. Simulated Annealing.

El objetivo es estudiar el comportamiento de enfoques exactos y metaheurísticos frente a instancias de **10, 20, 50 y 100 ciudades**, considerando calidad de solución, mejora respecto de una solución inicial, tiempo de ejecución, escalabilidad y distribución de los costos dentro de la mejor ruta encontrada.

Cada experimento registra las semillas utilizadas, los parámetros principales, la mejor ruta obtenida y una representación tipo **carta Gantt**, donde cada barra corresponde a un desplazamiento y su longitud representa el costo acumulado de la ruta.

> **Advertencia metodológica:** en los resultados actualmente reportados, cada algoritmo utilizó semillas de instancia diferentes. Por lo tanto, los costos absolutos no deben interpretarse como una comparación directa entre algoritmos, ya que cada método resolvió una matriz ATSP distinta. Los resultados sí permiten estudiar el comportamiento interno y la escalabilidad de cada implementación. Para una comparación concluyente, todos los algoritmos deben ejecutarse sobre las mismas instancias.

---

## 1. Descripción del problema

Sea un conjunto de ciudades:

\[
V = \{0,1,\dots,n-1\}
\]

y una matriz dirigida de costos:

\[
D = [d_{ij}]
\]

donde \(d_{ij}\) representa el costo de desplazarse desde la ciudad \(i\) hasta la ciudad \(j\).

En el ATSP no se exige simetría, por lo que:

\[
d_{ij} \neq d_{ji}
\]

Una solución es una permutación de las ciudades:

\[
\pi = (\pi_0,\pi_1,\dots,\pi_{n-1})
\]

que representa un ciclo hamiltoniano. Su costo se calcula como:

\[
f(\pi)=
\sum_{k=0}^{n-2} d_{\pi_k,\pi_{k+1}}
+
d_{\pi_{n-1},\pi_0}
\]

El objetivo es encontrar la ruta de costo mínimo:

\[
\pi^\star = \arg\min_{\pi} f(\pi)
\]

El TSP y su variante asimétrica pertenecen a la clase de problemas de optimización combinatoria NP-hard. El número de rutas posibles crece factorialmente, lo que vuelve impracticable una enumeración exhaustiva para instancias grandes.

---

## 2. Objetivos

### Objetivo general

Comparar el comportamiento de un algoritmo exacto y cuatro metaheurísticas sobre instancias ATSP de distintos tamaños.

### Objetivos específicos

- Implementar los cinco algoritmos bajo una estructura experimental comparable.
- Evaluar instancias de 10, 20, 50 y 100 ciudades.
- Registrar las semillas utilizadas en cada ejecución.
- Medir costo inicial, mejor costo, porcentaje de mejora y tiempo de ejecución.
- Analizar la escalabilidad de cada método.
- Visualizar la estructura de la mejor ruta mediante una carta Gantt.
- Identificar tramos críticos o cuellos de botella dentro de cada solución.
- Distinguir entre soluciones óptimas certificadas y soluciones heurísticas.

---

## 3. Algoritmos implementados

### 3.1 Branch and Bound

Método exacto que explora implícitamente el espacio de soluciones mediante una estructura de búsqueda. Utiliza cotas para descartar ramas que no pueden mejorar la mejor solución conocida.

La implementación registra:

- Solución greedy inicial.
- Mejor costo encontrado.
- Cantidad de nodos explorados.
- Podas por cota.
- Podas por dominancia.
- Límite máximo de nodos.
- Estado de terminación.

El estado **óptimo** solo se utiliza cuando la búsqueda termina completamente y certifica que no existe una solución mejor. Cuando se alcanza el límite de nodos, el resultado debe describirse como la **mejor solución encontrada**, no como un óptimo global.

### 3.2 Tabu Search

Metaheurística de búsqueda local que utiliza memoria de corto plazo para evitar ciclos y reducir el retorno inmediato a soluciones previamente visitadas.

La implementación considera:

- Solución inicial.
- Vecindario basado en modificaciones de la ruta.
- Lista tabú.
- Aspiración.
- Número máximo de iteraciones.
- Número de vecinos evaluados por iteración.

### 3.3 Ant Colony Optimization

Metaheurística poblacional inspirada en el comportamiento colectivo de colonias de hormigas.

Cada hormiga construye una ruta probabilísticamente utilizando:

- Información heurística.
- Feromonas acumuladas.
- Evaporación de feromonas.
- Refuerzo de las mejores soluciones.

La implementación registra el número de hormigas, iteraciones y evolución del mejor costo.

### 3.4 Genetic Algorithm

Metaheurística evolutiva basada en una población de rutas.

Las operaciones principales son:

- Inicialización de la población.
- Evaluación mediante función de aptitud.
- Selección.
- Cruce compatible con permutaciones.
- Mutación.
- Elitismo.
- Reemplazo generacional.

Se registran por separado la semilla de población inicial y la semilla del algoritmo.

### 3.5 Simulated Annealing

Metaheurística inspirada en el proceso físico de enfriamiento de materiales.

El algoritmo puede aceptar temporalmente soluciones peores con probabilidad:

\[
P(\Delta,T)=e^{-\Delta/T}
\]

donde:

- \(\Delta\) es el aumento del costo.
- \(T\) es la temperatura actual.

La implementación registra:

- Solución aleatoria inicial.
- Solución greedy.
- Temperatura inicial estimada.
- Tasa de aceptación.
- Factor de enfriamiento.
- Número de iteraciones.
- Mejor costo encontrado.

---

## 4. Diseño experimental

### 4.1 Tamaños evaluados

```python
SIZES = [10, 20, 50, 100]
```

### 4.2 Generación de instancias

Cada instancia se genera a partir de coordenadas aleatorias y una matriz de costos dirigida. La asimetría provoca que el costo de ida pueda ser distinto del costo de regreso.

La semilla de instancia permite reconstruir exactamente:

- Las coordenadas.
- La matriz de costos.
- La dificultad específica del caso.

### 4.3 Semillas

Cada notebook genera y registra:

- `semilla_maestra`
- `semilla_instancia`
- `semilla_algoritmo`

Cuando corresponde, también se registran:

- `semilla_poblacion`
- `semilla_inicio`
- `semilla_temperatura`

Con una semilla maestra fija, el experimento completo puede reproducirse. Con:

```python
MASTER_SEED = None
```

se generan semillas distintas en cada ejecución.

### 4.4 Reproducibilidad

Para repetir una ejecución, debe copiarse el valor impreso de la semilla maestra:

```python
MASTER_SEED = 1417570801
```

No es suficiente guardar únicamente la mejor ruta. También deben conservarse:

- Semilla de la instancia.
- Semilla del algoritmo.
- Parámetros.
- Versión del código.
- Tamaño de la instancia.
- Criterio de detención.

---

## 5. Métricas evaluadas

### Calidad de solución

- Costo inicial.
- Costo greedy.
- Mejor costo.
- Mejora porcentual.

La mejora se calcula como:

\[
\text{mejora}(\%) =
\frac{\text{costo inicial}-\text{mejor costo}}
{\text{costo inicial}}
\times 100
\]

### Rendimiento computacional

- Tiempo de ejecución.
- Iteraciones.
- Generaciones.
- Número de hormigas.
- Vecinos evaluados.
- Nodos explorados.
- Podas.

### Distribución de la ruta

- Costo promedio por tramo.
- Tramo de mayor costo.
- Percentil 75.
- Porcentaje del costo total aportado por cada tramo.
- Concentración del costo en los tres tramos más caros.
- Coeficiente de variación.

---

## 6. Resultados generales

### 6.1 Branch and Bound

| n | Costo greedy | Mejor costo | Mejora | Nodos | Límite | Tiempo (s) | Estado |
|---:|---:|---:|---:|---:|---:|---:|---|
| 10 | 300.70 | 277.54 | 7.70% | 1,208 | 500,000 | 0.329 | Óptimo |
| 20 | 431.78 | 373.15 | 13.58% | 40,000 | 40,000 | 18.372 | Límite |
| 50 | 596.88 | 550.49 | 7.77% | 5,000 | 5,000 | 1.121 | Límite |
| 100 | 866.46 | 830.58 | 4.14% | 1,000 | 1,000 | 1.295 | Límite |

**Interpretación:** el algoritmo certificó el óptimo únicamente para \(n=10\). Los tiempos de \(n=50\) y \(n=100\) no indican una mejor escalabilidad real, porque la búsqueda se detuvo con límites de nodos considerablemente menores.

### 6.2 Tabu Search

| n | Costo inicial | Mejor costo | Mejora | Iteraciones | Vecinos/iteración | Tiempo (s) |
|---:|---:|---:|---:|---:|---:|---:|
| 10 | 404.28 | 293.74 | 27.34% | 120 | 36 | 0.543 |
| 20 | 564.88 | 402.85 | 28.68% | 160 | 171 | 3.397 |
| 50 | 633.24 | 610.45 | 3.60% | 200 | 400 | 4.787 |
| 100 | 891.00 | 816.57 | 8.35% | 250 | 600 | 7.639 |

**Interpretación:** presentó el crecimiento temporal más moderado y estable. La mejora se redujo en \(n=50\), lo que sugiere sensibilidad al vecindario, tenure y cantidad de iteraciones.

### 6.3 Ant Colony Optimization

| n | Costo greedy | Mejor costo | Mejora | Hormigas | Iteraciones | Tiempo (s) |
|---:|---:|---:|---:|---:|---:|---:|
| 10 | 371.78 | 277.35 | 25.40% | 20 | 80 | 1.481 |
| 20 | 471.94 | 389.76 | 17.41% | 30 | 90 | 6.033 |
| 50 | 735.26 | 563.91 | 23.31% | 40 | 100 | 42.195 |
| 100 | 857.14 | 799.57 | 6.72% | 45 | 110 | 64.627 |

**Interpretación:** obtuvo mejoras relevantes, especialmente para \(n=50\), pero presentó el mayor crecimiento temporal debido a la construcción de múltiples rutas por iteración.

### 6.4 Genetic Algorithm

| n | Mejor inicial | Mejor final | Mejora | Población | Generaciones | Tiempo (s) |
|---:|---:|---:|---:|---:|---:|---:|
| 10 | 362.34 | 275.45 | 23.98% | 80 | 140 | 8.289 |
| 20 | 362.01 | 351.35 | 2.94% | 120 | 220 | 17.977 |
| 50 | 662.65 | 608.97 | 8.10% | 180 | 320 | 22.905 |
| 100 | 980.25 | 776.22 | 20.81% | 220 | 400 | 48.135 |

**Interpretación:** logró una mejora importante en \(n=100\), aunque con un costo computacional superior a Tabu Search y Simulated Annealing.

### 6.5 Simulated Annealing

| n | Costo azar | Costo greedy | Mejor costo | Mejora vs. azar | Aceptación | Iteraciones | Tiempo (s) |
|---:|---:|---:|---:|---:|---:|---:|---:|
| 10 | 458.06 | 244.94 | 204.46 | 55.36% | 24.66% | 7,000 | 4.176 |
| 20 | 1,093.04 | 415.53 | 369.61 | 66.18% | 18.46% | 12,000 | 7.462 |
| 50 | 2,389.87 | 688.48 | 701.36 | 70.65% | 16.43% | 22,000 | 10.721 |
| 100 | 5,048.68 | 1,004.84 | 1,169.50 | 76.84% | 16.99% | 35,000 | 11.467 |

**Interpretación:** la mejora frente a una ruta aleatoria es elevada, pero para \(n=50\) y \(n=100\) la solución final fue peor que la greedy. Por ello, la mejora frente al azar no debe utilizarse como único indicador.

La mejora frente a greedy debe calcularse adicionalmente:

\[
\text{mejora frente a greedy}(\%)=
\frac{\text{costo greedy}-\text{mejor costo}}
{\text{costo greedy}}
\times100
\]

Resultados:

| n | Mejora frente a greedy |
|---:|---:|
| 10 | 16.53% |
| 20 | 11.05% |
| 50 | -1.87% |
| 100 | -16.39% |

Un valor negativo indica que la metaheurística entregó una solución peor que la referencia greedy.

---

## 7. Comparación temporal

| Algoritmo | n=10 | n=20 | n=50 | n=100 |
|---|---:|---:|---:|---:|
| Branch and Bound | 0.329 | 18.372 | 1.121 | 1.295 |
| Tabu Search | 0.543 | 3.397 | 4.787 | 7.639 |
| Ant Colony Optimization | 1.481 | 6.033 | 42.195 | 64.627 |
| Genetic Algorithm | 8.289 | 17.977 | 22.905 | 48.135 |
| Simulated Annealing | 4.176 | 7.462 | 10.721 | 11.467 |

### Observaciones

- Branch and Bound no puede evaluarse únicamente por tiempo cuando termina por límite de nodos.
- Tabu Search mostró el crecimiento más estable.
- ACO fue el método más costoso para \(n=50\) y \(n=100\).
- Genetic Algorithm presentó crecimiento progresivo.
- Simulated Annealing mantuvo tiempos moderados, pero su calidad se deterioró en las instancias grandes reportadas.

---

## 8. Análisis detallado para 10 ciudades

### 8.1 Resumen de las mejores rutas

| Algoritmo | Costo total | Promedio/tramo | Tramo máximo | P75 | Tramo máximo/total |
|---|---:|---:|---:|---:|---:|
| Branch and Bound | 277.539 | 27.754 | 54.275 | 39.265 | 19.56% |
| Tabu Search | 293.743 | 29.374 | 76.428 | 43.265 | 26.02% |
| Ant Colony Optimization | 277.348 | 27.735 | 76.752 | 34.057 | 27.67% |
| Genetic Algorithm | 275.452 | 27.545 | 57.122 | 34.367 | 20.74% |
| Simulated Annealing | 204.457 | 20.446 | 45.507 | 24.386 | 22.26% |

> Estos valores no constituyen un ranking justo, porque cada algoritmo resolvió una instancia diferente.

### 8.2 Distribución interna

| Algoritmo | Tramo mínimo | Rango | Desviación estándar | Coef. variación | Peso top 3 |
|---|---:|---:|---:|---:|---:|
| Branch and Bound | 12.972 | 41.303 | 13.42 | 48.36% | 48.47% |
| Tabu Search | 2.493 | 73.935 | 22.74 | 77.41% | 59.72% |
| Ant Colony Optimization | 1.413 | 75.339 | 21.71 | 78.27% | 60.50% |
| Genetic Algorithm | 10.434 | 46.688 | 13.02 | 47.27% | 46.46% |
| Simulated Annealing | 2.820 | 42.687 | 12.43 | 60.78% | 51.76% |

### 8.3 Rutas obtenidas

#### Branch and Bound

```text
0 → 4 → 1 → 3 → 6 → 9 → 5 → 7 → 2 → 8 → 0
```

Resultado certificado como óptimo para su instancia.

#### Tabu Search

```text
0 → 5 → 2 → 9 → 3 → 6 → 8 → 1 → 7 → 4 → 0
```

El tramo `3 → 6` representa el 26.02% del costo total.

#### Ant Colony Optimization

```text
5 → 4 → 3 → 0 → 7 → 6 → 2 → 8 → 1 → 9 → 5
```

El cierre `9 → 5` concentra el 27.67% del costo total, lo que evidencia una solución con buenos segmentos locales, pero un arco final costoso.

#### Genetic Algorithm

```text
0 → 2 → 7 → 4 → 1 → 6 → 8 → 3 → 9 → 5 → 0
```

Presentó la menor concentración del costo en los tres tramos principales y el menor coeficiente de variación entre las rutas comparadas.

#### Simulated Annealing

```text
0 → 3 → 8 → 2 → 9 → 6 → 7 → 4 → 5 → 1 → 0
```

Presentó los menores valores absolutos en su instancia, pero no debe concluirse que superó a los otros algoritmos sin utilizar la misma matriz ATSP.

---

## 9. Cartas Gantt de las rutas

Cada notebook incluye una visualización tipo carta Gantt.

En el gráfico:

- El eje vertical representa la secuencia de desplazamientos.
- El inicio de cada barra representa el costo acumulado previo.
- El ancho de la barra representa el costo del tramo.
- Los tramos sobre el percentil 75 se marcan como tramos costosos.
- La línea vertical final representa el costo total.

La carta Gantt no representa tiempo calendario. Se utiliza como una visualización del avance del costo acumulado a través de la ruta.

Esta representación permite identificar:

- Cuellos de botella.
- Arcos dirigidos especialmente costosos.
- Concentración del costo.
- Cierres de ciclo deficientes.
- Diferencias entre soluciones equilibradas y soluciones dominadas por pocos tramos.

---

## 10. Principales hallazgos

1. **Branch and Bound solo certificó optimalidad para 10 ciudades.**  
   En las instancias mayores alcanzó el límite de nodos, por lo que no existe garantía de optimalidad.

2. **Tabu Search mostró la escalabilidad temporal más estable.**  
   Su tiempo creció de 0.543 a 7.639 segundos entre 10 y 100 ciudades.

3. **ACO presentó el mayor costo computacional.**  
   Para 100 ciudades necesitó 64.627 segundos.

4. **Genetic Algorithm obtuvo una mejora relevante para 100 ciudades.**  
   Redujo el mejor costo inicial en 20.81%.

5. **Simulated Annealing requiere revisar el criterio de referencia.**  
   Mejoró fuertemente respecto de soluciones aleatorias, pero fue peor que greedy en 50 y 100 ciudades.

6. **La calidad global no depende únicamente del costo total.**  
   También es importante revisar la concentración de costos, el tramo máximo y la estabilidad entre ejecuciones.

7. **Las semillas distintas impiden una comparación causal directa.**  
   Una instancia puede ser estructuralmente más fácil que otra.

---

## 11. Limitaciones metodológicas

### 11.1 Instancias diferentes

La principal limitación es que los algoritmos utilizaron semillas de instancia diferentes. En consecuencia, no resolvieron exactamente el mismo problema.

### 11.2 Una ejecución por configuración

Una sola ejecución no permite medir adecuadamente la variabilidad de las metaheurísticas.

Para cada algoritmo y tamaño deberían realizarse al menos 20 o 30 ejecuciones independientes.

### 11.3 Presupuesto computacional desigual

Los métodos no utilizaron un presupuesto común:

- Branch and Bound se limita por nodos.
- Tabu Search se limita por iteraciones y vecinos.
- ACO se limita por hormigas e iteraciones.
- GA se limita por población y generaciones.
- SA se limita por iteraciones y enfriamiento.

Por lo tanto, una comparación rigurosa debería igualar:

- Tiempo máximo.
- Número de evaluaciones de la función objetivo.
- O ambos criterios.

### 11.4 Ausencia de benchmark externo

Las instancias son sintéticas. Para fortalecer la validación, se recomienda utilizar instancias reconocidas de TSPLIB o ATSPLIB.

---

## 12. Protocolo recomendado para una comparación justa

1. Generar una instancia por cada tamaño.
2. Guardar la misma matriz de costos.
3. Ejecutar los cinco algoritmos sobre esa matriz.
4. Repetir cada metaheurística con múltiples semillas.
5. Registrar:
   - Mejor costo.
   - Promedio.
   - Mediana.
   - Peor costo.
   - Desviación estándar.
   - Tiempo.
   - Evaluaciones.
6. Comparar contra:
   - Óptimo conocido.
   - Mejor cota inferior.
   - Solución greedy.
7. Calcular el gap:

\[
\text{gap}(\%)=
\frac{\text{costo obtenido}-\text{costo de referencia}}
{\text{costo de referencia}}
\times100
\]

8. Reportar intervalos de confianza o pruebas estadísticas cuando corresponda.

Ejemplo de semillas comunes:

```python
INSTANCE_SEEDS = {
    10: 259536255,
    20: 3833636183,
    50: 3725748727,
    100: 2110848497
}
```

Todos los algoritmos deben reutilizar estas mismas semillas de instancia.

---

## 13. Estructura del repositorio

```text
.
├── 01_branch_and_bound_ATSP_semillas_dinamicas.ipynb
├── 02_tabu_search_ATSP_semillas_dinamicas.ipynb
├── 03_ant_colony_ATSP_semillas_dinamicas.ipynb
├── 04_genetic_algorithm_ATSP_semillas_dinamicas.ipynb
├── 05_simulated_annealing_ATSP_semillas_dinamicas.ipynb
├── resultados/
│   ├── tablas/
│   └── graficos/
└── README.md
```

---

## 14. Requisitos

- Python 3.10 o superior.
- Jupyter Notebook o JupyterLab.
- NumPy.
- Pandas.
- Matplotlib.

Instalación:

```bash
python -m pip install numpy pandas matplotlib notebook
```

Ejecución:

```bash
jupyter notebook
```

Luego debe abrirse el notebook correspondiente y ejecutar las celdas en orden.

---

## 15. Consideraciones de ejecución

- Ejecutar siempre las celdas desde el inicio.
- No modificar una semilla después de generar la instancia.
- No comparar costos si las matrices son diferentes.
- Guardar los resultados antes de reiniciar el kernel.
- Registrar versiones de dependencias.
- No denominar “óptima” a una solución heurística.
- En Branch and Bound, revisar el estado final antes de afirmar optimalidad.

---

## 16. Conclusiones

Los experimentos muestran diferencias claras entre enfoques exactos y metaheurísticos.

Branch and Bound puede certificar optimalidad en instancias pequeñas, pero el crecimiento combinatorio obliga a limitar su búsqueda en problemas mayores. Las metaheurísticas permiten trabajar con 50 y 100 ciudades dentro de tiempos razonables, aunque no garantizan el óptimo.

Tabu Search presentó un comportamiento temporal estable. Ant Colony Optimization obtuvo mejoras importantes, pero con un costo computacional elevado. Genetic Algorithm mostró capacidad para mejorar soluciones en instancias grandes. Simulated Annealing mantuvo tiempos moderados, aunque su desempeño respecto de greedy se deterioró en los casos de 50 y 100 ciudades.

La conclusión más importante es metodológica: **la calidad de un algoritmo no puede determinarse a partir de una sola ejecución ni mediante instancias diferentes**. Una evaluación académicamente válida requiere instancias comunes, múltiples repeticiones, presupuestos comparables y métricas de dispersión.

---

## 17. Referencias

- Dorigo, M., & Gambardella, L. M. (1997). *Ant Colony System: A Cooperative Learning Approach to the Traveling Salesman Problem*. IEEE Transactions on Evolutionary Computation.
- Glover, F. (1989). *Tabu Search—Part I*. ORSA Journal on Computing.
- Holland, J. H. (1975). *Adaptation in Natural and Artificial Systems*. University of Michigan Press.
- Kirkpatrick, S., Gelatt, C. D., & Vecchi, M. P. (1983). *Optimization by Simulated Annealing*. Science.
- Lawler, E. L., Lenstra, J. K., Rinnooy Kan, A. H. G., & Shmoys, D. B. (1985). *The Traveling Salesman Problem: A Guided Tour of Combinatorial Optimization*. Wiley.

---

## Autor

**Rodrigo Molina**  
Ingeniería Civil Informática
