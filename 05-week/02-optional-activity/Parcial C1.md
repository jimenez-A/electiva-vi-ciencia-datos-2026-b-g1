# 📊 Caso de Estudio: Análisis de Datos en una Cafetería

## 👤 Información del estudiante

**Nombre:** Ana Sofía Jiménez Ostos  
**Usuario de GitHub:** Jimenez-A  
**Programa:** Ingeniería Mecatrónica / Ciencia de Datos  
**Institución:** Corporación Universitaria del Huila — CORHUILA  
**Área:** Ciencia de Datos  

---

## ☕ 1. Descripción del caso

Para este ejercicio se selecciona como caso de estudio una **cafetería**, negocio que genera diferentes tipos de información diariamente a partir de sus ventas, pedidos, clientes y productos.

El análisis de estos datos permite conocer el comportamiento histórico del negocio y utilizar la información disponible para apoyar la toma de decisiones estratégicas, optimizar el inventario y mejorar la experiencia del cliente.

Entre los datos que genera la cafetería se encuentran los registros de ventas, pedidos digitales, comentarios de los clientes y fotografías de los productos.

---

## 📋 2. Tipos de datos y clasificación

| Tipo de dato | Ejemplo | Clasificación |
|---|---|---|
| Registros de ventas | Fecha, producto, cantidad, precio y total | **Estructurado** |
| Pedidos digitales | Información almacenada en formato JSON | **Semiestructurado** |
| Comentarios de clientes | Opiniones y reseñas escritas | **No estructurado** |
| Fotografías de productos | Imágenes de cafés, postres y alimentos | **No estructurado** |

### 🔹 Datos estructurados

Los registros de ventas se almacenan en tablas relacionales con columnas y filas predefinidas. Por ejemplo:

| Fecha | Producto | Cantidad | Precio | Total |
|---|---|---:|---:|---:|
| 01/09/2026 | Café americano | 5 | $4.000 | $20.000 |
| 01/09/2026 | Capuchino | 8 | $6.000 | $48.000 |
| 01/09/2026 | Croissant | 4 | $5.000 | $20.000 |

Este tipo de información es fácil de consultar y analizar mediante bases de datos relacionales (SQL).

### 🔹 Datos semiestructurados

Los pedidos realizados mediante plataformas digitales utilizan formatos como **JSON**, donde la información posee etiquetas y una estructura flexible sin requerir un esquema relacional rígido.

Ejemplo:

json
{
  "pedido": 105,
  "producto": "Capuchino",
  "cantidad": 2,
  "precio": 6000
}
### 🔹 Datos no estructurados

* **Comentarios de clientes:** Las opiniones y reseñas no tienen una estructura fija, conteniendo texto libre con diversas expresiones cualitativas.
* **Fotografías de productos:** Archivos visuales en formatos como `.jpg` o `.png` que contienen información no organizada en filas y columnas.

---

## 📈 3. Preguntas de analítica

El análisis de los datos de la cafetería se aborda mediante dos enfoques analíticos principales:

### 🔵 3.1 Analítica descriptiva

Examina la información histórica para comprender qué ocurrió en el negocio (*¿Qué sucedió?*).

#### Pregunta de analítica descriptiva:
> **¿Cuáles fueron los productos más vendidos y cuáles generaron mayores ingresos durante el último mes?**

#### Métricas asociadas:
- Cantidad total de productos vendidos.
- Ingresos generados por cada producto.
- Producto con mayor número de ventas.
- Producto con mayores ingresos.
- Distribución de ventas por día o semana.

#### Ejemplo ilustrativo:

| Producto | Cantidad vendida | Ingresos |
|---|---:|---:|
| Café americano | 120 | $480.000 |
| Capuchino | 150 | $900.000 |
| Croissant | 80 | $400.000 |

---

### 🟠 3.2 Analítica predictiva

Aplica técnicas estadísticas y algoritmos de aprendizaje automático sobre datos históricos para estimar eventos futuros (*¿Qué podría ocurrir?*).

