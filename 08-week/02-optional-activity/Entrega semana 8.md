Informe Técnico Avanzado: Diseño, Arquitectura y Gobernanza de un Pipeline ETL de Extremo a Extremo en la Industria 4.0

Institución: Corporación Universitaria del Huila (CORHUILA)

Programa: Ingeniería Industrial

Asignatura: Ciencia de Datos

Unidad: Unidad 2 - Modelamiento, transformación y conexión de datos

Semana: Semana 8 / Corte 2

Periodo Académico: 2026-B

1. Introducción y Marco Teórico-Conceptual

En el ecosistema contemporáneo de la Industria 4.0, la transformación digital de los entornos productivos y manufactureros exige sistemas de información altamente resilientes, escalables y con baja latencia. La convergencia entre la tecnología operativa (OT) y la tecnología de la información (IT) genera volúmenes masivos de datos heterogéneos que provienen tanto de sensores físicos en planta como de sistemas administrativos y logísticos tradicionales (Kleppmann, 2017).

Para convertir esta avalancha de datos en conocimiento estratégico para la toma de decisiones gerenciales y operativas, es necesario implementar arquitecturas formales de integración de datos. Estas arquitecturas se estructuran fundamentalmente mediante pipelines ETL (Extract, Transform, Load) y ELT (Extract, Load, Transform), los cuales garantizan la centralización, depuración y modelado dimensional de la información (Kimball & Ross, 2013; Inmon, 2005).

El presente documento amplía el diseño técnico de un pipeline ETL orientado a la monitorización en tiempo real del desempeño de líneas de producción, cumpliendo rigurosamente con los criterios pedagógicos y metodológicos de la asignatura de Ciencia de Datos en la Corporación Universitaria del Huila (CORHUILA).

2. Arquitectura Detallada y Topología del Pipeline ETL

Una arquitectura de integración robusta no se limita a mover datos de un punto A a un punto B; requiere una separación clara de responsabilidades en cada una de sus capas operativas para asegurar la trazabilidad, la tolerancia a fallos y la auditabilidad del sistema.

2.1 Diagrama Estructural Completo de Extremo a Extremo

[ CAPA DE FUENTES ]              [ CAPA DE INGESTA ]           [ CAPA DE STAGING & LAGO ]
┌─────────────────────────┐
│ Sensores IoT (MQTT/REST)│ ──> ( Apache NiFi / Kafka ) ──┐
├─────────────────────────┤                               │
│ Base Relacional (ERP)   │ ──> ( Debezium / CDC ) ───────┼─> [ Data Lake / S3 (Parquet) ]
├─────────────────────────┤                               │
│ Archivos Planos (CSV)   │ ──> ( Python / S3 Uploader ) ─┘
└─────────────────────────┘
                                                                        │
                                                                        ▼
[ CAPA DE BI & CONSUMO ]        [ CAPA DE DATA WAREHOUSE ]      [ CAPA DE TRANSFORMACIÓN ]
┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐
│ Microsoft Power BI      │ <── │ Snowflake / BigQuery    │ <── │ Apache Spark / dbt      │
│ (Dashboards OEE & KPIs) │     │ (Esquema Estrella)      │     │ (Limpieza & Agregación) │
└─────────────────────────┘     └─────────────────────────┘     └─────────────────────────┘


2.2 Análisis Profundo por Capas Operativas

A. Capa de Extracción (Extract)

La fase de extracción debe operar bajo el principio de menor impacto sobre los sistemas transaccionales fuente.

Estrategias de Ingesta: Se implementa Change Data Capture (CDC) mediante herramientas como Debezium conectadas a los logs de transacciones del ERP, evitando consultas pesadas de tipo SELECT * que degraden el rendimiento operacional.

Manejo de Protocolos: Para los dispositivos de borde (Edge IoT), se utiliza el protocolo ligero MQTT para telemetría continua y peticiones REST API programadas para metadatos estáticos o semi-estáticos.

B. Capa de Staging y Transformación (Transform)

Los datos extraídos llegan en formato bruto (Raw Zone) a un almacenamiento temporal escalable (Data Lake en Amazon S3 o Hadoop HDFS) utilizando formatos columnar optimizados como Apache Parquet.

