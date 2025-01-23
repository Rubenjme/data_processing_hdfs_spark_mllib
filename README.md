# Procesamiento y predicción de retrasos aéreos con HDFS, Spark y Kafka

## Descripción del proyecto
Este proyecto tiene como objetivo aplicar técnicas de procesamiento masivo de datos utilizando HDFS, Apache Spark (SQL, MLlib, Streaming) y Apache Kafka en un entorno clúster desplegado mediante Google Cloud Dataproc. A lo largo del proyecto, se aborda el análisis de un dataset de vuelos, integrando componentes de almacenamiento distribuido, procesamiento en tiempo real y aprendizaje automático. Se pretende conseguir un flujo integral desde la carga y transformación de datos hasta el cálculo de métricas y procesamiento en streaming.

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

## Tecnologías y herramientas utilizadas
- HDFS: Sistema de almacenamiento distribuido.
- Apache Spark: Framework para procesamiento masivo, análisis SQL y aprendizaje automático.
- Apache Kafka: Plataforma de mensajería para flujos de datos en tiempo real.
- PySpark: API de Python para Apache Spark.
- Google Cloud Platform (GCP): Infraestructura para despliegue de clústeres con Dataproc.
- JupyterLab: Entorno interactivo para desarrollo en notebooks.


## Configuración inicial
Para el desarrollo del proyecto será necesario disponer de una cuenta en Google Cloud Platform (GCP) (https://cloud.google.com/free)

### Creación de un bucket
En el menú lateral, ve a Cloud Storage -> Create Bucket.

Configura el bucket con:
- Nombre: Debe ser único globalmente, al mío lo llamé -> alpha-dataproc
- Ubicación: Selecciona "Region" y elige "europe-west1" (o la que prefieras)
- No es necesario tocar nada más. Haz clic en el botón Create para finalizar.

![Creación Bucket](https://github.com/user-attachments/assets/07270a35-d52e-479f-a08f-d0f2e0f37570)

### Creación de un clúster:
La creación del clúster se puede realizar con la interfaz gráfica de Google Cloud, pero será más sencillo y rápido hacerlo por línea de comandos.

Abrimos el Google Cloud Shell en la parte superior derecha.

![2cloudshell](https://github.com/user-attachments/assets/2fa185b7-c122-4ce3-87e2-d6bff2fb9443)

Introducimos el siguiente comando:

gcloud beta dataproc clusters create **nombrecluster** --enable-component-gateway --bucket **nombrebucket** --region europe-west1 --zone europe-west1-c --master-machine-type n1-standard-2 --master-boot-disk-size 500 --num-workers 2 --worker-machine-type n1-standard-2 --worker-boot-disk-size 500 --image-version 2.0-debian10 --properties spark:spark.jars.packages=org.apache.spark:spark-sql-kafka-0-10_2.12:3.1.3 --optional-components JUPYTER,ZOOKEEPER --max-age **14400s** --initialization-actions 'gs://goog-dataproc-initialization-actions-europe-west1/kafka/kafka.sh' --project **identificadorproyecto**

Se debe modificar:
- nombrecluster: Nombre que prefieras para el clúster, en el proyecto se usó -> cluster1
- nombrebucket: Nombre del bucket creado anteriormente.
- max-age: Es el tiempo que estará activo el clúster, pasado ese tiempo se eliminará para evitar gastos innecesarios (por defecto es 4 horas). Los datos y notebooks que necesiten persistencia deben almacenarse en el bucket GCS creado.
- identificadorproyecto: El ID del proyecto en GCP (visible en la terminal en color amarillo)

Tardará unos minutos en crearse.

![3codeshell](https://github.com/user-attachments/assets/de82f5ff-57fd-415c-a3fc-e1dc78a7f005)

### Acceso al clúster:

Una vez que el clúster esté operativo (marcado en verde), selecciona su nombre.

![4cluster](https://github.com/user-attachments/assets/89138c23-d43d-40aa-8787-6884164d4417)

Entra en Web Interfaces y abre JupyterLab.

![5jupyterLab](https://github.com/user-attachments/assets/744ec62c-7b99-4809-8c7f-fc1b45051999)

### Subida del archivo csv y configuración

Abre una terminal de JupyterLab y usa el comando:

hdfs dfs -mkdir /NombreDirectorio

Sustituye nombre directorio por el que prefieras, en el proyecto se usó -> DataCluster_Test

Sitúate en Local Disk y sube el archivo csv. 

En mi caso, al subirlo por la interfaz, no se subía correctamente, por lo que para cargar el archivo flights.csv lo cargué en una web externa y ejecuté el siguiente comando en la consola -> wget http://155.138.224.174:8080/flights.csv

Con este comando se descargará a la carpeta en la que me encuentre trabajando en terminal.

![6flightsup](https://github.com/user-attachments/assets/eaa32b85-3450-4462-9f15-21bd37a72e7f)

El archivo debe aparecer de la siguiente forma:

![6 1flightsup](https://github.com/user-attachments/assets/14c46138-8710-42b3-a9a8-7505deea39e2)


Ejecutamos el siguiente comando para copiar el csv al directorios de hdfs creado anteriormente:

hdfs dfs -copyFromLocal flights.csv /NombreDirectorio

![7copyfromlocal](https://github.com/user-attachments/assets/3b6cf2e6-9274-4776-a3a6-7191a6e03f68)

Ejecutamos el siguiente comando para confirmar que se ha copiado correctamente:

hdfs dfs -ls /NombreDirectorio

![8lscommand](https://github.com/user-attachments/assets/7e08d075-98af-4598-afb2-cb0ccd64bf48)

Ejecutamos el siguiente comando para conocer información detallada sobre cómo está almacenado el archivo:

hdfs fsck /NombreDirectorio/flights.csv

![9fsckcomm](https://github.com/user-attachments/assets/265ac109-3675-47f3-b591-ebb2164951c1)

