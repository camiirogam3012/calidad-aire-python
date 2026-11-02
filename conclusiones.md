(conclusiones)=
# Conclusiones

## Respuesta a la pregunta de investigación

Sí se pueden anticipar las excedencias de PM2.5. Con la ubicación, el tipo de estación, los días medidos y el año, el mejor modelo (gradient boosting) alcanzó un AUC de 0,824 con validación por estación, es decir, evaluado en estaciones que no vio durante el entrenamiento. Acierta en 3 de cada 4 registros.

Para llegar ahí hubo que preprocesar los datos: quitar duplicados, normalizar nombres, usar solo los registros de 24 horas y dejar por fuera las variables que causan fuga de información.

## Hallazgos del análisis exploratorio

- Calidad: el 46,96 % de las filas estaba duplicado. Hay 49 nombres para 24 departamentos y 339 para 193 municipios. La presión mezcla hPa y mmHg, la RUVb cambia de escala y hay representatividades de más de 100 %. Los faltantes son menos del 0,5 %.
- Cobertura: la red casi se triplicó entre 2011 y 2022, pero Antioquia y Bogotá tienen la mitad de los registros. La Amazonía, la Orinoquía y el Pacífico casi no tienen monitoreo.
- Contaminantes: el centro-norte (Valle de Aburrá) tiene la mediana más alta de PM2.5 (17,8 µg/m³) y el Caribe la más baja (13,7 µg/m³). El NO₂ es más del doble en las ciudades andinas que en el Caribe. Entre 2011 y 2024 el promedio nacional de PM10 bajó 27,7 % y el de PM2.5 31,4 %, y el ozono subió 14 %.
- Meteorología: el viento es débil en todas las regiones (medianas de 1,1 a 1,4 m/s). La presión sigue la altitud salvo por los registros en hPa. TAire y HAire tienen pocos registros, y P y RUVb tienen problemas de unidades y escala.
- Correlaciones: con Spearman la relación más confiable es PM10 y PM2.5 (ρ = 0,96 con 14 años). Las correlaciones con RUVb y presión no se pueden usar.
- Atípicos: 638 registros (4,1 %), casi todos valores altos. HAire (16 %), SO₂ (8,7 %) y CO (8,6 %) son las variables con más atípicos en proporción. Se conservan.

## Hallazgos sobre la variable objetivo

- La columna de excedencias solo sirve en los registros de 24 horas, donde coincide con el límite diario de la norma (50 µg/m³ hasta 2018 y 37 µg/m³ desde entonces).
- La variable objetivo tiene 1.144 registros de 383 estaciones y está balanceada (47,9 % excede).
- Aunque el promedio de PM2.5 bajó, el porcentaje de excedencia no baja. Los episodios críticos siguen.
- La ubicación y el tipo de estación son los predictores más claros.
- La excedencia sube con los días medidos: 21 % en el cuartil con menos días y 79 % en el de más días.

## Resultados del modelo

- Gradient boosting fue el mejor modelo (AUC 0,824, exactitud 0,747, recall 0,693), seguido por el bosque aleatorio (AUC 0,813). La regresión logística quedó atrás (AUC 0,740) porque la relación con la ubicación no es lineal.
- Los días medidos son el predictor que más pesa, seguido por el año y la ubicación. El tipo de estación casi no aporta cuando ya están los días medidos.
- Con una división de 80 % de las estaciones para entrenar y 20 % para probar, el AUC en prueba fue 0,816, muy parecido al de la validación cruzada.
- El modelo está integrado en el [dashboard en Dash](https://eda-dashboard-2pvi.onrender.com/) y en la [aplicación en Shiny](https://l28nb2-camilo-romero.shinyapps.io/calidad-aire-colombia/), con una página para entrenar y otra para predecir.

## Decisiones del preprocesamiento

| Problema | Decisión |
|---|---|
| Filas duplicadas | Eliminarlas |
| Nombres de departamento y municipio inconsistentes | Normalizar sin tildes y en mayúsculas |
| Excedencias no confiables en registros de 1 hora | Usar solo registros de 24 horas |
| Variables calculadas con las mismas mediciones (promedio, máximo, P98, días de excedencia) | Excluirlas por fuga de información |
| La excedencia depende de los días medidos | Incluir No. de datos como variable de control |
| El límite cambió en 2018 | Incluir el año como predictor |
| Una misma estación aparece en varios años | Validar separando estaciones (GroupKFold) |
| Errores de unidades en presión y RUVb | Corregirlos si se usan las variables meteorológicas |

## Limitaciones

- Las estaciones entran y salen de la red, así que los promedios nacionales mezclan cambios reales con cambios de cobertura.
- Los datos son anuales y no permiten estudiar la estacionalidad ni episodios de días concretos.
- La cobertura geográfica está sesgada y el modelo va a aprender sobre todo de la región Andina.
- Solo una parte de las estaciones de PM2.5 mide también variables meteorológicas, por eso no entran al modelo.
- El modelo estima la probabilidad de al menos un día de excedencia en el año. No pronostica concentraciones ni días concretos.
