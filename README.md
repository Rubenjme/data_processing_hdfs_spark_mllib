# Procesamiento y predicción de retrasos aéreos con HDFS, Spark y Kafka

## Descripción del proyecto
Este proyecto tiene como objetivo aplicar técnicas de procesamiento masivo de datos utilizando tecnologías como HDFS, Apache Spark (SQL, MLlib, Streaming) y Apache Kafka en un entorno clúster desplegado mediante Google Cloud Dataproc. A lo largo del proyecto, se aborda el análisis de un dataset de vuelos, integrando componentes de almacenamiento distribuido, procesamiento en tiempo real y aprendizaje automático. Se pretende conseguir un flujo integral desde la carga y transformación de datos hasta el cálculo de métricas y procesamiento en streaming.

El archivo CSV contiene información sobre vuelos, con 162,049 registros y 16 columnas que incluyen detalles como fechas, tiempos de salida y llegada, retrasos, distancia, aerolíneas, etc.

## Objetivos
- Comprender y manejar sistemas de almacenamiento distribuido (HDFS) para gestionar grandes volúmenes de datos.
- Utilizar Apache Spark SQL para la transformación y análisis de datos estructurados.
- Aplicar un modelo de aprendizaje automático utilizando Spark MLlib.
- Procesar y analizar datos en tiempo real con Apache Kafka y Spark Streaming.
- Integrar las tecnologías mencionadas en un entorno de clúster para simular un caso práctico de big data.


## Estructura del proyecto
El proyecto está estructurada en varias partes, abordando diferentes componentes del ecosistema de big data:

### Manejo de HDFS
- Creación y gestión de directorios en el sistema de archivos distribuido.
- Carga de datos al clúster utilizando comandos HDFS.
- Verificación del almacenamiento y visualización de información del dataset en HDFS.

### Exploración y procesamiento con Apache Spark
- Lectura del dataset desde HDFS utilizando PySpark.
- Exploración de datos: manejo de valores faltantes, agrupaciones y cálculos de métricas.
- Uso de Spark SQL para consultas sobre los datos.

### Aplicación de aprendizaje automático con Spark MLlib
- Implementación de modelos predictivos utilizando regresión.
- Evaluación del rendimiento de los modelos.
- Ajuste de parámetros para optimizar los resultados.

### Procesamiento en tiempo real con Kafka y Spark Streaming
- Configuración de un topic en Kafka para recibir y procesar datos en tiempo real.
- Consumo de mensajes JSON desde Kafka utilizando Spark Streaming.
- Actualización y visualización en tiempo real de métricas calculadas.

### Flujo de trabajo
- Uso de Apache Kafka para enviar y recibir mensajes simulando datos de retrasos de vuelos.
- Actualización continua de métricas calculadas en el apartado anterior.
- Implementación de un flujo continuo con Structured Streaming para simular el análisis de datos en tiempo real.

## Tecnologías y Herramientas Utilizadas
- HDFS: Sistema de almacenamiento distribuido.
- Apache Spark: Framework para procesamiento masivo, análisis SQL y aprendizaje automático.
- Apache Kafka: Plataforma de mensajería para flujos de datos en tiempo real.
- PySpark: API de Python para Apache Spark.
- Google Cloud Platform (GCP): Infraestructura para despliegue de clústeres con Dataproc.
- JupyterLab: Entorno interactivo para desarrollo en notebooks.


## Configuración inicial

Para el desarrollo del proytecto será necesario contar con una cuenta en Google Cloud Platform (GCP). 
En GCP vamos a Cloud Storage -> Create Bucket.
- Indicamos un nombre que debe ser único globalmente, al mío lo llamaré -> alpha-dataproc
- Escojo como ubicación -> Europe-west1
- No es necesario tocar nada más y le damos a crear.

Vamos a Dataproc -> Clusters
Abrimos el Google Cloud Shell, para la configuración del bucket podemos hacerlo mediante la interfaz gráfica o por comandos.

El comando a introducir es:

gcloud beta dataproc clusters create **nombrecluster** --enable-component-gateway --bucket **nombrebucket** --region europe-west1 --zone europe-west1-c --master-machine-type n1-standard-2 --master-boot-disk-size 500 --num-workers 2 --worker-machine-type n1-standard-2 --worker-boot-disk-size 500 --image-version 2.0-debian10 --properties spark:spark.jars.packages=org.apache.spark:spark-sql-kafka-0-10_2.12:3.1.3 --optional-components JUPYTER,ZOOKEEPER --max-age **14400s** --initialization-actions 'gs://goog-dataproc-initialization-actions-europe-west1/kafka/kafka.sh' --project **identificadorproyecto**


Para cargar el archivo flights.csv lo cargué en una web externa y ejecuté el comando -> wget http://155.138.224.174:8080/flights.csv
Con este comando se descargará a la carpeta en la que me encuentre trabajando en terminal.
