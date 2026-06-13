## 1. Descripción del problema

Sea un conjunto de ciudades:

```math
V = \{0,1,\dots,n-1\}
```

y una matriz dirigida de costos:

```math
D = [d_{ij}]
```

donde $d_{ij}$ representa el costo de desplazarse desde la ciudad $i$ hasta la ciudad $j$.

En el ATSP no se exige simetría, por lo que:

```math
d_{ij} \neq d_{ji}
```

Una solución es una permutación de las ciudades:

```math
\pi = (\pi_0,\pi_1,\dots,\pi_{n-1})
```

que representa un ciclo hamiltoniano. Su costo se calcula como:

```math
f(\pi)=
\sum_{k=0}^{n-2} d_{\pi_k,\pi_{k+1}}
+
d_{\pi_{n-1},\pi_0}
```

El objetivo es encontrar la ruta de costo mínimo:

```math
\pi^\star = \arg\min_{\pi} f(\pi)
```