Limpieza y Enriquecimiento: Mediante motores de computación distribuida como Apache Spark o transformaciones declarativas con dbt (data build tool), se aplican reglas estrictas:

Estandarización de Timestamps: Conversión de todas las zonas horarias a UTC para evitar desfases temporales.

Imputación y Filtrado: Eliminación de valores atípicos físicos (ej. lecturas de temperatura negativos imposibles en calderas industriales).

Unificación Semántica: Cruce de identificadores de maquinaria del ERP con los identificadores físicos de telemetría.

C. Capa de Carga (Load) y Modelado Dimensional

Los datos transformados se cargan en un Cloud Data Warehouse (Snowflake o Google BigQuery). Para optimizar las consultas analíticas del equipo de ingeniería industrial, se diseña un Esquema de Estrella (Star Schema):

Tabla de Hechos (fact_produccion): Contiene métricas cuantitativas como tiempo de ciclo, unidades producidas, consumo energético y micro-paradas.

Tablas de Dimensiones (dim_maquina, dim_turno, dim_operario): Contienen atributos descriptivos para segmentar el análisis de rendimiento.

3. Justificación Rigurosa de Paradigmas: Batch vs. Streaming

En los sistemas industriales modernos, la selección entre procesamiento por lotes (Batch Processing) y procesamiento de flujos en tiempo real (Stream Processing) está dictada por el concepto de Ventana de Oportunidad de Decisión (Eberius et al., 2012).

3.1 Procesamiento en Streaming (Tiempo Real)

Casos de uso en el pipeline: Monitoreo de vibración en rodamientos de motores de alta potencia y detección de picos de temperatura en hornos de fundición.

Fundamentación técnica: La latencia tolerable es inferior a 1 segundo. Si un sensor detecta una vibración anómala que precede a una falla catastrófica de la maquinaria, un pipeline batch fallaría en prevenir el daño.

Arquitectura Tecnológica: Ingesta mediante Apache Kafka como intermediario de mensajería desacoplado y procesamiento stateful mediante Apache Flink o Spark Streaming, disparando alertas automáticas a los dispositivos móviles de los supervisores de planta.

3.2 Procesamiento por Lotes (Batch)

Casos de uso en el pipeline: Consolidación de costos de materia prima, liquidación de nómina de operarios vinculada a productividad y cálculo del OEE (Overall Equipment Effectiveness) global al cierre de cada jornada laboral.

Fundamentación técnica: Estos procesos no requieren una acción inmediata segundo a segundo. Agrupar millones de registros en bloques horarios o nocturnos maximiza la eficiencia en el uso del CPU, reduce drásticamente los costos de infraestructura en la nube y simplifica la depuración de errores ante fallos de red.

Arquitectura Tecnológica: Orquestación automatizada de flujos de trabajo (DAGs) mediante Apache Airflow.

4. Gobernanza, Calidad de Datos y Seguridad

Un pipeline ETL de nivel industrial no puede ignorar los lineamientos de gobernanza de datos (Data Governance):

Validación de Calidad (Data Quality): Se implementan pruebas automatizadas (mediante frameworks como Great Expectations) antes de permitir el paso de la zona de Staging a la zona de producción en el Data Warehouse, evaluando completitud (valores nulos permitidos < 0.1%), unicidad de claves primarias y rangos válidos.

Seguridad y Privacidad: En cumplimiento con normativas de protección de datos, los registros que contengan información personal de empleados (PII) son enmascarados o anonimizados (Data Masking) desde la fase de extracción inicial. Además, todo tránsito de información se cifra mediante protocolos TLS 1.3 y los repositorios en reposo implementan AES-256.

5. Implementación Práctica: Script de Extracción de API en Python

A continuación, se presenta un script modularizado en Python que simula la ingesta de datos desde una API externa pública, incorporando buenas prácticas de programación defensiva, manejo de excepciones de red, tipado y registro de trazas (logging).

import requests
import json
import logging
from datetime import datetime
from typing import List, Dict, Any

# Configuración avanzada del sistema de logging para trazabilidad industrial
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s [%(levelname)s] %(name)s: %(message)s',
    datefmt='%Y-%m-%d %H:%M:%S'
)
logger = logging.getLogger("ETL_Extraction_Module")

