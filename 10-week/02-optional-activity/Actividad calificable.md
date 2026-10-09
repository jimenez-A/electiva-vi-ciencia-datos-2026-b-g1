# Informe Técnico: Mini-Proyecto de Modelamiento, Transformación y Conexión de Datos

* **Asignatura:** Ciencia de Datos
* **Programa:** Ingeniería Industrial
* **Periodo:** 2026-B
* **Unidad:** Unidad 2 – Modelamiento, transformación y conexión de datos
* **Semana / Corte:** Semana 10 – Cierre de Corte 2
* **Institución:** Corporación Universitaria del Huila (CORHUILA)

---

## 1. Introducción y Marco Metodológico

El presente informe documenta de manera exhaustiva el desarrollo del mini-proyecto correspondiente a la Semana 10 (Cierre del Corte 2) de la asignatura de Ciencia de Datos del programa de Ingeniería Industrial en la Corporación Universitaria del Huila (CORHUILA). 

La transformación digital y la proliferación de fuentes de datos heterogéneas exigen que los futuros profesionales de la ingeniería dominen el ciclo de vida completo de los datos (*Data Lifecycle*). Como señalan Davenport y Harris (2007), la capacidad de una organización para competir mediante la analítica depende de su rigor en la captura, estructuración y modelado de la información operativa. El objetivo central de esta práctica es integrar un modelo relacional normalizado, ejecutar un pipeline robusto de limpieza y transformación en Python (pandas), y resolver preguntas analíticas complejas mediante técnicas de filtrado y agregación.

---

## 2. Modelo de Datos (Diagrama Entidad-Relación - ERD)

Para asegurar la coherencia estructural y evitar anomalías en el almacenamiento, se ha diseñado un modelo relacional bajo los principios de la Tercera Forma Normal (3NF) (Elmasri & Navathe, 2015). El modelo está compuesto por tres entidades principales interconectadas:

### 2.1 Descripción de Entidades y Atributos

* **Entidad clientes**:
  * id_cliente (Primary Key, INT): Identificador único del cliente.
  * nombre_cliente (VARCHAR): Nombre completo del cliente o razón social.
  * correo (VARCHAR): Dirección de correo electrónico de contacto.
  * fecha_registro (DATETIME): Marca de tiempo del registro en el sistema.

* **Entidad productos**:
  * id_producto (Primary Key, INT): Identificador único del producto o ítem analizado.
  * nombre_producto (VARCHAR): Denominación comercial del producto.
  * categoria_producto (VARCHAR): Categoría o línea de negocio a la que pertenece.
  * precio_unitario (FLOAT): Costo monetario unitario.
  * stock_actual (INT): Cantidad disponible en inventario.

* **Entidad transacciones (Tabla de Hechos)**:
  * id_transaccion (Primary Key, INT): Identificador único de la transacción u orden.
  * id_cliente (Foreign Key, INT): Relación con la entidad clientes.
  * id_producto (Foreign Key, INT): Relación con la entidad productos.
  * cantidad (INT): Unidades adquiridas en la transacción.
  * fecha_transaccion (DATETIME): Fecha y hora exacta de la operación.
  * valor_total (FLOAT): Monto económico total calculado (cantidad * precio_unitario).

---

## 3. Ingesión, Preparación y Limpieza del Dataset en Pandas

El preprocesamiento de los datos se realiza mediante un script optimizado en Python utilizando la librería pandas (McKinney, 2017). El pipeline de ingesta y limpieza comprende las siguientes etapas críticas:

