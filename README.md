# Reto SQL usando Microsoft-SQL-Server

## Un poco de historia

El día viernes 11 de septiembre del 2022 un entrevistador me planteo un reto, el mismo contaba de:

1. se tiene una tabla llamada **_stocks_** cuyos campos son:
   - id
   - product_it
   - fecha_ult_actualizacion
   - cantidad_ultima_actualizacion
2. Qué se registra en ésta tabla?
   - Cada día, se calcula la cantidad de productos que ingresan y egresan, si, la cantidad ha variado desde la última vez, se crea un nuevo registro, indicando el id del producto, la fecha de la última actualización y la cantidad que ha quedado en existencia
3. Qué se requiere:
   - Genera una contula que nos retorna la cantidad en existencia para una fecha de terminado o, en su defecto, que tome la última fecha anterior a la indicada

En la entrevista, el entrevistador propusu una consulta similar a:

```sql
select  *
        from    stocks s
        where   s.product_id = @product_id
                and s.fecha_ult_actualizacion = (select max(fecha_ult_actualizacion)
                    from    stocks s1
                    where   s1.product_id = s.product_id);
```

Esta solución, aún cuando se que retornará lo que se pide, tengo la impresión que la misma no es tan eficiente.

## Preparación del entorno de prueba

1. Se creará la tabla indicada
2. Se creará una gran cantidad de registros, un millón o más
3. Aún cuando muchos registros tendrán fecha consecutivas, deberán existir una gran cantidad de registros por producto en la cual existan espacios en blanco, es decir, cuyas fechas no serán consecutivas

## Las Pruebas

### Primera Prueba

1. Se ejecutará la consulta propuesta por el entrevistador, se revisará los tiempos y el plan de ejecución

### Segunda Prueba

- Para este caso, se creará un índice por los campos
  - product_id asc
  - fecha_ult_actualizacion desc

```sql
create unique index product_fecha_desc on stocks (product_id, fecha_ult_actualizacion desc)
```

Para poder realizar la prueba, se creará una nueva consulta en la cual no existirá una subconsulta, como se muestra

```sql
select  top 1 *
        from    stocks s
        where   s.product_id = @product_id
                and s.fecha_ult_actualizacion <= @fecha;
```

https://hub.docker.com/_/microsoft-mssql-server
