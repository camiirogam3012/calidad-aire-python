(conclusiones)=
# Conclusiones

## Respuesta a la pregunta de investigación

**Sí es posible anticipar las excedencias de PM2.5**, y los datos muestran de dónde viene esa capacidad predictiva. Las excedencias se concentran en las ciudades andinas (Bogotá 65 %, Antioquia 57 %) y son poco frecuentes en la costa Caribe (Córdoba 5 %). Además, ocurren más en estaciones fijas que en indicativas. Una prueba preliminar con validación por estación alcanzó un AUC de 0,81.

Pero la información **requiere preprocesamiento antes de modelar**: hay duplicados, nombres inconsistentes, errores de unidades, un registro de excedencias no confiable en las mediciones horarias y una variable objetivo que depende del esfuerzo de medición.

## Hallazgos del análisis exploratorio (objetivo 1)

- **Calidad:** el 46,96 % de las filas estaba duplicado. Los departamentos aparecen con 49 nombres para 24 reales (municipios: 339 para 193). La presión mezcla hPa y mmHg, la radiación UV-B cambia de escala entre fuentes y hay representatividades mayores a 100 %. Los faltantes son mínimos (< 0,5 %).
- **Cobertura:** la red se triplicó entre 2011 y 2022, pero Antioquia y Bogotá aportan la mitad de los registros; la Amazonía, la Orinoquía y el Pacífico casi no tienen monitoreo.
- **Contaminantes:** PM10 (−27,7 %) y PM2.5 (−31,4 %) bajaron entre 2011 y 2024; el ozono subió (+14 %) y el NO₂ repunta desde 2017. Parte del cambio puede deberse a la entrada de estaciones nuevas.
- **Meteorología:** vientos débiles (1,2–1,7 m/s) que limitan la dispersión. TAire y HAire tienen pocos años; P y RUVb tienen problemas de unidades y escala.
- **Asociaciones:** la única relación fuerte y confiable es PM10–PM2.5 (r = 0,97). Las correlaciones de RUVb y presión son espurias.
- **Atípicos:** cerca del 4 % de los registros, casi siempre valores altos y concentrados en pocas estaciones. Se conservan.

## Hallazgos sobre la variable objetivo (objetivo 2)

- La columna de excedencias solo es confiable en los **registros de 24 horas**, donde aplica exactamente el límite diario de la norma (50 µg/m³ hasta 2018, 37 µg/m³ desde entonces).
- La variable objetivo tiene **1.144 registros de 383 estaciones** y está **balanceada** (47,9 % excede).
- Aunque el promedio de PM2.5 bajó, la tasa de excedencia **no muestra una tendencia a la baja**: los episodios críticos persisten.
- **Ubicación y tipo de estación** son los predictores más claros.
- La probabilidad de excedencia **sube con los días medidos** (21 % en el cuartil con menos días, 79 % en el de más días).

## Implicaciones para el modelo (objetivo 3)

| Problema | Acción en el preprocesamiento |
|---|---|
| Filas duplicadas | Eliminarlas (ya aplicado) |
| Nombres de departamento y municipio inconsistentes | Normalizar (sin tildes, mayúsculas) |
| Excedencias no confiables en registros de 1 hora | Usar solo registros de 24 horas |
| Variables calculadas con las mismas mediciones (Promedio, Máximo, P98, Días de excedencias) | Excluirlas: fuga de información |
| Dependencia del esfuerzo de medición | Incluir `No. de datos` como predictor de control |
| Cambio del límite en 2018 | Incluir el año como predictor |
| Varias estaciones con muchos años | Validar separando estaciones (*GroupKFold*), no filas al azar |
| Errores de unidades en presión y RUVb | Corregirlos si se incorporan variables meteorológicas |

## Limitaciones

- **Composición cambiante de la red:** las estaciones entran y salen; las tendencias nacionales mezclan cambios reales con cambios de cobertura.
- **Agregación anual:** el dataset no permite estudiar la estacionalidad ni episodios diarios concretos.
- **Cobertura geográfica sesgada:** el modelo aprenderá sobre todo de la región Andina.
- **Meteorología incompleta:** solo una fracción de las estaciones de PM2.5 mide también variables meteorológicas.

## Próximos pasos

1. **Preprocesamiento:** aplicar las acciones de la tabla anterior y construir el conjunto de entrenamiento.
2. **Modelado:** comparar regresión logística, bosque aleatorio y gradient boosting con validación por estación; reportar AUC, recall y matriz de confusión.
3. **Dashboard:** incorporar el modelo para estimar el riesgo de excedencia de una estación.
