# Análisis Ibex
Proyecto para analizar las series históricas de datos del Ibex y buscar factores para predecir si el IBEX está en un ciclo de subida o de bajada. Por otro lado, predecir un precio objetivo.

# Dataset
Obtengo los datos de investing.es

Descargo en CSV los datos desde 1/01/2015 hasta 11/04/2025.

# Preparación de los datos
En primer lugar, habrá que limpiar y preparar los datos. 
En segundo lugar, preparo el vector indice de fechas. 
En tercer lugar, exploro y visualizo los datos para ver cómo proceder.
Quería establecer relación entre el IBEX, el VIX, y el precio del petróleo.
Normalizar los valores de las series temporales.
Hago pruebas de correlación a 30 días entre las series, y pruebas de restar las diferencias... pero al final no veo camino para establecer un modelo entrenado o no se me ocurre.ç

Así que decido calcular las medias a 3, 7 y 30 días, y establecer una señal: cuando las medias se crucen, el modelo deberá dar una señal de 1. EN caso contrario, cero.

# Entrenamiento
Eligo una regresión Logística. La entrada es un vector de valores del IBEX35, que debe ser superior a 30 (para la media a 30). Esto podría mejorarse mucho y que el cálculo de medias se hiciera por un servicio a partir de los valores obtenidos en un servicio web. Ahora no se hace así, es todo estático.

El modelo se entrena con los valores del IBEX35 del 1/1/2024 al 31/12/2024. 

# Validación
Para validar, selecciono los valores del IBEX del 01/01/2025 al 11/04/2025, y el modelo da señales de 1 cuando se cumple la condición de cruce de medias y de 0 en caso contrario.

# Conexión a gradio
A través de gradio el usuario puede subir un CSV de valores, y la salida del modelo es la predicción.
La mejora sería calcular los predecir a 1 semana los valores esperados, y obtener de servicios las cotizaciones y preguntarle al usuario simplemente el número de días de predicción!. 

# Extra - N8N
AL ser más mi especialización, he montado un sistema de N8N para procesar los emails de gmail en 2 etapas. El núcleo de esta práctica es la ingeniería de prompt y el tener un hardware lo más potente posible para ejecutar un modelo capaz. 

He seleccionado el https://huggingface.co/NousResearch/Hermes-3-Llama-3.1-8B-GGUF, cuantizado a 3 bits porque si no no entra en mi GPU...

La primera etapa clasifica loss emails y determina candidatos a facturas mediante LLM cargados en OLLAMA. Si el eail es un PDF o una imagen, se manda a un servicio levantado en docling en un contenedor. https://github.com/docling-project/docling-serve

La segunda etapa extrae los parámetros (coste, fecha, empresa).

Como resultado, el sistema va moviendo los emails a etiquetas (EMAIL_REVISADOS, FACTURAS_PENDIENTES, FACTURAS_PROCESADAS, FACTURAS_DESCARTADAS). Se podría mejorar metiendo en tablas en una BBDD los paránmetros extraídos de las facturas, por ej. 

O crear otro pipeline para detectar otro tipo de emails interesantes para el usuario.

