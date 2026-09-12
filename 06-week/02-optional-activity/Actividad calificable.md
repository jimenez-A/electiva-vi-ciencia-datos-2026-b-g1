# Actividad Semana 06 — ERD del caso

## Ciencia de Datos — Modelamiento, transformación y conexión de datos

**Programa:** Ingeniería Industrial / Ingeniería Mecatrónica
**Asignatura:** Ciencia de Datos
**Periodo:** 2026-B
**Unidad:** 2 — Modelamiento, transformación y conexión de datos
**Semana:** 6 — ERD del caso

---

# 1. Descripción del caso

El proyecto plantea un **Sistema de Mantenimiento Predictivo Industrial** orientado al seguimiento de equipos críticos de una línea de producción, especialmente motores eléctricos trifásicos.

El sistema busca almacenar y relacionar información sobre los equipos, las mediciones obtenidas mediante sensores, los eventos de mantenimiento y las órdenes generadas a partir de las predicciones del modelo de analítica.

El propósito es disponer de una estructura de datos organizada que permita posteriormente analizar el comportamiento de los equipos y apoyar decisiones de mantenimiento. En un sistema de mantenimiento predictivo, los datos de sensores como temperatura, vibración, presión o velocidad pueden utilizarse para identificar cambios en las condiciones de operación y anticipar posibles fallos. [1]

Para este caso se propone utilizar un **modelo de base de datos relacional**, debido a que existe información claramente estructurada y relaciones definidas entre los diferentes elementos del sistema.

---

# 2. Entidades principales

Para el diseño del ERD se proponen las siguientes entidades:

1. **Equipo**
2. **Sensor**
3. **Medición**
4. **Orden_Mantenimiento**
5. **Tecnico**
6. **Orden_Tecnico**

La tabla Orden_Tecnico funciona como **tabla intermedia** para representar la relación muchos a muchos (N:M) entre las órdenes de mantenimiento y los técnicos.

---

## 2.1. Entidad Equipo

La entidad **Equipo** almacena la información básica de cada máquina o activo industrial que será monitoreado.

| Campo             | Tipo    | Clave | Descripción                        |
| ------------------| ------- | ----- | ---------------------------------- |
| id_equipo         | INT     | PK    | Identificador único del equipo     |
| nombre            | VARCHAR | —     | Nombre o identificación del equipo |
| tipo              | VARCHAR | —     | Tipo de equipo                     |
| ubicacion         | VARCHAR | —     | Lugar donde se encuentra           |
| fecha_instalacion | DATE    | —     | Fecha de instalación               |
| estado            | VARCHAR | —     | Estado actual del equipo           |

### Ejemplo

Un registro podría representar un motor eléctrico trifásico instalado en una línea de producción.


id_equipo: 001
nombre: Motor Línea 1
tipo: Motor eléctrico trifásico
ubicacion: Línea de ensamblaje
estado: Operativo


---

# 2.2. Entidad Sensor

La entidad **Sensor** registra los dispositivos encargados de obtener información sobre las condiciones de funcionamiento de cada equipo.

| Campo         | Tipo    | Clave | Descripción                    |
| --------------| ------- | ----- | ------------------------------ |
| id_sensor     | INT     | PK    | Identificador único del sensor |
| id_equipo     | INT     | FK    | Equipo al que pertenece        |
| tipo_sensor   | VARCHAR | —     | Tipo de variable medida        |
| unidad_medida | VARCHAR | —     | Unidad utilizada               |
| estado        | VARCHAR | —     | Estado del sensor              |

La clave foránea id_equipo permite establecer la relación entre un equipo y sus sensores.

Un equipo puede tener varios sensores, mientras que cada sensor pertenece a un equipo específico.

**Relación:**


Equipo 1 ─────────── N Sensor


---

# 2.3. Entidad Medición

La entidad **Medición** almacena los valores obtenidos por los sensores durante el funcionamiento del equipo.

| Campo        | Tipo      | Clave | Descripción                    |
| -------------| --------- | ----- | ------------------------------ |
| id_medicion  | BIGINT    | PK    | Identificador de la medición   |
| id_sensor    | INT       | FK    | Sensor que realizó la medición |
| fecha_hora   | TIMESTAMP | —     | Momento de la medición         |
| valor        | DECIMAL   | —     | Valor registrado               |
| calidad_dato | VARCHAR   | —     | Estado o calidad del dato      |