#### Pregunta de analítica predictiva:
> **¿Qué producto tendrá mayor demanda durante la próxima semana?**

#### Variables de entrada para el modelo:
- Historial de ventas cuantitativo.
- Día de la semana y estacionalidad.
- Horarios de mayor afluencia.
- Comportamiento y preferencias de los clientes.

---

### 🔄 Diferencia entre ambas analíticas

| Analítica descriptiva | Analítica predictiva |
|---|---|
| Analiza información histórica. | Estima posibles resultados futuros. |
| Responde qué ocurrió. | Responde qué podría ocurrir. |
| Utiliza datos del pasado. | Utiliza datos históricos para realizar predicciones. |
| Permite identificar tendencias. | Permite anticipar posibles comportamientos. |

---

### 🇺🇸 3.3 Explanation in English

> **Descriptive analytics explains what happened in the past by summarizing and visualizing historical data.**
> 
> **Predictive analytics uses historical data, statistics, and machine learning techniques to estimate what may happen in the future.**

🏗️ 4. Diagrama sencillo del proceso de datos
┌──────────────────────┐
│       FUENTE         │
│                      │
│ • Ventas             │
│ • Pedidos digitales  │
│ • Comentarios        │
│ • Fotografías        │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│    ALMACENAMIENTO    │
│                      │
│ • Base de datos      │
│ • Archivos JSON      │
│ • Almacenamiento     │
│   de imágenes        │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│       ANÁLISIS       │
│                      │
│ • Analítica          │
│   descriptiva        │
│ • Analítica          │
│   predictiva         │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│    VISUALIZACIÓN     │
│                      │
│ • Gráficos           │
│ • Tablas             │
│ • Indicadores        │
│ • Dashboards         │
└──────────────────────┘
## 🔎 5. Interpretación del flujo

1. **Fuente:** La cafetería captura datos continuos de sus operaciones diarias: transacciones en caja, peticiones digitales en formato JSON, comentarios de usuarios y registros visuales.
2. **Almacenamiento:** Los datos se canalizan según su estructura: bases de datos relacionales para las ventas, repositorios de documentos para archivos JSON y almacenamiento de objetos para imágenes.
3. **Análisis:** Se procesa la información para obtener resúmenes históricos mediante agregaciones cuantitativas y se entrenan modelos predictivos para proyectar la demanda.
4. **Visualización:** Los resultados procesados se presentan en tableros de control (*dashboards*), gráficos e indicadores clave de rendimiento (KPIs) para facilitar la toma de decisiones.

---

## ✅ 6. Conclusión

El caso de estudio de la cafetería demuestra de forma práctica cómo los datos generados en el entorno comercial (estructurados, semiestructurados y no estructurados) pueden ser capturados, almacenados y analizados sistemáticamente.

La integración de la **analítica descriptiva** para comprender el desempeño pasado y de la **analítica predictiva** para anticipar la demanda futura permite estructurar un flujo continuo (**Fuente → Almacenamiento → Análisis → Visualización**), transformando datos primarios en información estratégica para la gestión de inventario, ventas y planificación operativa.

---

## 📚 7. Referencias bibliográficas

* [1] IBM, "What Is Unstructured Data?", *IBM Think*, 2025. [En línea]. Disponible en: https://www.ibm.com/think/topics/unstructured-data
* [2] IBM, "Structured vs. Unstructured Data: What's the Difference?", *IBM Think*, 2025. [En línea]. Disponible en: https://www.ibm.com/think/topics/structured-vs-unstructured-data
* [3] IBM, "What Is AI Analytics?", *IBM Think*, 2024. [En línea]. Disponible en: https://www.ibm.com/think/topics/ai-analytics
* [4] IBM, "¿Qué es el análisis de Big Data?", *IBM Think*, 2024. [En línea]. Disponible en: https://www.ibm.com/es-es/think/topics/big-data-analytics
* [5] Provost, F., & Fawcett, T. (2013). *Data Science for Business: What you need to know about data mining and data-analytic thinking*. O'Reilly Media.
