# Planteamiento

## Introducción

La contaminación del aire es uno de los principales riesgos ambientales para la salud. Partículas finas como el PM2.5 llegan hasta los alvéolos pulmonares, y gases como el dióxido de nitrógeno (NO₂) y el ozono (O₃) irritan las vías respiratorias. En Colombia, estos contaminantes los vigilan las autoridades ambientales regionales y urbanas mediante redes de estaciones de monitoreo, cuyos resultados se consolidan a nivel nacional.

Este trabajo analiza ese consolidado entre 2011 y 2024. Cada registro resume, para una estación, una variable y un tiempo de exposición, los estadísticos del año: promedio, mediana, percentil 98, máximo, mínimo, número de datos y representatividad temporal.

## Marco de referencia

En Colombia, la **Resolución 2254 de 2017** del Ministerio de Ambiente y Desarrollo Sostenible fija los niveles máximos permisibles de contaminantes en el aire. Además de los niveles vigentes, define metas más estrictas a 2030.

| Contaminante | Tiempo de exposición | Nivel vigente (µg/m³) | Meta 2030 (µg/m³) |
|---|---|---|---|
| PM10 | Anual | 50 | 30 |
| PM2.5 | Anual | 25 | 15 |
| NO₂ | Anual | 60 | 40 |
| SO₂ | 24 horas | 50 | 20 |
| O₃ | 8 horas | 100 | — |

Estos valores sirven como **referencia** para leer los promedios del EDA. No permiten verificar cumplimiento, porque el dataset mezcla tiempos de exposición distintos (1, 8 y 24 horas).

Las variables meteorológicas importan porque controlan la dispersión: el viento y la mezcla vertical (favorecida por la temperatura) diluyen los contaminantes, y la radiación solar participa en la formación de ozono.

## Planteamiento del problema

Colombia cuenta con más de una década de mediciones, pero la información llega de decenas de autoridades, con estaciones que entran y salen de operación, tiempos de exposición distintos y criterios de registro no homogéneos. Antes de usarla para tomar decisiones o construir modelos, hace falta saber qué contiene realmente, qué tan completa es y qué patrones muestra. Sin esa caracterización se corre el riesgo de sacar conclusiones de artefactos de los datos.

**Pregunta de investigación:**

> ¿Es posible anticipar, a partir de la ubicación, el tipo de estación y las características del monitoreo, qué estaciones registrarán días con PM2.5 por encima del límite diario de la norma? Y, como paso previo, ¿qué tan confiable y representativa es la información disponible para construir ese modelo?

Predecir las excedencias tiene valor práctico: permite priorizar dónde reforzar el monitoreo, emitir alertas tempranas y focalizar medidas de control en las zonas con mayor riesgo para la salud.

(objetivos)=
## Objetivos

### Objetivo general

Desarrollar un modelo de clasificación que prediga si una estación de monitoreo registrará excedencias del límite diario de PM2.5 establecido en la Resolución 2254 de 2017, e integrarlo en un dashboard interactivo, a partir del análisis exploratorio de los datos de calidad del aire de Colombia (2011–2024).

### Objetivos específicos

1. **Realizar un análisis exploratorio** que evalúe la calidad del dataset y caracterice la cobertura del monitoreo, los contaminantes, las variables meteorológicas, sus asociaciones y sus valores atípicos. *(Secciones 1 a 1.10)*
2. **Caracterizar la variable objetivo** —la excedencia del límite diario de PM2.5— y su relación con los posibles predictores. *(Sección 1.11)*
3. **Identificar los problemas de los datos** que debe resolver el preprocesamiento antes de entrenar el modelo. *(Conclusiones)*
4. **Entrenar y comparar modelos de clasificación** con validación por estación. *(Siguiente etapa)*
5. **Construir un dashboard interactivo** que integre el análisis exploratorio y el modelo. *(Siguiente etapa)*

Este libro corresponde a la **etapa de análisis exploratorio** y desarrolla los objetivos 1, 2 y 3.

## Metodología

El proyecto sigue cuatro etapas: **análisis exploratorio → preprocesamiento → modelado → dashboard**. En esta etapa (EDA) se aplicó el siguiente flujo:

1. **Carga y conversión de tipos.** Las columnas numéricas venían como texto con comas de miles; se eliminan las comas y se convierten a número.
2. **Diagnóstico de calidad.** Conteo de faltantes y duplicados, revisión de categorías y de la representatividad temporal.
3. **Depuración.** Se eliminan las filas duplicadas exactas. Los valores atípicos se conservan, porque un dato extremo no es necesariamente un error.
4. **Análisis univariado.** Estadística descriptiva y diagramas de caja por variable, sobre la columna `Promedio`.
5. **Análisis temporal.** Promedio anual de cada variable y cambio porcentual entre 2011 y 2024.
6. **Análisis bivariado.** Correlación de Pearson sobre los promedios anuales, con esta escala de fuerza: |r| ≥ 0,80 muy fuerte; 0,60–0,79 fuerte; 0,40–0,59 moderada; 0,20–0,39 débil; < 0,20 muy débil.
7. **Valores atípicos.** Criterio de Tukey: es atípico todo valor por debajo de Q1 − 1,5·IQR o por encima de Q3 + 1,5·IQR.
8. **Variable objetivo.** Excedencia del límite diario de PM2.5 (registros de 24 horas), su balance y su relación con los predictores candidatos, controlando la fuga de información.

**Herramientas:** Python (pandas, NumPy, SciPy, Matplotlib, seaborn). El mismo análisis se replicó en R con resultados idénticos.