### Ejemplo

Un sensor de temperatura podría generar registros como:


Sensor: S001
Fecha: 2026-09-11 14:30:00
Valor: 82.5
Unidad: °C


De esta forma, un único sensor puede generar muchas mediciones a lo largo del tiempo.

**Relación:**


Sensor 1 ─────────── N Medición


---

# 2.4. Entidad Orden_Mantenimiento

La entidad **Orden_Mantenimiento** registra las acciones de mantenimiento que deben realizarse sobre los equipos.

| Campo                | Tipo      | Clave | Descripción                         |
| -------------------- | --------- | ----- | ----------------------------------- |
| id_orden           | INT       | PK    | Identificador de la orden           |
| id_equipo          | INT       | FK    | Equipo relacionado                  |
| fecha_generacion`   | TIMESTAMP | —     | Fecha de generación                 |
| tipo_mantenimiento` | VARCHAR   | —     | Preventivo, predictivo o correctivo |
| prioridad`          | VARCHAR   | —     | Nivel de prioridad                  |
| probabilidad_fallo` | DECIMAL   | —     | Probabilidad estimada de fallo      |
| estado`             | VARCHAR   | —     | Estado de la orden                  |
| descripcion`        | TEXT      | —     | Descripción de la actividad         |

En el caso planteado, una orden puede generarse cuando el modelo predictivo identifica una probabilidad de fallo superior al umbral establecido para la toma de decisiones.

El mantenimiento predictivo utiliza datos operativos y monitoreo de condición para estimar cuándo un activo podría fallar y permitir que el personal de mantenimiento actúe antes de una avería. [1]

**Relación:**


Equipo 1 ─────────── N Orden_Mantenimiento


---

# 2.5. Entidad Tecnico

La entidad **Tecnico** contiene la información básica del personal encargado de ejecutar las actividades de mantenimiento.

| Campo        | Tipo    | Clave | Descripción               |
| -------------| ------- | ----- | ------------------------- |
| id_tecnico   | INT     | PK    | Identificador del técnico |
| nombre       | VARCHAR | —     | Nombre del técnico        |
| especialidad | VARCHAR | —     | Área de especialización   |
| telefono     | VARCHAR | —     | Información de contacto   |
| estado       | VARCHAR | —     | Disponibilidad            |

---

# 2.6. Entidad Orden_Tecnico

Esta entidad es la **tabla intermedia** utilizada para representar una relación **N:M** entre Orden_Mantenimiento y Tecnico.

| Campo            | Tipo    | Clave | Descripción            |
| -----------------| ------- | ----- | ---------------------- |
| id_orden         | INT     | PK/FK | Orden de mantenimiento |
| id_tecnico       | INT     | PK/FK | Técnico asignado       |
| fecha_asignacion | DATE    | —     | Fecha de asignación    |
| rol              | VARCHAR | —     | Función del técnico    |

La clave primaria está compuesta por:


(id_orden, id_tecnico)


Esto permite evitar que el mismo técnico sea registrado dos veces para una misma orden.

La utilización de dos claves foráneas en una tabla intermedia es una forma habitual de implementar relaciones muchos a muchos en un modelo relacional. [2]

---

# 3. Relaciones del modelo

Las relaciones principales del sistema son:

| Relación                            | Cardinalidad | Explicación                                                                                |
| ----------------------------------- | ------------ | ------------------------------------------------------------------------------------------ |
| Equipo — Sensor                     | 1:N          | Un equipo puede tener varios sensores                                                      |
| Sensor — Medición                   | 1:N          | Un sensor puede generar muchas mediciones                                                  |
| Equipo — Orden_Mantenimiento        | 1:N          | Un equipo puede tener varias órdenes                                                       |
| Orden_Mantenimiento — Tecnico       | N:M          | Una orden puede involucrar varios técnicos y un técnico puede participar en varias órdenes |
| Orden_Mantenimiento — Orden_Tecnico | 1:N          | Una orden puede tener varios registros de asignación                                       |
| Tecnico — Orden_Tecnico             | 1:N          | Un técnico puede aparecer en varias asignaciones                                           |

---

# 4. Diagrama ERD

