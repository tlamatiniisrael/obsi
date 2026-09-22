
## Declaración Mensual

```sql
@Table(name = "INT_DECLARACIONCA", schema = "SIR7_ING")
```


## Declaración Cero

```sql
@Table(name = "INT_DECLARACIONCA", schema = "SIR7_ING")
```

Lo mismo se graba en la misma tabla solo cambia el idtipodec


## Consulta definitiva

```sql
SELECT 
    cat.PERIODO,       -- Año
    cat.EJERCICIO,     -- Mes / Periodo
    cat.FECHAHABIL,     -- Fecha límite de pago
    P.DESCRIPCION
FROM inc_calvigencias cat
INNER JOIN CAT_PERIODO P ON ( CAT.EJERCICIO=P.IDPERIODO)
INNER JOIN RGA_OBLIGACION_CONT O ON (O.REC='18100043917' AND O.CLAVEOBLIGACION= 27 )
LEFT JOIN (
    --- SUBCONSULTA: PAGOS REALIZADOS (Optimizada) ---
    SELECT DISTINCT PERIODO, EJERCICIO 
    FROM (
        -- Parte 1: Declaraciones normales
        SELECT D.IDEJERCICIO+2000 AS PERIODO, D.IDPERIODO AS EJERCICIO
        FROM INT_DECLARACIONCA D
        INNER JOIN INC_CALVIGENCIAS A
            ON A.PERIODO = (D.IDEJERCICIO + 2000)
            AND A.EJERCICIO = D.IDPERIODO
            AND A.CLAVETIPOOPERSERV = 162
        WHERE D.REC = '18100043917'  -- <--- Movido aquí adentro para mejor rendimiento
          AND (D.IDESTADODEC IN (3, 5) OR (D.IDESTADODEC = 2 AND D.TOTALPAGAR = 0))
          AND D.IDEJERCICIO > 13
          AND D.FCHREG >= TIMESTAMP '2016-01-01 00:00:00'
          AND D.IDPERIODO NOT IN (13,22)
        -- Parte 2: Declaraciones con detalle de meses
    )
) pagos 
    ON cat.PERIODO = pagos.PERIODO 
    AND cat.EJERCICIO = pagos.EJERCICIO
WHERE cat.CLAVETIPOOPERSERV = 162
  -- Nota: ADD_MONTHS es más exacto que 365*5 porque considera años bisiestos
  AND cat.FECHAHABIL >= ADD_MONTHS(SYSDATE, -60) 
  AND pagos.PERIODO IS NULL  -- <--- CLAVE: Esto nos deja SOLO los que NO pagó
  AND cat.ejercicio not in (13,22)
ORDER BY cat.PERIODO, cat.EJERCICIO;
```

al parecer el periodo y ejercicio esta invertidos.