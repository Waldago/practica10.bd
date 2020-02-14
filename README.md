# practica10.bd

--               BASE DE DATOS II - PRACTICA 10 (2018)
--               ------------------------------------
--
-- CLIENTES (Nrocli,     NyApe,       Domicilio, Localidad, Saldocli)
-- FACTURAS (Nrofactura, Cliente,     Fecha)
-- DETALLES (Nrofactura, Renglón,     Articulo,  Cantidad,  Preciouni)
-- ARTICULOS(Nroartic,   Descripción, Rubro,     Stock,     Pto_reposicion, precio)
-- RUBROS   (Cod_rubro,  Descripción)
--
--------------------------------------------------------------------------------------
-- A) Hallar los clientes deudores ordenado en forma alfabetica
--
SELECT * FROM CLIENTES WHERE SALDOCLI>0
--------------------------------------------------------------------------------------
-- B) Hallar los articulos que se deberían reponer
--
SELECT DESCRIPCION FROM ARTICULOS WHERE STOCK<PTO_REPOSICION
--------------------------------------------------------------------------------------
-- C) Averiguar los clientes que viven en Capital
--
SELECT NYAPE FROM CLIENTES WHERE localidad ILIKE '%CAPITAL%'
--------------------------------------------------------------------------------------
-- D) Averiguar los clientes que vivan en Capital o en Carapachay y no sean deudores
--
SELECT * FROM CLIENTES WHERE SALDOCLI>0 AND localidad ILIKE '%CAPITAL%' OR LOCALIDAD ILIKE '%CARAPA%'
--------------------------------------------------------------------------------------
-- E) Averiguar la cantidad de cada uno de los artículos vendidos durante
--    marzo del 2010, ordenado segun la cantidad vendida.
--    (Rsta: 13 FILAS)
SELECT a.descripcion, SUM(D.CANTIDAD) 
FROM DETALLES D inner join FACTURAS F on D.NROFACTURA = F.NROFACTURA
	inner join articulos a on d.articulo=a.nroartic
WHERE F.FECHA BETWEEN '01-03-2010' AND '31-03-2010' 
GROUP BY a.descripcion
order by SUM(D.CANTIDAD) 
--------------------------------------------------------------------------------------
-- F) Hallar los importes totales día a día durante abril del 2010, ordenados en
--    forma decreciente. (Rsta: 5 filas)
SELECT F.FECHA,SUM(D.CANTIDAD*D.PRECIOUNI) FROM DETALLES D, FACTURAS F
WHERE D.NROFACTURA = F.NROFACTURA AND F.FECHA BETWEEN '01-04-2010' AND '30-04-2010'
GROUP BY F.FECHA
--------------------------------------------------------------------------------------
-- G) Obtener las fechas en las que se hayan vendido más de $200.- (Rsta: filas 9)
--
SELECT F.FECHA,SUM(D.CANTIDAD*D.PRECIOUNI) AS VENTA FROM DETALLES D, FACTURAS F
WHERE D.NROFACTURA = F.NROFACTURA GROUP BY F.FECHA HAVING SUM(D.CANTIDAD*D.PRECIOUNI)>200
--------------------------------------------------------------------------------------
-- H) Obtener las fechas en las que mas se facturo
--    (2010-03-17 - $429)
-- select f.fecha,sum (d.cantidad*d.preciouni) as suma
	from detalles d join facturas f on d.nrofactura=f.nrofactura
	group by f.fecha
	order by suma desc
	limit 1
--------------------------------------------------------------------------------------
-- I A) Averiguar los rubros con movimientos del 15 al 30 de Abril del 2010 (Rsta: 3 filas)
--select distinct r.descripcion as nom_rubro
	from detalles d inner join articulos a on a.nroartic=d.articulo
		inner join facturas f on d.nrofactura=f.nrofactura
			inner join rubros r on a.rubro=r.cod_rubro
	where f.fecha between '15/04/2010' and '30/04/2010'
--------------------------------------------------------------------------------------
-- J) Listar Numero y nombre de los cliente. Además, listar la cantidad de facturas que
--    tuvo durante Marzo y Abril del 2010 considerando SOLO las facturas en las que compro
--    articulos del rubro 3, ordenado por numero de cliente. (Rsta: 7 filas)
--select c.nrocli, c.nyape, count (distinct f.nrofactura)
	from clientes c inner join facturas f on c.nrocli=f.cliente
	INNER JOIN DETALLES D ON D.nrofactura=F.nrofactura
	INNER JOIN ARTICULOS A ON D.articulo=A.nroartic
	where f.fecha between '01/03/2010' and '30/04/2010' 
	AND A.rubro=3
	group by c.nrocli, c.nyape
	ORDER BY c.nrocli DESC
