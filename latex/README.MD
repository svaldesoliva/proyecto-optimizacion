---
author:
- |
  Simón Valdés, Vicente Díaz\
  ING200 - Optimización\
  Prof. Jorge Acuña, PhD.\
  Universidad Adolfo Ibáñez\
  Viña del Mar
date: 2026-06-11
title: Proyecto
---

# Introducción

El Hospital Regional de Villa Zarcillo, al igual como otros hospitales
del país, se encuentra en una situación crítica respecto a las cirugías
electivas dada la suspensión de estas en el periodo de pandemia.

Debido a esto, desde el Gobierno, se ha tomado la decisión de habilitar
un contingente de salud, conformado por pabellones móviles junto a sus
respectivos equipos de salud a fin de recorrer distintas ciudades del
país realizando operaciones electivas a fin de descongestionar los
hospitales.

Durante la semana del 6 de julio, desde el día lunes hasta el día
viernes, el contingente de salud llegará a Villa Zarcillo. Debido a
esto, en conjunto con el hospital, deben determinar las operaciones a
realizar junto con la respectiva asignación de pabellones.

Para definir el plan de las operaciones a realizar, se tienen las
siguientes consideraciones:

-   Del conjunto de operaciones del Hospital, no todas deben ser
    realizadas.

-   Cada operación tiene un tipo de especialidad, un tiempo de
    realización y un costo asociado para el Estado.

-   Existe un conjunto de pabellones. En cada pabellón, se pueden
    realizar solamente ciertos tipos de operaciones, por ende, solo se
    puede realizar una operación en un pabellón que lo permita.

-   Cada operación que inicia un día debe ser completada ese mismo día.
    Además, cada pabellón puede tener una operación a la vez y los
    horarios son de 10:00 a 20:00.

-   Entre cada operación, debe existir una limpieza total de 1 hora. Al
    final del día, los pabellones se limpian luego de la última
    operación, no importando si la limpieza sobrepasa las 20:00.

-   Se posee un presupuesto de **\$100.000.000**.

El objetivo de este informe es optimizar el funcionamiento del hospital,
máximizando la cantidad de pacientes atendidos, el uso de los
pabellones, siguiendo las restricciones dadas.

# Desarrollo

## Modelado

A partir de las consideraciones, diseñamos el siguiente modelo de
optimización:

##### Variables:

$$X_{i,j,k} = \begin{cases}
        1 \text{ si la operación $i$ se realiza en el pabellón $j$ el día $k$} \\
        0 \text{ si no se realiza}
    \end{cases}$$

$$c_i = \text{Costo en pesos de la operación } i$$
$$t_i = \text{Tiempo en horas de la operación } i$$

$$p_{i,j}= \begin{cases}
        1 \text{ si la operación $i$ se puede realizar en el pabellón $j$} \\
        0 \text{ si no se puede}
    \end{cases}$$

##### Conjuntos:

[newline]{style="color: white"}

Operaciones: $$i \in I = [1,100]$$ Pabellones: $$j \in J = [1,5]$$ Días:
$$k \in K = [1,5]$$

##### Restricciones

[newline]{style="color: white"}

**1. Presupuesto:** La suma de todas las operaciones a realizar en
multiplicadas por el costo de su respectiva operación debe ser menor al
presupuesto de \$100.000.000.
$$\sum_{i\in I} \sum_{j \in J} \sum_{k \in K} X_{i,j,k} C_i \leq 100.000.000$$

**2. No se repiten las operaciones:** Para todas las operaciones, la
suma de las operaciones de un cierto tipo debe ser menor o igual a 1
para todos los pabellones y días.
$$\sum_{j \in J} \sum_{k \in K} X_{i,j,k} \leq 1, \forall i \in I$$

**3. Tiempo diario disponible:** El tiempo disponible son 10 horas al
día, por cada pabellón, además hay que considerar la hora de limpieza
post operación, por lo que cada operación dura $t_i + 1$ horas en total.
Cómo la última limpieza (posterior a las 20:00) no es considerada en
horas de limpieza, lo restamos a nuestra restricción, y como esta debe
ser menor o igual a las 10 horas disponibles, sumamos 1 a ambas partes
para simplificar.
$$\sum_{i \in I} (t_i + 1)X_{i,j,k} \leq 11, \forall j \in J, k \in K$$

**4. Compatibilidad:**Solo se puede asignar una operación a un pabellón
si este admite ese tipo.[^1]
$$X_{i,j,k} \leq P_{i,j}, \forall i \in I, j \in J, k \in K$$

##### Función objetivo

$$\max Z = \sum_{i \in I} \sum_{j \in J} \sum_{k \in K} X_{i,j,k}$$

##### Naturaleza de las variables

$$X_{i,j,k} = \{0,1\} \forall i \in I, j \in J, k \in K$$
$$c_i \in \mathbb{R}^+, \forall i \in I$$
$$t_i \in \mathbb{Z}^+, \forall i \in I$$

