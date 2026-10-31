# Análisis de ConnectaTel – Proyecto 6

Este repositorio contiene el análisis exploratorio de datos de **ConnectaTel**, una empresa de telecomunicaciones que opera en Latinoamérica. El proyecto utiliza información de clientes, planes contratados y registros de uso de llamadas y mensajes para identificar problemas de calidad, comprender los patrones de consumo y generar recomendaciones para el negocio.

El análisis se desarrolla en un notebook de Jupyter y utiliza datos correspondientes al periodo de estudio hasta 2024.

## 📂 Contenido del repositorio

La estructura sugerida para organizar los archivos es:

```text
connectatel-analysis/
│
├── data/
│   ├── plans.csv
│   ├── users_latam.csv
│   └── usage.csv
│
├── notebooks/
│   └── connectatel_analysis.ipynb
│
└── README.md
```

- `data/plans.csv` → Información de los planes, precios, minutos, datos incluidos y costes adicionales.
- `data/users_latam.csv` → Información de los clientes: identificador, edad, ciudad, fecha de registro, plan y fecha de cancelación.
- `data/usage.csv` → Registros de llamadas y mensajes realizados por los clientes.
- `notebooks/connectatel_analysis.ipynb` → Notebook principal con la preparación, limpieza, exploración, visualizaciones, segmentación e insights ejecutivos.

> **Nota:** ajusta los nombres o las rutas anteriores si tu repositorio utiliza una estructura diferente. El notebook trabajado hasta ahora cargaba los archivos desde `/datasets/`.

## 🎯 Objetivo del proyecto

Analizar el comportamiento de los clientes de ConnectaTel a partir de sus características y patrones de uso, con el fin de:

- Identificar y tratar problemas de calidad en los datos.
- Explorar la distribución de clientes según edad y plan contratado.
- Medir el uso de llamadas y mensajes por cliente.
- Identificar valores atípicos y patrones de consumo extremos.
- Segmentar a los clientes por edad y nivel de uso.
- Generar insights y recomendaciones que apoyen las decisiones comerciales y la optimización de la oferta de planes.

## 📊 Datasets utilizados

| Dataset | Contenido | Variables de interés |
|---|---|---|
| `plans.csv` | Características y condiciones de los planes | Precio, minutos, GB y costes adicionales |
| `users_latam.csv` | Perfil y ciclo de vida de los clientes | Edad, ciudad, fecha de registro, plan y fecha de cancelación |
| `usage.csv` | Actividad de los usuarios | Tipo de actividad, fecha, duración de llamadas y longitud de mensajes |

Los tres archivos se relacionan mediante el identificador `user_id` para construir un perfil de uso por cliente.

## 🔎 Etapas del análisis realizadas

### 1. Carga y exploración inicial
- Importación de `pandas`, `numpy`, `seaborn` y `matplotlib`.
- Carga de los tres archivos CSV.
- Revisión de dimensiones, tipos de datos y primeras filas.

### 2. Diagnóstico de calidad de datos
Se identificaron, entre otros, los siguientes problemas:

- En `users.city`, 469 valores nulos (aproximadamente 11,73 %).
- En `users.churn_date`, 3.534 valores nulos (aproximadamente 88,35 %), compatibles con clientes que no registran una fecha de cancelación.
- En `usage.date`, 50 valores nulos (aproximadamente 0,125 %).
- En `usage.duration`, 22.076 valores nulos (aproximadamente 55,19 %).
- En `usage.length`, 17.896 valores nulos (aproximadamente 44,74 %).
- El valor `-999` en `users.age` y el símbolo `?` en `users.city`, tratados como valores inválidos o sentinels.
- 40 fechas de registro de clientes correspondientes a 2026, fuera del periodo de análisis hasta 2024.

### 3. Limpieza y preparación
- Estandarización de texto en las columnas `city` y `plan`.
- Sustitución del sentinel `-999` en edad por la mediana calculada sin ese valor.
- Conversión del sentinel `?` de ciudad a un valor nulo.
- Conversión de las columnas de fecha al tipo datetime.
- Identificación de fechas futuras fuera del periodo de estudio.
- Comprobación de los nulos estructurales de `duration` y `length`.

Los nulos de `duration` en registros de mensajes (`text`) y los nulos de `length` en registros de llamadas (`call`) se conservaron porque corresponden a campos que no aplican a ese tipo de actividad.

### 4. Agregación del uso por cliente
- Cálculo de la cantidad de mensajes por usuario.
- Cálculo de la cantidad de llamadas por usuario.
- Cálculo del total de minutos de llamadas por usuario.
- Unión de estas métricas con la información de clientes para construir `user_profile`.

### 5. Análisis exploratorio y visualizaciones
- Histogramas de edad y métricas de uso, diferenciados por plan.
- Boxplots de edad, mensajes, llamadas y minutos de llamadas.
- Revisión de estadísticas descriptivas y posibles valores atípicos.

