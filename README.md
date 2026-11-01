# Calidad del aire en Colombia (2011–2024) · Dashboard del EDA

Dashboard en Dash con el análisis exploratorio de la calidad del aire en Colombia, la comparación de modelos de clasificación y la predicción de la excedencia diaria de PM2.5.

Autor: Camilo Romero

## Contenido

| Sección | Pestaña | Qué muestra |
|---|---|---|
| Planteamiento | Inicio, Introducción, Contexto, Problema, Objetivos, Metodología | Motivación, Res. 2254/2017, pregunta de investigación y objetivos |
| Datos | Dataset y calidad | Faltantes, duplicados (47 %), nombres inconsistentes, representatividad |
| Análisis | Exploración general · Contaminantes · Meteorología · Correlaciones · Valores atípicos · Variable objetivo | Gráficos interactivos y mapas satelitales |
| Modelo | Comparación de modelos · Predicción | Regresión logística, bosque aleatorio y gradient boosting con validación por estación. Predicción interactiva |
| Cierre | Conclusiones · Limitaciones | Resultados y limitaciones |

## Estructura

```
├── app.py                 # Punto de entrada y navegación
├── components.py          # Componentes de interfaz y estilo de gráficos
├── data/
│   ├── data_loader.py     # Carga, limpieza (igual al EDA) y agregaciones
│   └── dataset_calidad_aire.csv
├── modelo/entrenar.py    # Entrena y compara los modelos (python -m modelo.entrenar)
├── data/modelo/          # modelo.joblib y resultados.json
├── tabs/                  # Una pestaña por archivo: layout() + register_callbacks(app)
├── assets/styles.css      # Estilos (Dash los carga automáticamente)
├── requirements.txt
├── Procfile               # Comando de arranque para Render
└── .python-version        # Python 3.12
```

## Ejecutar localmente

```bash
pip install -r requirements.txt
python app.py
```

Abrir http://localhost:8050

## Desplegar en Render

1. Subir esta carpeta completa a un repositorio de GitHub (el CSV incluido).
2. En Render: **New → Web Service** → conectar el repositorio.
3. Configurar:
   - **Build command:** `pip install -r requirements.txt`
   - **Start command:** `gunicorn app:server --workers 2 --timeout 120`
   - **Instance type:** Free
4. (Opcional) En **Environment** agregar los enlaces de la barra lateral:
   `URL_EDA_PYTHON`, `URL_EDA_R`, `URL_REPO`.

## Datos

Consolidado nacional de indicadores anuales de calidad del aire reportados por 31 autoridades ambientales (2011–2024): 29.683 filas originales, 15.745 únicas, 654 estaciones y 20 variables.
