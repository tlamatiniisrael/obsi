
eSTA DECLARACION TIENE MENSUAL Y cERO.

## Declaración Mensual

```sql
sql += "INSERT INTO  " + INT_DECLARACION;  
sql += "                            ( FOLIO,\n";  
sql += "                              REC,\n";  
sql += "                              EJERCICIO,\n";  
sql += "                              PERIODO,\n";  
sql += "                              TIPODECLARACION,\n";  
sql += "                              TOTALAFAVOR,\n";  
sql += "                              TOTALAPAGAR,\n";  
sql += "                              FECHAREGISTRO,\n";  
sql += "                              ID_ESTADODEC,\n";  
sql += "                              FOLIO_PAGOANT,\n";  
sql += "                              FECHA_PAGOANT,\n";  
sql += "                              CREFERENCIA,\n";   //revisa las comas perro  
sql += "                              IMPUESTOACARGO,\n";  
sql += "                              ACTUALIZACIONES,\n";  
sql += "                              RECARGOS,\n";  
sql += "                              IMPUESTOFAVOR,\n";  
sql += "                              ACTUALIZACIONFAVOR,\n";  
sql += "                              RECARGOFAVOR\n";
```



## Declaración Cero

```sql
sql = "INSERT INTO INT_DECLARACION_CERO (";  
sql += "   FOLIO, FECHA_REG, REC, ";  
sql += "   IDEJERCICIO, IDPERIODO, IDTIPODEC,ID_CAT_DEC_CERO, MOTIVO ) ";  
sql += "VALUES (?,SYSDATE,?,?,?,?,?,?)";
```

pROPUESTA DE CONSULTA FINAL

```SQL
SELECT  
    cat.PERIODO,       -- Año  
    cat.EJERCICIO,     -- Mes / Periodo  
    cat.FECHAHABIL     -- Fecha límite de pago  
FROM inc_calvigencias cat  
         LEFT JOIN (  
    --- SUBCONSULTA: PAGOS REALIZADOS (Optimizada) ---  
    SELECT DISTINCT PERIODO, EJERCICIO  
    FROM (  
             -- Parte 1: Declaraciones normales  
             SELECT distinct D.EJERCICIO+2000 AS PERIODO, D.PERIODO AS EJERCICIO  
             FROM INT_DECLARACION D  
                      INNER JOIN IT_SERVICIOS S ON (D.CREFERENCIA = S.REFERENCIA)  
                      INNER JOIN IT_CARGABANCO B ON (S.REFERENCIA = B.REFERENCIA and B.CLAVEESTADOCARGA = 2 OR B.CLAVEESTADOCARGA = 8)  
                      INNER JOIN INC_CALVIGENCIAS A  
                                 ON A.PERIODO = (D.EJERCICIO + 2000)  
                                     AND A.EJERCICIO = D.PERIODO  
                                     AND A.CLAVETIPOOPERSERV = 42  
             WHERE D.REC = '00000522586'  -- <--- Movido aquí adentro para mejor rendimiento  
               AND D.ID_ESTADODEC =3  
               AND D.EJERCICIO > 13  
               AND D.FECHAREGISTRO >= TIMESTAMP '2016-01-01 00:00:00'  
               AND D.PERIODO NOT IN (13,22)  
             UNION ALL  
             -- Parte 2: Declaraciones en cero  
             SELECT D.IDEJERCICIO+2000 AS PERIODO, D.IDPERIODO AS EJERCICIO  
             FROM INT_DECLARACION_CERO D  
                      INNER JOIN INC_CALVIGENCIAS A  
                                 ON A.PERIODO = (D.IDEJERCICIO + 2000)  
                                     AND A.EJERCICIO = D.IDPERIODO  
                                     AND A.CLAVETIPOOPERSERV = 42  
             WHERE D.REC = '00000522586'  -- <--- Movido aquí adentro para mejor rendimiento  
               AND D.IDEJERCICIO > 13  
               AND D.FECHA_REG >= TIMESTAMP '2016-01-01 00:00:00'  
               AND D.IDPERIODO NOT IN (13,22)  
         )  
) pagos  
                   ON cat.PERIODO = pagos.PERIODO  
                       AND cat.EJERCICIO = pagos.EJERCICIO  
WHERE cat.CLAVETIPOOPERSERV = 42  
  -- Nota: ADD_MONTHS es más exacto que 365*5 porque considera años bisiestos  
  AND cat.FECHAHABIL >= ADD_MONTHS(SYSDATE, -60)  
  AND pagos.PERIODO IS NULL  -- <--- CLAVE: Esto nos deja SOLO los que NO pagó  
  AND cat.ejercicio not in (13,22)  
ORDER BY cat.PERIODO, cat.EJERCICIO;
```