def extraer_datos_api(endpoint_url: str, timeout_sec: int = 10) -> List[Dict[str, Any]]:
    """
    Extrae datos estructurados desde una API REST pública implementando 
    manejo de errores de red, validación de códigos de estado y control de excepciones.
    
  Parámetros:
        endpoint_url (str): URL del recurso API.
        timeout_sec (int): Tiempo límite de espera para la conexión HTTP.
        
  Retorna:
        List[Dict[str, Any]]: Lista de diccionarios con los registros crudos obtenidos.
    """
    headers = {
        "User-Agent": "IndustrialDataPipeline-CORHUILA/2.6",
        "Accept": "application/json"
    }
    
  logger.info(f"Iniciando solicitud HTTP GET hacia el endpoint: {endpoint_url}")
    
  try:
        response = requests.get(endpoint_url, headers=headers, timeout=timeout_sec)
        
  #Evaluación del código de respuesta HTTP
        if response.status_code == 200:
            data = response.json()
            logger.info(f"Extracción exitosa. Se descargaron {len(data)} registros de la fuente.")
            return data
        else:
            logger.error(f"Fallo en la API fuente. Código de estado HTTP retornado: {response.status_code}")
            return []
            
  except requests.exceptions.Timeout:
        logger.error(f"Error crítico: La solicitud expiró (Timeout) tras superar los {timeout_sec} segundos.")
        return []
    except requests.exceptions.ConnectionError:
        logger.error("Error crítico: Fallo de conectividad de red con el servidor de la API.")
        return []
    except requests.exceptions.RequestException as e:
        logger.error(f"Error inesperado durante la petición HTTP: {e}")
        return []

def transformar_y_filtrar_registros(raw_data: List[Dict[str, Any]], limite_muestreo: int = 3) -> None:
    """
    Simula la etapa inicial de transformación y limpieza sobre los primeros registros,
    estructurándolos para su posterior carga en el Data Lake.
    """
    if not raw_data:
        logger.warning("No hay datos disponibles para procesar en la etapa de transformación.")
        return

  print("\n" + "=" * 70)
    print(f" REPORTE DE EJECUCIÓN ETL - MUESTRA DE {limite_muestreo} REGISTROS PROCESADOS")
    print("=" * 70)
    
   for indice, registro in enumerate(raw_data[:limite_muestreo], start=1):
        # Mapeo y saneamiento de campos clave (transformación elemental)
        registro_estructurado = {
            "metadata_etl": {
                "timestamp_extraccion": datetime.utcnow().isoformat() + "Z",
                "version_esquema": "v1.2"
            },
            "datos_operativos": {
                "id_registro": registro.get("id"),
                "nombre_empleado_o_entidad": registro.get("name", "N/A"),
                "correo_institucional": registro.get("email", "N/A"),
                "ubicacion_geografica": registro.get("address", {}).get("city", "N/A"),
                "compania_asociada": registro.get("company", {}).get("name", "N/A")
            }
        }
        
  print(f"\n--- Registro Limpio #{indice} ---")
        print(json.dumps(registro_estructurado, indent=4, ensure_ascii=False))
        print("-" * 50)
        
  print("=" * 70 + "\n")

if __name__ == "__main__":
    # URL de API pública de prueba robusta (JSONPlaceholder Users)
    API_URL = "https://jsonplaceholder.typicode.com/users"
    
  # Ejecución secuencial del sub-pipeline
  datos_brutos = extraer_datos_api(API_URL)
    transformar_y_filtrar_registros(datos_brutos, limite_muestreo=3)


6. Referencias Bibliográficas

Eberius, J., Hentschke, R., & Lehner, W. (2012). A unified architecture for batch and stream processing in enterprise data warehousing. Datenbanksysteme für Business, Technologie und Web (BTW), 199-218.

Inmon, W. H. (2005). Building the Data Warehouse (4th ed.). Wiley.

Kimball, R., & Ross, M. (2013). The Data Warehouse Toolkit: The Definitive Guide to Dimensional Modeling (3rd ed.). John Wiley & Sons.

Kleppmann, M. (2017). Designing Data-Intensive Applications: The Big Ideas Behind Reliable, Scalable, and Maintainable Systems. O'Reilly Media.

White, T. (2015). Hadoop: The Definitive Guide (4th ed.). O'Reilly Media.