## Resolución

Usando la librería de Python del software de optimización *Gurobi*,
resolivmos el modelo, el cuál nos indicó que el óptimo se encuentra en
58 asignaciones. $$Z_1^* = 58$$ Luego, en base a ese número decidimos ir
resolviendo modelos de manera secuencial para obtener los costos óptimos
y el óptimo de uso de tiempo. Para ello, agregamos la restricción:

$$\sum_{i \in I} \sum_{j \in J} \sum_{k \in K} X_{i,j,k} = 58$$

Y modificamos la función objetivo para minimizar el costo:

$$\min C = \sum_{i\in I} \sum_{j \in J} \sum_{k \in K} X_{i,j,k} c_i$$

Al resolver el modelo, nos entrega un valor óptimo de \$97.802.912

$$C^* = 97.802.912$$

Luego, agregamos este valor como restricción, siguiendo la misma lógica
anterior:

$$\sum_{i\in I} \sum_{j \in J} \sum_{k \in K} X_{i,j,k} = 97.802.912$$

Finalmente, modificamos la función objetivo para maximizar la suma de
las horas asignadas:

$$\max T = \sum_{i\in I} \sum_{j \in J} \sum_{k \in K} X_{i,j,k} t_i$$

Obteniendo un valor óptimo de 134 horas de operaciones: $$T^* = 134$$

Pudimos obtener la siguiente información sobre el costo y uso de las
operaciones que el modelo eligió como óptimas:

