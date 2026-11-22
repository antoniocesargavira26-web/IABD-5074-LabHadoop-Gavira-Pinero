# Despliegue de un Clúster Multi-Nodo Apache Hadoop con Docker

Práctica de laboratorio correspondiente al **Módulo 5074/5075** del Curso de Especialización en Inteligencia Artificial y Big Data (IABD) — **Campus Cámara de Comercio de Sevilla**.

El proyecto implementa mediante Infraestructura como Código (IaC) un clúster distribuido de **Apache Hadoop 3** sobre contenedores Docker para auditar el almacenamiento físico en **HDFS**, validar la **tolerancia a fallos** ante la caída en caliente de un nodo trabajador y ejecutar cargas de cómputo distribuido **MapReduce sobre YARN**.

---

## 1. Ficha Resumen de la Arquitectura

El entorno se despliega sobre una red puente aislada (`hadoop-net`) compuesta por **6 contenedores** basados en la imagen oficial `apache/hadoop:3`:

| Servicio / Contenedor | Rol en el Clúster | Capa | Puertos Expuestos (Host) | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| **`namenode`** | Maestro HDFS | Almacenamiento | `9870:9870` | Gestiona el espacio de nombres, metadatos y el mapa de bloques (`hdfs://namenode:8020`). |
| **`datanode1`** | Trabajador HDFS | Almacenamiento | — | Almacena bloques físicos de datos y reporta *heartbeats* al NameNode. |
| **`datanode2`** | Trabajador HDFS | Almacenamiento | — | Almacena bloques físicos de datos (usado en la simulación de caída en caliente). |
| **`datanode3`** | Trabajador HDFS | Almacenamiento | — | Almacena bloques físicos de datos y participa en la replicación. |
| **`resourcemanager`** | Maestro YARN | Cómputo | `8088:8088` | Planifica y asigna los recursos globales del clúster a las aplicaciones. |
| **`nodemanager`** | Trabajador YARN | Cómputo | — | Ejecuta los contenedores de tareas MapReduce asignados por el ResourceManager. |

### Parámetros Principales de Configuración (`docker/config`)

* **Sistema de ficheros por defecto (`fs.defaultFS`):** `hdfs://namenode:8020`
* **Tamaño de bloque (`dfs.blocksize`):** `64 MiB` (`67108864` bytes)
* **Factor de replicación (`dfs.replication`):** `R = 2`
* **Memoria asignada al NodeManager (`yarn.nodemanager.resource.memory-mb`):** `2048 MB` (`2 vCores`)
* **Framework de ejecución (`mapreduce.framework.name`):** `yarn`

---

## 2. Estructura del Repositorio

```text
IABD-5074-LabHadoop-Gavira-Pinero/
├── .gitignore
├── README.md
├── docker/
│   ├── config
│   └── docker-compose.yml
├── evidencias/
│   ├── 01_namenode_ui_9870.png
│   ├── 02_hdfs_fsck_bloques.png
│   ├── 03_simulacion_fallo_datanode2.png
│   └── 04_yarn_wordcount_8088.png
└── informe/
    └── Gavira_Pinero_InformeLabHadoop.pdf