--------------------------------------------------------------------------------------
-- K) Listar Numero y nombre de los cliente y la cantidad de facturas que tuvo durante
--    Marzo y Abril del 2010 con articulos del rubro 3 y CON MAS DE 2 FACTURAS, 
--    ordenado por numero de cliente. (Rsta: 1 fila)
--select c.nrocli, c.nyape, count (distinct f.nrofactura)
	from clientes c inner join facturas f on c.nrocli=f.cliente
		INNER JOIN DETALLES D ON D.nrofactura=F.nrofactura
		INNER JOIN ARTICULOS A ON D.articulo=A.nroartic
	where f.fecha between '01/03/2010' and '30/04/2010' 
		AND A.rubro=3
	group by c.nrocli, c.nyape
	HAVING count (distinct f.nrofactura)>2
--------------------------------------------------------------------------------------
-- L) Calcular la cantidad de unidades del artículo 4040 vendidas en marzo del 2010.
--    (Rsta: 8 articulos)
--SELECT SUM (D.cantidad)
	FROM DETALLES D INNER JOIN public.facturas F ON D.nrofactura=F.nrofactura
	WHERE F.fecha BETWEEN '01/03/2010' AND '30/03/2010'
		AND D.articulo=4040
--------------------------------------------------------------------------------------
-- M) Calcular la cantidad de facturas en las que vendieron artículos del rubro 3
--    (Rsta: 26 facturas)
--SELECT COUNT (DISTINCT F.nrofactura)
	FROM DETALLES D INNER JOIN public.facturas F ON D.nrofactura=F.nrofactura
		INNER JOIN ARTICULOS A ON D.articulo=A.nroartic
	WHERE A.rubro=3
--------------------------------------------------------------------------------------
-- N) Obtener el promedio del importe diario de las ventas del mes de mayo del 2010.
--SELECT F.fecha, AVG (D.cantidad*D.preciouni)
	FROM DETALLES D INNER JOIN public.facturas F ON D.nrofactura=F.nrofactura
	GROUP BY F.fecha
	HAVING F.fecha BETWEEN '01/05/2010' AND '30/05/2010'
--------------------------------------------------------------------------------------
-- O) Listar el numero, nombre, apellido y el total facturado de los clientes que 
--    hayan gastado más de $150 durante el mes de mayo de 2010.
--SELECT C.nrocli, C.nyape, SUM (D.cantidad*D.preciouni)
	FROM DETALLES D INNER JOIN public.facturas F ON D.nrofactura=F.nrofactura
		inner join clientes c on c.nrocli=f.cliente
	GROUP BY C.nrocli, C.nyape,F.fecha
	HAVING SUM (D.cantidad*D.preciouni)>150
		AND F.fecha BETWEEN '01/05/2010' AND '30/05/2010'
--------------------------------------------------------------------------------------
-- P) Listar los articulos que tengan un stock menor al stock minimo y NO se hayan
--    vendido en Junio del 2010
--    Resultado: 1 fila
--SELECT DISTINCT A.nroartic, A.descripcion
FROM public.articulos A INNER JOIN public.detalles D ON D.articulo=A.nroartic
	INNER JOIN public.facturas F ON D.nrofactura=F.nrofactura
WHERE A.stock<A.pto_reposicion
	AND NOT EXISTS (SELECT DISTINCT A2.nroartic
						FROM public.articulos A2 INNER JOIN public.detalles D2 ON D2.articulo=A2.nroartic
								INNER JOIN public.facturas F2 ON D2.nrofactura=F2.nrofactura
						WHERE F2.fecha BETWEEN '01/06/2010' AND '30/06/2010'
						AND A.nroartic=A2.nroartic)
--------------------------------------------------------------------------------------
-- Q) Listar los artículos que tengan un precio mayor a la mitad del precio
--    promedio de los artículos y un stock mínimo mayor a 200 unidades. (6 filas)
--SELECT A.descripcion, A.precio
	FROM public.articulos A
	WHERE A.precio > (SELECT AVG(A2.precio)/2
		  		FROM public.articulos A2 )
	AND A.stock>200
--------------------------------------------------------------------------------------
-- R) Listar los datos cabecera de las Facturas de mas de $200 y que la cantidad
--    de artículos facturados en la misma sea mayor a 30 
--    (Rsta: 4 filas)
--SELECT F.*,SUM (D.articulo*D.cantidad) AS TOTAL_FAC, SUM (cantidad)
	FROM public.facturas F INNER JOIN public.detalles D ON F.nrofactura=D.nrofactura
	WHERE (D.articulo*D.cantidad)>200
	GROUP BY F.nrofactura
	HAVING SUM (cantidad)>30
----------------------------------------------------------------------------------------
-- S) Listar los datos cabecera de las Facturas de mas de $200 y que la cantidad
--    de artículos facturados en la misma sea mayor al 5% del stock promedio
--    de los rubros "Herramienta%"
--    (Rsta: 5 FILAS)
--
SELECT F.*,SUM (D.cantidad*D.preciouni) AS TOTAL_FACT,SUM (D.cantidad)AS TOTAL_CANT 
FROM public.facturas F INNER JOIN public.detalles D ON F.nrofactura=D.nrofactura 
	INNER JOIN public.articulos A ON D.articulo=A.nroartic 
	INNER JOIN public.rubros R ON A.rubro=R.cod_rubro 