::: {#tab:resumen_general}
  **Indicador**                          **Valor**
  ------------------------------------ --------------
  Total de Operaciones Realizadas            58
  Costo Total Asociado                  \$97.802.912
  Utilización Promedio de Pabellones       53,60%

  : Costos y Utilización
:::

También, pudimos obtener la distribución de operaciones por día:

::: {#tab:operaciones_diarias}
    **Día**    **Cantidad de Operaciones**
  ----------- -----------------------------
     Día 1                 11
     Día 2                  8
     Día 3                 12
     Día 4                 10
     Día 5                 17
   **Total**             **58**

  : Distribución Diaria
:::

Finalmente, pudimos hacer un análisis del tipo de operación, el número
de operaciones por tipo y su porcentaje respecto al total, el costo
asociado por tipo y el porcentaje del presupuesto que dicho tipo de
operación ocupa.

::: {#tab:analisis_especialidad}
  **Tipo de Operación**    **N° Ops.**     **% Ops.**   **Costo Asociado**  **% Presup.**
  ----------------------- ------------- ------------- -------------------- ---------------
  Gástrica                     12              20,69%         \$23.540.589     24,07%
  Traumatológica                8              13,79%         \$16.150.372     16,51%
  Ginecológica                  8              13,79%         \$13.960.515     14,27%
  Vascular                      2               3,45%          \$4.600.068      4,70%
  Dérmica                       7              12,07%          \$8.210.241      8,39%
  Urológica                     2               3,45%          \$4.510.138      4,61%
  Oftalmológica                11              18,97%         \$14.530.699     14,86%
  Otorrinolaringológica         8              13,79%         \$12.300.290     12,58%
  **Total**                  **58**       **100,00%**     **\$97.802.912**   **100,00%**

  : Tipos de operaciones
:::

A su vez, pudimos graficar el cronograma diario con las asignaciones de
operaciones correspondientes.

![Cronograma con asignación de operaciones - Día
1.](./TeX_Files/graphs/Cronograma_Dia1.pdf){#fig:cronograma_dia1
width="80%"}

![Cronograma con asignación de operaciones - Día
2.](./TeX_Files/graphs/Cronograma_Dia2.pdf){#fig:cronograma_dia2
width="80%"}

![Cronograma con asignación de operaciones - Día
3.](./TeX_Files/graphs/Cronograma_Dia3.pdf){#fig:cronograma_dia3
width="80%"}

![Cronograma con asignación de operaciones - Día
4.](./TeX_Files/graphs/Cronograma_Dia4.pdf){#fig:cronograma_dia4
width="80%"}

![Cronograma con asignación de operaciones - Día
5.](./TeX_Files/graphs/Cronograma_Dia5.pdf){#fig:cronograma_dia5
width="80%"}

## Modelo 2

##### 

Siguiendo con el contexto del problema, desde algunos sectores del
Ministerio de Salud, se ha planteado la necesidad de cambiar el enfoque
del problema, ya que, a juicio de algunos expertos, lo ideal sería poder
usar los pabellones la mayor parte del tiempo en operaciones y no en
limpiezas. Para esto, decidimos plantear una nueva función objetivo para
maximizar la suma de las horas asignadas:

$$\max T = \sum_{i\in I} \sum_{j \in J} \sum_{k \in K} X_{i,j,k} t_i$$

Resolvimos el modelo, el cuál nos indicó que el óptimo se encuentra en
152 horas. $$T^* = 152$$ Luego, en base a ese número decidimos ir
resolviendo modelos de manera secuencial para obtener la mayor cantidad
de operaciones y los costos óptimos. Para ello, agregamos la
restricción:

$$\sum_{i\in I} \sum_{j \in J} \sum_{k \in K} X_{i,j,k} t_i = 152$$

Y modificamos la función objetivo para maximizar la cantidad total de
operaciones realizadas:

$$\max Z = \sum_{i\in I} \sum_{j \in J} \sum_{k \in K} X_{i,j,k}$$

Al resolver el modelo, nos entrega un valor óptimo de 53 operaciones

$$Z^* = 53$$

Luego, agregamos este valor como restricción, siguiendo la misma lógica
anterior:

$$\sum_{i\in I} \sum_{j \in J} \sum_{k \in K} X_{i,j,k} = 53$$

Finalmente, modificamos la función objetivo para minimizar el costo:

$$\min C = \sum_{i\in I} \sum_{j \in J} \sum_{k \in K} X_{i,j,k} c_i$$

Obteniendo un valor óptimo de \$99.852.714: $$C^* = 99.852.714$$

Además, se obtienen los nuevos cronogramas:

![Cronograma con asignación de operaciones - Día 1 (Modelo
C).](./TeX_Files/graphs/Pregunta_C/Cronograma_Dia1.pdf){#fig:cronograma_dia1_modelo_C
width="80%"}

![Cronograma con asignación de operaciones - Día 2 (Modelo
C).](./TeX_Files/graphs/Pregunta_C/Cronograma_Dia2.pdf){#fig:cronograma_dia2_modelo_C
width="80%"}

![Cronograma con asignación de operaciones - Día 3 (Modelo
C).](./TeX_Files/graphs/Pregunta_C/Cronograma_Dia3.pdf){#fig:cronograma_dia3_modelo_C
width="80%"}

![Cronograma con asignación de operaciones - Día 4 (Modelo
C).](./TeX_Files/graphs/Pregunta_C/Cronograma_Dia4.pdf){#fig:cronograma_dia4_modelo_C
width="80%"}

![Cronograma con asignación de operaciones - Día 5 (Modelo
C).](./TeX_Files/graphs/Pregunta_C/Cronograma_Dia5.pdf){#fig:cronograma_dia5_modelo_C
width="80%"}

### Comparación

#### Comparativa general {#comparativa-general .unnumbered}

  **Indicador**                **Modelo B**   **Modelo C**
  --------------------------- -------------- --------------
  Total operaciones                 58             53
  Utilización de pabellones       53,60%         60,80%
  Costo total (\$)              97.802.912     99.852.714

  : Comparativa general de modelos

El modelo C logra una mejor utilización de los pabellones, logrando un
aumento del 7,2% de utilización (de 53,60% a 60,80%). Es más caro,
siendo \$2.049.802 mayor que el modelo B. Además, logra 5 operaciones
menos en total, con 53 versus 58.

#### Operaciones por día {#operaciones-por-día .unnumbered}

   **Día**   **Modelo B**   **Modelo C**
  --------- -------------- --------------
      1           11             10
      2           8              8
      3           12             12
      4           10             11
      5           17             12

  : Operaciones por día

El modelo C distribuye de forma más equitativa y similar las operaciones
por cada día.

#### Tipos de operaciones {#tipos-de-operaciones .unnumbered}

Acá podemos ver que en el Modelo C no se realiza ninguna operación
Vascular, mientras que en el B se hace una. Se hacen más
traumatológicas, un 8,85% más. Se hacen menos en gástrica, un 9,37%
menos.

Al comparar los cronogramas gráficos, podemos ver que el modelo C toma
cirugías más largas, a diferencia del B que intenta poner la mayor
cantidad de cirugías por lo que favorece a las de menor tiempo. Como
cada cirugía independiente de su tiempo tiene que tener una limpieza de
1 hora, hacer varias operaciones cortas requiere mucho tiempo de
limpieza que se "pierde", en cambio, si es una cirugía larga, se
requiere 1 sola limpieza.

El modelo C termina atendiendo 5 pacientes menos, sin embargo, toma más
operaciones largas, las cuales por su naturaleza son más graves, por lo
que estaría priorizando pacientes de gravedad frente a pacientes que
requieren operaciones menores.

En conclusión, decidimos que el modelo C es el más apropiado para el
contexto del problema, ya que el país se encuentra en una situación
crítica. Para agregar y mejorar el modelo, como vimos antes se había
anulado la operación vascular al optimizar, por lo que se podría
considerar algunos tipos de operación como más prioritarios al asignarle
un peso al tipo.

## Sensibilidad

::: thebibliography
9 Acuña, J. (2026) *Clases*
:::

[^1]: En el documento Jupyter no se usa esta restricción de forma
    explícita ya que se implementó una lista `combinaciones` que filtra
    previamente las tuplas válidas. Así, el modelo en Gurobi solo crea
    las variables de decisión para las operaciones y pabellones que son
    compatibles.