1. **Carga y Validación Inicial:** Ingesta de archivos planos (CSV) mediante pd.read_csv() y revisión estructural de tipos de datos, dimensiones y celdas vacías con métodos exploratorios como .info() y describe().
2. **Tratamiento de Valores Nulos e Inconsistencias:** Detección de nulos mediante .isnull().sum() y aplicación de estrategias de imputación (sustitución de valores numéricos por la mediana estadística) o eliminación de registros anómalos que comprometan la integridad analítica.
3. **Tipificación y Normalización Temporal:** Conversión de campos de fecha mediante pd.to_datetime() para habilitar operaciones de filtrado cronológico y series de tiempo, junto con la estandarización de cadenas de texto (minúsculas, eliminación de espacios blancos redundantes).
4. **Depuración de Duplicados:** Eliminación de registros repetidos utilizando .drop_duplicates(subset=['id_transaccion']) para garantizar la unicidad de las transacciones.

---

## 4. Consultas Analíticas (Filtro y Agregación)

Para resolver los requerimientos analíticos del proyecto, se han desarrollado dos consultas basadas en operaciones avanzadas de filtrado condicional y agregación con `pandas`:

### 4.1 Consulta 1: Volumen de Ventas y Rendimiento por Categoría de Producto
* **Objetivo:** Filtrar las transacciones correspondientes al año fiscal actual y calcular la suma total de ingresos generados por cada categoría de producto.
* **Implementación en Código (pandas):**

``python
import pandas as pd

# Carga del dataset depurado
df = pd.read_csv('transacciones_limpio.csv')

# Conversión y filtrado por fecha (Año 2026)
df['fecha_transaccion'] = pd.to_datetime(df['fecha_transaccion'])
df_filtrado = df[df['fecha_transaccion'].dt.year == 2026]

# Agregación por categoría de producto
ventas_por_categoria = (
    df_filtrado.groupby('categoria_producto')['valor_total']
    .sum()
    .reset_index(name='ingreso_total')
    .sort_values(by='ingreso_total', ascending=False)
)

print("--- REPORTE DE VENTAS POR CATEGORÍA ---")
print(ventas_por_categoria)

## 4.2 Consulta 2: 
Identificación de Clientes Frecuentes y Cálculo del Ticket PromedioObjetivo: Agrupar las operaciones por cliente para determinar su frecuencia de compra y calcular el valor monetario medio por orden (ticket promedio), aislando a los clientes más activos.   
Implementación en Código (pandas):

# Agregación por cliente para frecuencia y ticket promedio
analisis_clientes = df.groupby(['id_cliente', 'correo']).agg(
    frecuencia_compras=('id_transaccion', 'count'),
    ticket_promedio=('valor_total', 'mean'),
    gasto_acumulado=('valor_total', 'sum')
).reset_index()

# Filtrado de clientes frecuentes (más de 3 transacciones)
umbral_frecuencia = 3
clientes_frecuentes = analisis_clientes[analisis_clientes['frecuencia_compras'] > umbral_frecuencia]

print("--- SEGMENTACIÓN DE CLIENTES FRECUENTES ---")
print(clientes_frecuentes.sort_values(by='gasto_acumulado', ascending=False))

## 5. Documentación del Flujo de Trabajo (Pipeline)

El flujo de trabajo implementado se articula en tres fases secuenciales que aseguran la trazabilidad y reproducibilidad del estudio:

# Extracción (Extract): Adquisición de los datos crudos desde fuentes primarias o repositorios institucionales.

# Transformación (Transform): Depuración, limpieza de nulos, tipificación y estructuración del modelo relacional en Python.

# Análisis y Carga (Load & Analyze): Ejecución de consultas de filtrado y agregación para la obtención de indicadores clave de rendimiento (KPIs).

## Referencias Bibliográficas

Corporación Universitaria del Huila (CORHUILA). (2026). Guía de Actividad Práctica: Ciencia de Datos - Semana 10 (Corte 2). Programa de Ingeniería Industrial, Neiva, Colombia.

Davenport, T. H., & Harris, ج. G. (2007). Competing on Analytics: The New Science of Winning. Harvard Business Press.

Elmasri, R., & Navathe, S. B. (2015). Fundamentals of Database Systems (7th ed.). Pearson.

McKinney, W. (2017). Python for Data Analysis: Data Wrangling with Pandas, NumPy, and IPython. O'Reilly Media.
