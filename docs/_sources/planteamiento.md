# Planteamiento

## Introducción

La contaminación del aire es uno de los riesgos ambientales que más afectan la salud. Las partículas finas como el PM2.5 llegan hasta los alvéolos, y gases como el dióxido de nitrógeno (NO₂) y el ozono (O₃) irritan las vías respiratorias. En Colombia estos contaminantes los miden las autoridades ambientales con redes de estaciones, y los resultados se reúnen en un consolidado nacional.

En este trabajo se analiza ese consolidado entre 2011 y 2024. Cada registro resume un año de mediciones para una estación, una variable y un tiempo de exposición, con el promedio, la mediana, el percentil 98, el máximo, el mínimo, el número de datos y la representatividad temporal.

## Marco de referencia

La Resolución 2254 de 2017 del Ministerio de Ambiente y Desarrollo Sostenible fija los niveles máximos permitidos de contaminantes en el aire y unas metas más estrictas para 2030.

| Contaminante | Tiempo de exposición | Nivel vigente (µg/m³) | Meta 2030 (µg/m³) |
|---|---|---|---|
| PM10 | Anual | 50 | 30 |
| PM2.5 | Anual | 25 | 15 |
| NO₂ | Anual | 60 | 40 |
| SO₂ | 24 horas | 50 | 20 |
| O₃ | 8 horas | 100 | Sin meta |

En el EDA estos valores se usan como referencia para leer los resultados. No sirven para verificar cumplimiento porque el dataset mezcla tiempos de exposición de 1, 8 y 24 horas.

Las variables meteorológicas también importan porque controlan cómo se dispersan los contaminantes. El viento y la mezcla del aire, que aumenta con la temperatura, los diluyen, y la radiación solar ayuda a formar ozono.

## Planteamiento del problema

Colombia tiene más de diez años de mediciones, pero los datos vienen de muchas autoridades, las estaciones entran y salen de operación, los tiempos de exposición son distintos y no todos registran igual. Antes de usar esta información para tomar decisiones o hacer un modelo hay que saber qué contiene, qué tan completa está y qué patrones tiene.

Pregunta de investigación:

> ¿Se puede anticipar, a partir de la ubicación, el tipo de estación y las características del monitoreo, qué estaciones van a tener días con PM2.5 por encima del límite diario de la norma? Y antes de eso, ¿qué tan confiable es la información disponible para construir ese modelo?

Predecir las excedencias sirve para decidir dónde reforzar el monitoreo, dar alertas tempranas y enfocar las medidas de control en las zonas con más riesgo.

(objetivos)=
## Objetivos

### Objetivo general

Desarrollar un modelo de clasificación que prediga si una estación de monitoreo tendrá excedencias del límite diario de PM2.5 de la Resolución 2254 de 2017, e integrarlo en un dashboard interactivo, a partir del análisis exploratorio de los datos de calidad del aire de Colombia entre 2011 y 2024.

### Objetivos específicos

1. Realizar un análisis exploratorio que evalúe la calidad del dataset y describa la cobertura del monitoreo, los contaminantes, las variables meteorológicas, sus correlaciones y sus valores atípicos.
2. Caracterizar la variable objetivo, la excedencia del límite diario de PM2.5, y su relación con los posibles predictores.
3. Identificar los problemas de los datos que debe resolver el preprocesamiento antes de entrenar el modelo.
4. Entrenar y comparar modelos de clasificación con validación por estación.
5. Construir un dashboard interactivo que integre el análisis exploratorio y el modelo.

## Metodología

El proyecto tiene cuatro etapas: análisis exploratorio, preprocesamiento, modelado y dashboard. En el EDA se siguieron estos pasos:

1. Carga y conversión de tipos. Las columnas numéricas venían como texto con comas de miles. Se quitan las comas y se convierten a número.
2. Diagnóstico de calidad. Conteo de faltantes y duplicados, revisión de las categorías y de la representatividad temporal.
3. Depuración. Se eliminan las filas duplicadas. Los valores atípicos se dejan, porque un dato extremo no siempre es un error.
4. Análisis univariado. Estadística descriptiva y diagramas de caja de la columna Promedio para cada variable.
5. Comparación por región. Distribución de cada contaminante y variable meteorológica según la región de la estación.
6. Análisis bivariado. Correlación de Spearman sobre los promedios anuales. Se usa Spearman porque cada variable tiene pocos puntos (máximo 14 años), las distribuciones no son normales y un año extremo afecta menos a los rangos. La fuerza se lee así: |ρ| ≥ 0,80 muy fuerte, 0,60 a 0,79 fuerte, 0,40 a 0,59 moderada, 0,20 a 0,39 débil y menos de 0,20 muy débil.
7. Valores atípicos. Criterio de Tukey: es atípico todo valor menor que Q1 − 1,5·IQR o mayor que Q3 + 1,5·IQR.
8. Variable objetivo. Excedencia del límite diario de PM2.5 en los registros de 24 horas, su balance y su relación con los posibles predictores, cuidando la fuga de información.

Herramientas: Python (pandas, NumPy, SciPy, Matplotlib, seaborn y Plotly).
