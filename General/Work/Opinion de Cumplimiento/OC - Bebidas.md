
Esta declaración tiene mensual y Cero.

## Declaración Mensual

```sql
sql = " Insert into INT_DECBEBIDAS(";  
sql += "FOLIO,";  
sql += "TOTALPAGAR,";  
sql += "FECHA_REG,";  
sql += "BASE,";  
sql += "ACTUALIZACION,";  
sql += "RECARGO,";  
sql += "BONIFICACION,";  
sql += "IMPORTECOMP,";  
sql += "REC,";  
sql += "IDPERIODO,";  
sql += "IDEJERCICIO,";  
sql += "IDBASECALC,";  
sql += "IDESTADODEC,";  
sql += "IMPTOCARGO,";  
sql += "IMPTOANT,";  
sql += "IDTIPODEC,";  
sql += "DFECHAANT,";  
sql += "TOTALAFAVOR,";  
sql += "CREFERENCIA, ";  
sql += "IMPUESTOFAVOR,";  
sql += "ACTUALIZACIONFAVOR,";  
sql += " RECARGOFAVOR)";  
sql += " values(?,?,SYSDATE,?,?,?,null,?,?,?,?,1,?,?,?,?,";  
if ((beb.decimpComple > 0) && !((beb.decfechaComple.equals("--/--/----")))) {  
    sql += "to_date('" + beb.decfechaComple + "', 'dd-mm-yyyy'), ?, ?, ?, ?, ?)";  
} else {  
    sql += "null, ?, ?, ?, ?, ?)";  
}
```


## Declaración Cero


```sql
sql = "Insert into INT_DECBEBIDAS_CERO (FOLIO,REC,FECHA_REG,IDPERIODO,IDEJERCICIO,ID_CAT_DEC_CERO,MOTIVO,CREFERENCIA,IDTIPODEC,IDBASECALC) values(?,?,SYSDATE,?,?,?,?,?,?,1)";
```


Probando la CONSULTA final

```sql
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
             SELECT distinct D.IDEJERCICIO+2000 AS PERIODO, D.IDPERIODO AS EJERCICIO  
             FROM INT_DECBEBIDAS D  
                      INNER JOIN IT_SERVICIOS S ON (D.CREFERENCIA = S.REFERENCIA)  
                      INNER JOIN IT_CARGABANCO B ON (S.REFERENCIA = B.REFERENCIA and B.CLAVEESTADOCARGA = 2 OR B.CLAVEESTADOCARGA = 8)  
                      INNER JOIN INC_CALVIGENCIAS A  
                                 ON A.PERIODO = (D.IDEJERCICIO + 2000)  
                                     AND A.EJERCICIO = D.IDPERIODO  
                                     AND A.CLAVETIPOOPERSERV = 127  
             WHERE D.REC = '00000522586'  -- <--- Movido aquí adentro para mejor rendimiento  
               AND D.IDESTADODEC =3  
               AND D.IDEJERCICIO > 13  
               AND D.FECHA_REG >= TIMESTAMP '2016-01-01 00:00:00'  
               AND D.IDPERIODO NOT IN (13,22)  
             UNION ALL  
             -- Parte 2: Declaraciones en cero  
             SELECT D.IDEJERCICIO+2000 AS PERIODO, D.IDPERIODO AS EJERCICIO  
             FROM int_decbebidas_cero D  
                      INNER JOIN INC_CALVIGENCIAS A  
                                 ON A.PERIODO = (D.IDEJERCICIO + 2000)  
                                     AND A.EJERCICIO = D.IDPERIODO  
                                     AND A.CLAVETIPOOPERSERV = 127  
             WHERE D.REC = '00000522586'  -- <--- Movido aquí adentro para mejor rendimiento  
               AND D.IDEJERCICIO > 13  
               AND D.FECHA_REG >= TIMESTAMP '2016-01-01 00:00:00'  
               AND D.IDPERIODO NOT IN (13,22)  
         )  
) pagos  
                   ON cat.PERIODO = pagos.PERIODO  
                       AND cat.EJERCICIO = pagos.EJERCICIO  
WHERE cat.CLAVETIPOOPERSERV = 127  
  -- Nota: ADD_MONTHS es más exacto que 365*5 porque considera años bisiestos  
  AND cat.FECHAHABIL >= ADD_MONTHS(SYSDATE, -60)  
  AND pagos.PERIODO IS NULL  -- <--- CLAVE: Esto nos deja SOLO los que NO pagó  
  AND cat.ejercicio not in (13,22)  
ORDER BY cat.PERIODO, cat.EJERCICIO;
```



