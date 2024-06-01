Subconsultas Con operadores básicos de comparación

Devuelve el nombre del cliente con mayor límite de crédito.
´´´sql 
SELECT nombreCliente FROM cliente ORDER BY limiteCredito DESC LIMIT 1;
´´´
Devuelve el nombre del producto que tenga el precio de venta más caro. select nombre from producto order by precioVenta desc limit 1;

Devuelve el nombre del producto del que se han vendido más unidades. (Tenga en cuenta que tendrá que calcular cuál es el número total de unidades que se han vendido de cada producto a partir de los datos de la tabla detalle_pedido)

SELECT p.nombre AS nombre_producto FROM detallePedido dp JOIN producto p ON dp.fkIdProducto = p.id GROUP BY dp.fkIdProducto, p.nombre ORDER BY SUM(dp.cantidad) DESC LIMIT 1;

Los clientes cuyo límite de crédito sea mayor que los pagos que haya realizado. (Sin utilizar INNER JOIN). SELECT nombreCliente FROM cliente WHERE limiteCredito > ( SELECT SUM(total) FROM pago WHERE fkIdCliente = cliente.id );

Devuelve el producto que más unidades tiene en stock. select nombre from producto order by cantidadStock asc limit 1;

Devuelve el producto que menos unidades tiene en stock. select nombre from producto order by cantidadStock desc limit 1;

Devuelve el nombre, los apellidos y el email de los empleados que están a cargo de Juan Garcia . select nombre, apellido1,apellido2 from empleado where fkCodigoJefe = 1;

Subconsultas con ALL y ANY

Devuelve el nombre del cliente con mayor límite de crédito.
SELECT nombreCliente FROM cliente WHERE limiteCredito >= ALL ( SELECT limiteCredito FROM cliente );

Devuelve el nombre del producto que tenga el precio de venta más caro. SELECT nombre FROM producto WHERE precioVenta >= ALL ( SELECT precioVenta FROM producto );

Devuelve el producto que menos unidades tiene en stock. SELECT nombre FROM producto WHERE cantidadStock <= ALL ( SELECT cantidadStock FROM producto );

Subconsultas con IN y NOT IN

Devuelve el nombre, apellido1 y cargo de los empleados que no representen a ningún cliente. SELECT e.nombre, e.apellido1 FROM empleado e WHERE e.id NOT IN ( SELECT fkCodigoEmpleado FROM cliente );

Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.

SELECT nombreCliente FROM cliente WHERE id NOT IN ( SELECT fkIdCliente FROM pago );

Devuelve un listado que muestre solamente los clientes que sí han realizado algún pago. SELECT nombreCliente FROM cliente WHERE id IN ( SELECT fkIdCliente FROM pago );

Devuelve un listado de los productos que nunca han aparecido en un pedido. SELECT nombre FROM producto WHERE id NOT IN ( SELECT DISTINCT fkIdProducto FROM detallePedido );

Devuelve el nombre, apellidos, puesto y teléfono de la oficina de aquellos empleados que no sean representante de ventas de ningún cliente.

SELECT e.nombre, e.apellido1, e.apellido2, p.puesto, t.telefono FROM empleado e JOIN puesto p ON e.fkPuesto = p.id JOIN oficina o ON e.fkIdOficina = o.id JOIN telefonoOficina t ON o.id = t.fkIdOficina WHERE e.id NOT IN ( SELECT DISTINCT fkCodigoEmpleado FROM cliente );

Devuelve las oficinas donde no trabajan ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado la compra de algún producto de la gama Frutales.

Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.

SELECT nombreCliente FROM cliente WHERE id IN ( SELECT DISTINCT fkIdCliente FROM pedido ) AND id NOT IN ( SELECT DISTINCT fkIdCliente FROM pago );

Subconsultas con EXISTS y NOT EXISTS

Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago. SELECT nombreCliente FROM cliente c WHERE NOT EXISTS ( SELECT 1 FROM pago p WHERE p.fkIdCliente = c.id );

Devuelve un listado que muestre solamente los clientes que sí han realizado algún pago. SELECT nombreCliente FROM cliente c WHERE EXISTS ( SELECT 1 FROM pago p WHERE p.fkIdCliente = c.id );

Devuelve un listado de los productos que nunca han aparecido en un pedido. SELECT nombre FROM producto p WHERE NOT EXISTS ( SELECT 1 FROM detallePedido dp WHERE dp.fkIdProducto = p.id );

Devuelve un listado de los productos que han aparecido en un pedido alguna vez. SELECT DISTINCT nombre FROM producto WHERE id IN ( SELECT DISTINCT fkIdProducto FROM detallePedido );

Subconsultas correlacionadas Consultas variadas

Devuelve el listado de clientes indicando el nombre del cliente y cuántos pedidos ha realizado. Tenga en cuenta que pueden existir clientes que no han realizado ningún pedido.
SELECT c.nombreCliente, COUNT(p.id) AS pedidos_realizados FROM cliente c LEFT JOIN pedido p ON c.id = p.fkIdCliente GROUP BY c.id, c.nombreCliente;

Devuelve un listado con los nombres de los clientes y el total pagado por cada uno de ellos. Tenga en cuenta que pueden existir clientes que no han realizado ningún pago.

Devuelve el nombre de los clientes que hayan hecho pedidos en 2008 ordenados alfabéticamente de menor a mayor. SELECT DISTINCT c.nombreCliente FROM cliente c JOIN pedido p ON c.id = p.fkIdCliente WHERE YEAR(p.fechaPedido) = 2008 ORDER BY c.nombreCliente ASC;

Devuelve el nombre del cliente, el nombre y primer apellido de su representante de ventas y el número de teléfono de la oficina del representante de ventas, de aquellos clientes que no hayan realizado ningún pago. SELECT c.nombreCliente, CONCAT(e.nombre, ' ', e.apellido1) AS nombre_representante, tof.telefono AS telefono_oficina FROM cliente c LEFT JOIN empleado e ON c.fkCodigoEmpleado = e.id LEFT JOIN telefonoOficina tof ON e.fkIdOficina = tof.fkIdOficina WHERE NOT EXISTS ( SELECT 1 FROM pago p WHERE p.fkIdCliente = c.id );

Devuelve el listado de clientes donde aparezca el nombre del cliente, el nombre y primer apellido de su representante de ventas y la ciudad donde está su oficina. SELECT c.nombreCliente, CONCAT(e.nombre, ' ', e.apellido1) AS nombre_representante, ci.nombre AS ciudad_oficina FROM cliente c JOIN empleado e ON c.fkCodigoEmpleado = e.id JOIN oficina o ON e.fkIdOficina = o.id JOIN ciudad ci ON o.fkIdCiudad = ci.id;

Devuelve el nombre, apellidos, puesto y teléfono de la oficina de aquellos empleados que no sean representante de ventas de ningún cliente. SELECT e.nombre, e.apellido1, e.apellido2, p.puesto, tof.telefono FROM empleado e JOIN puesto p ON e.fkPuesto = p.id JOIN oficina o ON e.fkIdOficina = o.id JOIN telefonoOficina tof ON o.id = tof.fkIdOficina WHERE NOT EXISTS ( SELECT 1 FROM cliente c WHERE c.fkCodigoEmpleado = e.id );

Devuelve un listado indicando todas las ciudades donde hay oficinas y el número de empleados que tiene. SELECT ci.nombre AS ciudad, COUNT(e.id) AS numero_empleados FROM empleado e JOIN oficina o ON e.fkIdOficina = o.id JOIN ciudad ci ON o.fkIdCiudad = ci.id GROUP BY ci.id, ci.nombre;
