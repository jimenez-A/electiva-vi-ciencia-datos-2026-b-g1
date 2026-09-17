# Actividad Práctica – Semana 7
## Consultas SQL y pandas

**Asignatura:** Ciencia de Datos  
**Programa:** Ingeniería Industrial  
**Periodo:** 2026-B  
**Unidad:** Unidad 2 – Modelamiento, transformación y conexión de datos  


## 1. Introducción

Las consultas SQL permiten realizar operaciones de selección, filtrado, combinación y
agrupación sobre datos almacenados en bases de datos relacionales. En esta actividad
se trabaja con un conjunto de datos correspondiente a las ventas de una tienda,
utilizando las cláusulas WHERE, JOIN y GROUP BY.

De acuerdo con la documentación oficial de PostgreSQL, SQL permite realizar consultas
sobre los datos almacenados en tablas y utilizar diferentes operaciones para obtener
información específica de una base de datos [1].

Además, se utiliza la biblioteca **pandas** de Python para reproducir mediante
groupby() el análisis realizado con GROUP BY en SQL. La función
DataFrame.groupby() permite agrupar los datos de un DataFrame y posteriormente
aplicar operaciones de agregación sobre dichos grupos [2].

---

# 2. Dataset utilizado

Para desarrollar la actividad se plantea un conjunto de datos sencillo correspondiente
a las ventas de una tienda de productos electrónicos.

El dataset está compuesto por tres tablas:

- clientes
- productos
- ventas

## 2.1 Tabla `clientes`

| id_cliente | nombre | ciudad |
|------------|--------|--------|
| 1 | Ana | Neiva |
| 2 | Carlos | Bogotá |
| 3 | María | Neiva |
| 4 | Juan | Medellín |
| 5 | Laura | Bogotá |

## 2.2 Tabla `productos`

| id_producto | producto | categoria | precio |
|-------------|----------|-----------|--------|
| 101 | Motor DC | Electrónica | 85000 |
| 102 | Sensor ultrasónico | Electrónica | 35000 |
| 103 | Protoboard | Electrónica | 28000 |
| 104 | Multímetro | Instrumentación | 95000 |
| 105 | Cable jumper | Electrónica | 12000 |

## 2.3 Tabla `ventas`

| id_venta | id_cliente | id_producto | cantidad |
|----------|------------|-------------|----------|
| 1 | 1 | 101 | 2 |
| 2 | 2 | 102 | 3 |
| 3 | 1 | 103 | 2 |
| 4 | 3 | 104 | 1 |
| 5 | 4 | 101 | 1 |
| 6 | 5 | 105 | 5 |
| 7 | 3 | 102 | 2 |
| 8 | 2 | 103 | 1 |

---

# 3. Consulta SQL con WHERE

La primera consulta utiliza la cláusula WHERE para filtrar los productos cuyo precio
sea superior a 50.000 pesos colombianos.

``sql
SELECT *
FROM productos
WHERE precio > 50000;
Explicación
La cláusula WHERE permite establecer una condición para seleccionar únicamente los
registros que cumplen con un determinado criterio.
En este caso, la condición:

precio > 50000

permite obtener únicamente los productos cuyo precio sea superior a 50.000.
Resultado esperado
| id_producto | producto | categoria | precio |
|-------------|----------|-----------|--------|
| 101 | Motor DC | Electrónica | 85000 |
| 104 | Multímetro | Instrumentación | 95000 |

Por lo tanto, la consulta permite identificar los productos con un precio superior al
valor establecido.

# 4. Consulta SQL con JOIN
La segunda consulta utiliza JOIN para relacionar las tablas ventas, clientes y
productos.
SELECT 
    v.id_venta,
    c.nombre AS cliente,
    p.producto,
    v.cantidad,
    p.precio,
    v.cantidad * p.precio AS total
FROM ventas v
JOIN clientes c 
    ON v.id_cliente = c.id_cliente
JOIN productos p 
    ON v.id_producto = p.id_producto;
Explicación
La operación JOIN permite combinar registros provenientes de diferentes tablas
utilizando una condición de relación entre ellas [1].
En esta consulta se relaciona:
- La tabla ventas con clientes mediante id_cliente.
- La tabla ventas con productos mediante id_producto.
También se calcula el valor total de cada venta mediante la operación:

cantidad × precio

| id_venta | cliente | producto | cantidad | precio | total |
|----------|---------|----------|----------|--------|-------|
| 1 | Ana | Motor DC | 2 | 85000 | 170000 |
| 2 | Carlos | Sensor ultrasónico | 3 | 35000 | 105000 |
| 3 | Ana | Protoboard | 2 | 28000 | 56000 |
| 4 | María | Multímetro | 1 | 95000 | 95000 |
| 5 | Juan | Motor DC | 1 | 85000 | 85000 |
| 6 | Laura | Cable jumper | 5 | 12000 | 60000 |
| 7 | María | Sensor ultrasónico | 2 | 35000 | 70000 |
| 8 | Carlos | Protoboard | 1 | 28000 | 28000 |

Esta consulta permite conocer qué cliente realizó cada compra, qué producto adquirió,
la cantidad comprada, el precio unitario y el valor total de la operación.

# 5. Consulta SQL con GROUP BY
La tercera consulta utiliza GROUP BY para agrupar las ventas de acuerdo con la
categoría de los productos.

SELECT 
    p.categoria,
    SUM(v.cantidad) AS unidades_vendidas,
    SUM(v.cantidad * p.precio) AS ingresos_totales
FROM ventas v
JOIN productos p
    ON v.id_producto = p.id_producto
GROUP BY p.categoria;