El siguiente diagrama representa las entidades, claves primarias, claves foráneas y relaciones del sistema.

erDiagram
    EQUIPO {
        INT id_equipo PK
        VARCHAR nombre
        VARCHAR tipo
        VARCHAR ubicacion
        DATE fecha_instalacion
        VARCHAR estado
    }
    SENSOR {
        INT id_sensor PK
        INT id_equipo FK
        VARCHAR tipo_sensor
        VARCHAR unidad_medida
        VARCHAR estado
    }
    MEDICION {
        BIGINT id_medicion PK
        INT id_sensor FK
        TIMESTAMP fecha_hora
        DECIMAL valor
        VARCHAR calidad_dato
    }
    ORDEN_MANTENIMIENTO {
        INT id_orden PK
        INT id_equipo FK
        TIMESTAMP fecha_generacion
        VARCHAR tipo_mantenimiento
        VARCHAR prioridad
        DECIMAL probabilidad_fallo
        VARCHAR estado
        TEXT descripcion
    }
    TECNICO {
        INT id_tecnico PK
        VARCHAR nombre
        VARCHAR especialidad
        VARCHAR telefono
        VARCHAR estado
    }
    ORDEN_TECNICO {
        INT id_orden PK, FK
        INT id_tecnico PK, FK
        DATE fecha_asignacion
        VARCHAR rol
    }
    EQUIPO ||--o{ SENSOR : "posee"
    SENSOR ||--o{ MEDICION : "genera"
    EQUIPO ||--o{ ORDEN_MANTENIMIENTO : "tiene"
    ORDEN_MANTENIMIENTO ||--o{ ORDEN_TECNICO : "asigna"
    TECNICO ||--o{ ORDEN_TECNICO : "participa"



---

# 5. Explicación de la relación N:M

La relación N:M solicitada en la actividad se presenta entre las entidades:


ORDEN_MANTENIMIENTO ↔ TECNICO


Una orden de mantenimiento puede requerir la participación de varios técnicos. Por ejemplo, una intervención sobre un motor puede necesitar un técnico especializado en electricidad y otro especializado en mecánica.

Al mismo tiempo, un técnico puede participar en diferentes órdenes de mantenimiento.

Por lo tanto:


1 Orden → Muchos Técnicos
1 Técnico → Muchas Órdenes


Esto genera una relación:


N:M


En una base de datos relacional, esta relación no se almacena directamente entre las dos tablas. Se crea una tabla intermedia denominada `ORDEN_TECNICO`.


ORDEN_MANTENIMIENTO
        |
        | 1:N
        |
ORDEN_TECNICO
        |
        | N:1
        |
     TECNICO


Esta estructura permite mantener la integridad referencial y registrar información adicional sobre la asignación, como la fecha y el rol desempeñado. PostgreSQL documenta precisamente el uso de tablas intermedias con claves foráneas para implementar relaciones N:M. [2]

---

# 6. Decisión: modelo relacional o NoSQL

## Modelo seleccionado: Relacional

Para este caso se selecciona un **modelo de base de datos relacional**, utilizando potencialmente un sistema como PostgreSQL.

La decisión se fundamenta principalmente en que los datos del proyecto presentan una estructura definida y relaciones claras entre entidades.

El sistema contiene elementos como equipos, sensores, mediciones, técnicos y órdenes de mantenimiento. Cada uno posee atributos específicos y relaciones que pueden representarse mediante claves primarias y foráneas.

Por ejemplo:


Equipo
   ↓
Sensor
   ↓
Medición


y:


Equipo
   ↓
Orden de mantenimiento
   ↓
Orden_Tecnico
   ↓
Tecnico


Este tipo de estructura se adapta naturalmente al modelo relacional.

---

## 6.1. Ventajas del modelo relacional para el proyecto

### Integridad de los datos

Las claves primarias permiten identificar de forma única los registros, mientras que las claves foráneas permiten establecer relaciones válidas entre las tablas.

Una clave foránea exige que el valor almacenado corresponda con un registro válido de la tabla referenciada, ayudando a mantener la integridad referencial. [2]

### Organización

La información puede dividirse en diferentes tablas según su función:


EQUIPO
SENSOR
MEDICION
ORDEN_MANTENIMIENTO
TECNICO
ORDEN_TECNICO


Esto evita almacenar toda la información en una única tabla demasiado grande.

### Reducción de redundancia

La información de un equipo se almacena una sola vez y puede ser utilizada mediante su `id_equipo` desde otras tablas.

Por ejemplo, no sería necesario repetir:


Motor Línea 1
Línea de ensamblaje
Motor trifásico


en cada medición.

En lugar de eso, las mediciones únicamente almacenan el identificador correspondiente al sensor.

### Consultas estructuradas

El modelo relacional facilita realizar consultas para responder preguntas como:

* ¿Qué equipos presentan mayor cantidad de alertas?
* ¿Qué sensores han registrado valores anormales?
* ¿Cuántas órdenes de mantenimiento tiene cada equipo?
* ¿Qué técnicos han participado en determinadas órdenes?
* ¿Cuál fue la probabilidad de fallo registrada para una orden?

---

# 7. ¿Por qué no se seleccionó NoSQL?

Una alternativa sería utilizar una base de datos NoSQL, especialmente si se necesitara almacenar grandes cantidades de datos con estructuras muy variables.

Sin embargo, para la actividad propuesta no es la opción principal porque el caso posee entidades y relaciones previamente definidas.

Además, el objetivo de la actividad es precisamente trabajar con:

* Entidades.
* PK.
* FK.
* Relaciones.
* Relación N:M.
* Normalización.

Estos elementos corresponden directamente al diseño de una base de datos relacional.

No significa que NoSQL sea una tecnología inadecuada para sistemas industriales. En una implementación real, podría utilizarse como complemento para determinados flujos de datos de sensores de alta frecuencia. Sin embargo, para el modelo académico planteado en esta actividad, el enfoque relacional resulta más claro y apropiado.

---

# 8. Normalización aplicada

La normalización consiste en organizar los datos en estructuras relacionadas para reducir redundancias y evitar problemas de actualización.

En el diseño propuesto se aplica principalmente hasta la **Tercera Forma Normal (3FN)**.

Los fundamentos del diseño de bases de datos y la normalización incluyen las formas normales y las dependencias funcionales como parte del proceso de diseño relacional. [3]

---

## 8.1. Primera Forma Normal — 1FN

La Primera Forma Normal busca que cada atributo almacene valores individuales y no grupos repetitivos.

### Ejemplo incorrecto

Una tabla de equipos podría tener:


id_equipo | nombre | sensores
001       | Motor 1 | Temperatura, Vibración, Velocidad


El campo sensores contiene varios valores.

Esto genera una estructura poco adecuada para consultar y administrar individualmente cada sensor.

### Solución aplicada

Se separan los sensores en una entidad independiente:


EQUIPO
   |
   └── SENSOR


Así cada sensor posee su propio registro.


id_sensor | id_equipo | tipo_sensor
001       | 001       | Temperatura
002       | 001       | Vibración
003       | 001       | Velocidad


Cada campo contiene un valor individual.

---

# 8.2. Segunda Forma Normal — 2FN

La Segunda Forma Normal busca que los atributos dependan de la totalidad de la clave primaria y no solamente de una parte de una clave compuesta.

Esto es especialmente importante en la tabla intermedia ORDEN_TECNICO, cuya clave está formada por:


(id_orden, id_tecnico)


Los atributos:


fecha_asignacion
rol


corresponden a la relación entre una orden específica y un técnico específico.

Por ejemplo:


id_orden | id_tecnico | fecha_asignacion | rol
001      | 010        | 2026-09-11        | Electricista


La información de la asignación se encuentra en la tabla intermedia, mientras que los datos propios del técnico permanecen en `TECNICO`.

De esta manera se evita colocar información como:


nombre_tecnico
especialidad
telefono


dentro de ORDEN_TECNICO.

---

# 8.3. Tercera Forma Normal — 3FN

La Tercera Forma Normal busca evitar dependencias transitivas entre atributos que no forman parte de la clave.

Por ejemplo, sería incorrecto almacenar en MEDICION:


id_medicion
id_sensor
tipo_sensor
unidad_medida
valor


si tipo_sensor y unidad_medida dependen realmente de id_sensor.

En el modelo propuesto:


SENSOR
id_sensor
tipo_sensor
unidad_medida


y:


MEDICION
id_medicion
id_sensor
valor
fecha_hora


De esta forma, la información propia del sensor se almacena únicamente en la entidad `SENSOR`.

La normalización a 3FN busca precisamente reducir redundancia y evitar anomalías de actualización mediante la separación lógica de la información en tablas relacionadas. [4]

---

# 9. Ejemplo de información que se evita repetir

Sin normalización podríamos tener una tabla como:


id_medicion | equipo | ubicación | sensor | unidad | valor
001         | Motor 1| Línea 1   | Temp.  | °C     | 80
002         | Motor 1| Línea 1   | Temp.  | °C     | 82
003         | Motor 1| Línea 1   | Temp.  | °C     | 84


La información:


Motor 1
Línea 1
Temp.
°C

se repetiría constantemente.

Con el modelo normalizado, la información se distribuye:


EQUIPO
id_equipo | nombre | ubicacion
001       | Motor 1| Línea 1



SENSOR
id_sensor | id_equipo | tipo_sensor | unidad_medida
001       | 001        | Temperatura | °C



MEDICION
id_medicion | id_sensor | fecha_hora | valor
001         | 001       | ...        | 80
002         | 001       | ...        | 82
003         | 001       | ...        | 84


Así, los datos descriptivos del equipo y del sensor no necesitan repetirse en cada medición.

---

# 10. Beneficios del modelo propuesto

El modelo diseñado proporciona varios beneficios para el Sistema de Mantenimiento Predictivo:

### 1. Menor redundancia

La información se almacena en la entidad correspondiente y se reutiliza mediante claves.

### 2. Mayor integridad

Las claves primarias y foráneas permiten establecer relaciones válidas entre los registros.

### 3. Facilidad de mantenimiento

Si cambia la ubicación de un equipo, se modifica su registro en EQUIPO sin tener que modificar todas las mediciones históricas.

### 4. Escalabilidad

Es posible agregar nuevos sensores, equipos, técnicos y órdenes sin modificar completamente la estructura.

### 5. Preparación para analítica

La información organizada facilita posteriormente realizar consultas y análisis sobre:

* comportamiento de los equipos;
* evolución de variables;
* historial de mantenimiento;
* frecuencia de fallos;
* probabilidad de fallo;
* desempeño de sensores.

---

# 11. Conclusión

El diseño del ERD permite representar de manera estructurada el funcionamiento básico del Sistema de Mantenimiento Predictivo Industrial. Las entidades `EQUIPO`, `SENSOR`, `MEDICION`, `ORDEN_MANTENIMIENTO` y `TECNICO` permiten organizar los principales elementos del caso, mientras que `ORDEN_TECNICO` resuelve la relación N:M requerida entre las órdenes y los técnicos.

Se seleccionó el **modelo relacional** porque el caso presenta entidades claramente definidas, relaciones estructuradas y necesidad de mantener integridad entre los datos. El uso de claves primarias y foráneas permite identificar registros y establecer relaciones consistentes entre las diferentes tablas.

Finalmente, se aplicaron principios de normalización hasta la Tercera Forma Normal, separando información que no debe repetirse y evitando dependencias innecesarias. Esto genera una estructura más organizada, consistente y adecuada para posteriores procesos de análisis de datos y mantenimiento predictivo.

---

# 12. Referencias bibliográficas

[1] Corporación Universitaria del Huila — CORHUILA, *Actividad Práctica: Ciencia de Datos · Semana 6 · ERD de tu caso*, Unidad 2: Modelamiento, transformación y conexión de datos, Periodo 2026-B.

[2] R. Elmasri and S. B. Navathe, *Fundamentals of Database Systems*, 7th ed. Pearson, 2021.

[3] PostgreSQL Global Development Group, “5.5. Constraints — PostgreSQL 18 Documentation,” *PostgreSQL Documentation*, 2026. [En línea]. Disponible en: https://www.postgresql.org/docs/18/ddl-constraints.html

[4] IBM, “¿Qué es el mantenimiento predictivo?”, *IBM Think*, 2026. [En línea]. Disponible en: https://www.ibm.com/es-es/think/topics/predictive-maintenance

[5] C. J. Martínez Moncaleano, *Modelos estadísticos y de minería de datos en el análisis turístico de la ciudad de Neiva*. Neiva, Colombia: Editorial CORHUILA, 2025.