GROUP BY F.nrofactura 
HAVING SUM (D.cantidad*D.preciouni)>200 
	AND (SUM (D.cantidad))>(SELECT (AVG(a.stock))*0.05 
				FROM public.articulos a inner join public.rubros r on A.rubro=R.cod_rubro
				where R.descripcion ILIKE 'HERRAMIENTA%')
--------------------------------------------------------------------------------------
-- T) a) Listar los clientes compraron TODOS los articulos (1 fila - cliente 109)
--    b) Listar los clientes que en Junio compraron TODOS los articulos del rubro
--       "Tornillos". (2 filas - clientes 160 - 304)
--a)--select c.nyape, count (distinct nroartic)
from public.clientes c inner join public.facturas f on c.nrocli=f.cliente
	inner join public.detalles d on d.nrofactura=f.nrofactura
	inner join public.articulos a on d.articulo=a.nroartic
group by c.nyape
having count (distinct nroartic)=(select count(distinct nroartic)
from public.articulos)
b)select c.nyape,c.nrocli
from public.clientes c inner join public.facturas f on c.nrocli=f.cliente 
	inner join public.detalles d on d.nrofactura=f.nrofactura 
	inner join public.articulos a on d.articulo=a.nroartic 
	inner join public.rubros r on a.rubro=r.cod_rubro 
where r.descripcion ilike 'tornillos' 
	and f.fecha between '01/06/2010' and '30/06/2010'
group by c.nyape,c.nrocli
having count (a.nroartic)=(select count (a.nroartic)
		from public.articulos a inner join public.rubros r on a.rubro=r.cod_rubro
			where r.descripcion ilike 'tornillos')

--------------------------------------------------------------------------------------
-- U) Aumentar un 10% el stock mínimo de los artículos del rubro 'Tornillos'
--update public.articulos a set pto_reposicion=pto_reposicion+(pto_reposicion*0.1) where a.rubro=2
--------------------------------------------------------------------------------------
-- V) Agregar la columna color (alfabetico) a la tabla ARTICULOS
-- alter table public.articulos add column color varchar;
--------------------------------------------------------------------------------------
-- W) Dar permisos de lectura y actualizacion a los usuarios 'SIST' y 'ADMIN'
--    sobre la tabla ARTICULOS
--
--------------------------------------------------------------------------------------
-- X) Generar una view con los articulos del rubro "Clavos"
--
--------------------------------------------------------------------------------------
-- Y) Listar los clientes de 'Capital Federal' que NO compraron articulos del
--    rubro 'Articulos de Electricidad' durante mayo de año 2010.
--    Ordenar los datos en forma descendente por nombre y apellido.
--    (Rsta: 10 filas)
--SELECT DISTINCT C.*
FROM public.clientes C 
WHERE C.LOCALIDAD ILIKE 'CAP%'
	AND NOT EXISTS(SELECT C.*
				FROM public.clientes C2 INNER JOIN public.facturas F2 ON C2.NROCLI=F2.CLIENTE
					INNER JOIN public.detalles D2 ON F2.NROFACTURA=D2.NROFACTURA
					INNER JOIN public.articulos A2 ON D2.ARTICULO=A2.NROARTIC
					INNER JOIN public.rubros R ON A2.RUBRO=R.COD_RUBRO
				WHERE R.DESCRIPCION ILIKE 'ARTICULOS DE ELECTRICIDAD'
					AND F2.FECHA BETWEEN '01/05/2010' AND '31/05/2010'
					AND C2.NROCLI=C.NROCLI)
ORDER BY C.NYAPE
--------------------------------------------------------------------------------------
-- Z) Listar los rubros (y la cantidad de articulos vendidos) de cada rubro que haya
--    vendido mas de 30 articulos.
-- select r.descripcion, sum (d.cantidad)
from public.detalles d inner join public.articulos a on d.articulo=a.nroartic
	inner join public.rubros r on a.rubro=r.cod_rubro
group by r.descripcion
having sum (d.cantidad) > 30
--------------------------------------------------------------------------------------
-- 1) Generar una view, FACT-CAB (Nrofactura, Cliente, Fecha, TOTAL)

--
--------------------------------------------------------------------------------------
-- 2) Agregar la columna TOTAL en la tabla FACTURAS. Actualizar la misma con el total

--    segun corresponda.
--
--------------------------------------------------------------------------------------
-- 3) Generar un unico Select con el o los articulos mas caros y con el o los mas baratos


--
--------------------------------------------------------------------------------------
-- 4) Listar los datos de los clientes que no compraron ningun articulo

--
--------------------------------------------------------------------------------------
-- 5) Listar las localidades que vendieron mas de que lo que se le facturo al cliente 179

--select c.localidad
from public.detalles d inner join public.facturas f on d.nrofactura=f.nrofactura
	inner join public.clientes c on f.cliente=c.nrocli
group by c.localidad
having sum (d.cantidad*d.preciouni)>(select sum(d2.cantidad*d2.preciouni)
					from public.detalles d2 inner join public.facturas f2 on d2.nrofactura=f2.nrofactura
						where f2.cliente=179)
--------------------------------------- Fin Practica10 --------------------------------
