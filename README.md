[Descripcion m4_consultas_negocio.txt](https://github.com/user-attachments/files/32140872/Descripcion.m4_consultas_negocio.txt)
- Base de Datos Ventas_Tech_DB

- Descripción
Este proyecto contiene el script SQL para la creación y población de una base de datos relacional orientada al análisis de ventas de retail tecnológico. Incluye la definición del esquema normalizado (DDL) y la inserción de registros iniciales de prueba (DML).
Asimismo, incluye consultas realizadas para la ejecución de las actividades propuestas en clases.

- Estructura del modelo de datos
El modelo cuenta con 4 tablas principales:

*Categorias: 'id_categoria' (PK), 'nombre_categoria', 'descripcion';
*Clientes: 'id_cliente' (PK), 'nombre', 'email', 'ciudad', 'fecha_registro';
*Productos: 'id_producto' (PK), 'nombre_producto', 'precio', 'stock', 'activo', 'id_categoria' (FK -> Categorias)
*Ventas: 'id_venta' (PK), 'id_cliente' (FK -> Clientes), 'id_producto' (FK -> Productos), 'cantidad', 'precio_unitario', 'fecha_venta';


CONSULTAS:

-- Consulta 1 — Resumen ejecutivo mensual

SELECT
MONTH (fecha_venta) AS mes,
SUM (cantidad*precio_unitario) AS total_facturado,
COUNT (id_venta) AS cantidad_de_pedidos,
AVG (cantidad*precio_unitario) AS ticket_promedio
FROM ventas
GROUP BY MONTH (fecha_venta)

Esta consulta suma el total facturado, la cantidad de ventas y calcula el ticket promedio, agrupando por mes. 

-- Consulta 2 — Ranking de productos

SELECT 
TOP 5
id_producto,
SUM (cantidad) AS unidades_vendidas,
SUM (cantidad*precio_unitario) AS total_facturado
FROM ventas
GROUP BY id_producto
ORDER BY total_facturado DESC

Esta consulta muestra los 5 productos de mayores ventas, indicando el id del producto, la cantidad de unidades vendidas y el monto total de venta de cada producto. Ordenando la tabla de forma descendente por el monto total facturado.

-- Consulta 3 — Clientes recurrentes

SELECT
id_cliente,
COUNT (id_venta) AS cantidad_de_pedidos,
SUM (cantidad*precio_unitario) AS total_gastado
FROM ventas
GROUP BY id_cliente
HAVING COUNT(id_cliente) > 1
ORDER BY total_gastado DESC

Lo que hace esta consulta es mostrar el id de los 5 clientes que realizaron compras por mayor monto de dinero, mostrando el total de los pedidos por cliente y monto total gastado. La tabla se ordena de forma descendente por el total gastado.

-- Consulta 4 — Meses por encima/por debajo del promedio

SELECT
MONTH(fecha_venta) AS mes,
SUM(cantidad * precio_unitario) AS total_facturado,
CASE
WHEN SUM(cantidad * precio_unitario) > (
SELECT AVG(total_mensual)
FROM (
SELECT SUM(cantidad * precio_unitario) AS total_mensual
FROM ventas
GROUP BY MONTH(fecha_venta)
) AS promedio_meses
) THEN 'Por encima'
ELSE 'Por debajo'
END AS promedio_venta
FROM ventas
GROUP BY MONTH(fecha_venta)
ORDER BY mes;

La función de esta tabla es calcular el monto total vendido por mes, haciendo una comparación con el monto promedio de venta mensual, y mostrando si el monto de cada mes está por arriba o por debajo del promedio. 