### 6. Segmentación de clientes
Se construyeron dos criterios de segmentación:

**Por nivel de uso**
- `Bajo uso`: menos de 5 llamadas y menos de 5 mensajes.
- `Uso medio`: no pertenece al grupo anterior y registra menos de 10 llamadas y menos de 10 mensajes.
- `Alto uso`: los demás casos.

**Por edad**
- `Joven`: menor de 30 años.
- `Adulto`: desde 30 y menor de 60 años.
- `Adulto Mayor`: 60 años o más.

Se generaron gráficos de barras para visualizar la distribución de clientes en cada segmento.

### 7. Insight ejecutivo y recomendaciones
El análisis de segmentación compartido para el cierre del proyecto señala:

- **Nivel de uso:** el grupo de Uso medio es el más numeroso, con cerca de 2.900 usuarios; Bajo uso reúne alrededor de 800 y Alto uso aproximadamente 300.
- **Edad:** Adultos (30 a 59 años) conforman el grupo más numeroso, con más de 2.000 clientes; Adultos Mayores representan cerca de 1.250 y Jóvenes alrededor de 750.
- **Segmentos de interés comercial:** el grupo de Uso medio y edad adulta constituye una base amplia para estrategias de retención. El grupo de Alto uso puede representar una oportunidad para ofertas de mayor capacidad y fidelización.
- **Consumo de voz:** se reportó un máximo de 155,69 minutos frente a una mediana de 19,78 minutos. Este comportamiento puede justificar una revisión de paquetes de voz o complementos para clientes de alto consumo.

A partir de estos hallazgos se plantearon recomendaciones preliminares:

1. Evaluar planes flexibles para clientes de uso medio.
2. Estudiar complementos o planes de mayor capacidad de voz para clientes de alto consumo.
3. Diseñar campañas de comunicación y retención diferenciadas por segmentos de edad y uso.

> **Criterio de interpretación:** las cantidades de segmentos y los hallazgos ejecutivos anteriores son aproximados y corresponden a los resultados compartidos durante el trabajo. Antes de tomar decisiones comerciales, conviene validarlos contra las salidas finales del notebook. Los valores extremos deben evaluarse con el método IQR y con los registros originales; no deben considerarse errores automáticamente.

## ▶ Cómo abrir el notebook en Google Colab

### Opción 1: desde GitHub

Cuando el repositorio esté publicado en GitHub:

1. Abre el repositorio.
2. Entra en la carpeta `notebooks/`.
3. Abre `connectatel_analysis.ipynb`.
4. Selecciona **Open in Colab** si aparece esa opción, o utiliza el enlace de Colab con la URL del notebook.

También puedes construir un enlace de apertura reemplazando `USUARIO`, `REPOSITORIO` y la ruta por los datos reales:

```text
https://colab.research.google.com/github/USUARIO/REPOSITORIO/blob/main/notebooks/connectatel_analysis.ipynb
```

### Opción 2: cargar el notebook directamente

1. Entra en [Google Colab](https://colab.research.google.com/).
2. Selecciona **Archivo → Subir cuaderno**.
3. Carga `connectatel_analysis.ipynb`.

Para ejecutar el análisis, también debes poner los archivos CSV a disposición del entorno de Colab, por ejemplo, subiéndolos a la sesión o guardándolos en Google Drive.

## 📘 Guía breve de reproducción

1. Descarga o clona el repositorio.
2. Abre `notebooks/connectatel_analysis.ipynb` en Google Colab o Jupyter Notebook.
3. Comprueba que los tres archivos CSV estén disponibles en la ruta que utiliza el notebook.
4. Si es necesario, actualiza las rutas de lectura. Por ejemplo, si los CSV están dentro de `data/` en el entorno de Colab:

   ```python
   plans = pd.read_csv('/content/data/plans.csv')
   users = pd.read_csv('/content/data/users_latam.csv')
   usage = pd.read_csv('/content/data/usage.csv')
   ```

5. Ejecuta las celdas en orden, desde la importación de librerías hasta las conclusiones.
6. Verifica que las columnas y los nombres de variables coincidan con los utilizados en el notebook.
7. Revisa las tablas, gráficos y resultados para confirmar que la ejecución se completó correctamente.

### Librerías necesarias

El proyecto utiliza:

```python
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
```

En Google Colab estas librerías suelen estar disponibles por defecto. En un entorno local, pueden instalarse con:

```bash
pip install pandas numpy seaborn matplotlib
```

## 📌 Resultado esperado

Al reproducir el notebook se obtiene una base de clientes enriquecida con métricas de uso, un diagnóstico de calidad de datos, visualizaciones de las principales variables, segmentos por edad y nivel de consumo, y un conjunto de insights y recomendaciones para ConnectaTel.

---

**Proyecto:** ConnectaTel – Proyecto 6  
**Herramienta principal:** Python y Jupyter Notebook  
**Entorno recomendado:** Google Colab  