Explicación
La cláusula GROUP BY permite agrupar los registros que tienen valores iguales en una
o varias columnas. En combinación con funciones de agregación, permite obtener
información resumida de los datos [1].
En esta consulta se utiliza:
- SUM(v.cantidad) para calcular las unidades vendidas.
- SUM(v.cantidad * p.precio) para calcular los ingresos totales.
- GROUP BY p.categoria para agrupar los resultados por categoría.

Resultado esperado

| categoria | unidades_vendidas | ingresos_totales |
|-----------|-------------------:|-----------------:|
| Electrónica | 15 | 504000 |
| Instrumentación | 1 | 95000 |

Por medio de esta consulta es posible identificar la cantidad de unidades vendidas y
los ingresos generados por cada categoría de productos.

# 6. Equivalente de GROUP BY en pandas
La consulta anterior puede reproducirse utilizando la biblioteca pandas de Python.
La función DataFrame.groupby() permite dividir los datos en grupos según una o más
columnas y aplicar operaciones posteriores sobre dichos grupos [2].
import pandas as pd

# Datos de productos
productos = pd.DataFrame({
    "id_producto": [101, 102, 103, 104, 105],
    "producto": [
        "Motor DC",
        "Sensor ultrasónico",
        "Protoboard",
        "Multímetro",
        "Cable jumper"
    ],
    "categoria": [
        "Electrónica",
        "Electrónica",
        "Electrónica",
        "Instrumentación",
        "Electrónica"
    ],
    "precio": [85000, 35000, 28000, 95000, 12000]
})

# Datos de ventas
ventas = pd.DataFrame({
    "id_venta": [1, 2, 3, 4, 5, 6, 7, 8],
    "id_cliente": [1, 2, 1, 3, 4, 5, 3, 2],
    "id_producto": [101, 102, 103, 104, 101, 105, 102, 103],
    "cantidad": [2, 3, 2, 1, 1, 5, 2, 1]
})

# Relacionar las ventas con los productos
df = ventas.merge(
    productos,
    on="id_producto",
    how="inner"
)

# Calcular el valor total de cada venta
df["total"] = df["cantidad"] * df["precio"]

# Agrupar por categoría
resultado = df.groupby("categoria").agg(
    unidades_vendidas=("cantidad", "sum"),
    ingresos_totales=("total", "sum")
).reset_index()

print(resultado)

# Datos de ventas
ventas = pd.DataFrame({
    "id_venta": [1, 2, 3, 4, 5, 6, 7, 8],
    "id_cliente": [1, 2, 1, 3, 4, 5, 3, 2],
    "id_producto": [101, 102, 103, 104, 101, 105, 102, 103],
    "cantidad": [2, 3, 2, 1, 1, 5, 2, 1]
})

# Relacionar las ventas con los productos
df = ventas.merge(
    productos,
    on="id_producto",
    how="inner"
)

# Calcular el valor total de cada venta
df["total"] = df["cantidad"] * df["precio"]

# Agrupar por categoría
resultado = df.groupby("categoria").agg(
    unidades_vendidas=("cantidad", "sum"),
    ingresos_totales=("total", "sum")
).reset_index()

print(resultado)

Resultado esperado

  categoria  unidades_vendidas  ingresos_totales
0       Electrónica                 15            504000
1  Instrumentación                  1             95000

El resultado obtenido mediante pandas es equivalente al obtenido mediante la consulta
SQL con GROUP BY. Para ello se utiliza primero merge() para relacionar las tablas
y posteriormente groupby() junto con agg() para realizar las operaciones de
agregación.

# 7. Comparación entre SQL y pandas
| Operación | SQL | pandas |
|-----------|-----|--------|
| Filtrar datos | WHERE | loc[] / condiciones |
| Relacionar datos | JOIN | merge() |
| Agrupar datos | GROUP BY | groupby() |
| Sumar valores | SUM() | sum() |
| Agregar resultados | Funciones SQL | agg() |

Esta comparación muestra que SQL y pandas proporcionan mecanismos diferentes para
realizar operaciones similares sobre datos. SQL está orientado principalmente a la
consulta y manipulación de datos dentro de bases de datos, mientras que pandas
proporciona estructuras y herramientas para el análisis de datos en Python [2].

# 8. Conclusiones
1. La cláusula WHERE permite realizar filtros sobre los registros de una tabla,
   facilitando la selección de información que cumple condiciones específicas.
2. La operación JOIN permite combinar información almacenada en diferentes tablas
   mediante campos relacionados, haciendo posible realizar análisis sobre datos
   distribuidos en varias estructuras.
3. La cláusula GROUP BY, acompañada de funciones de agregación como SUM(),
   permite obtener información resumida y organizada por categorías.
4. La función groupby() de pandas permite reproducir análisis de agrupación
   realizados originalmente en SQL, proporcionando una alternativa para trabajar con
   los datos desde Python [2].
5. El ejercicio permite relacionar las consultas SQL con herramientas de análisis de
   datos, fortaleciendo el proceso de transformación y análisis de información
   estructurada.
   
# 9. Referencias bibliográficas
[1] PostgreSQL Global Development Group, PostgreSQL Documentation: SQL Commands,
PostgreSQL, 2026. [En línea]. Disponible en:
https://www.postgresql.org/docs/current/sql-commands.html
[2] The pandas development team, pandas.DataFrame.groupby — pandas documentation,
pandas documentation, 2026. [En línea]. Disponible en:
https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.groupby.html
[3] The pandas development team, pandas documentation, pandas, 2026.
[En línea]. Disponible en:
https://pandas.pydata.org/docs/
