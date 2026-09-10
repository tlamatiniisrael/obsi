
## DeclaracionImpl.java
#### obtenClaveHistorialIngreso(String, String, Connection)
```sql
SELECT D.CLAVEHISTORIALINGRESO AS CLAVEHISTORIALINGRESO
  FROM INC_HISTORIALING D
       INNER JOIN IC_SERVICIOS T ON (D.CLAVEINGRESOVIRTUAL = T.CLAVESERVICIO)
 WHERE     D.CLAVESERVICIO = ?
       AND CLAVEINGRESOVIRTUAL = ?
       AND SYSDATE BETWEEN D.FECHAINICIO AND D.FECHAFIN
```


## DeclaracionGasesAnualImpl.java
#### obtenClavesServicio(int, Connection)
```sql
SELECT D.CLAVEHISTORIALINGRESO  AS CLAVEHISTORIALINGRESO
        FROM INC_HISTORIALING D
        INNER JOIN IC_SERVICIOS T ON (D.CLAVEINGRESOVIRTUAL= T.CLAVESERVICIO)  
        WHERE D.CLAVESERVICIO = 110001 AND CLAVEINGRESOVIRTUAL = ? AND SYSDATE BETWEEN D.FECHAINICIO AND D.FECHAFIN
```


## GuardaBebidas.java

### obtenClavesServicio(int, Connection)
```sql
SELECT D.CLAVEHISTORIALINGRESO  AS CLAVEHISTORIALINGRESO
        FROM INC_HISTORIALING D
        INNER JOIN IC_SERVICIOS T ON (D.CLAVEINGRESOVIRTUAL= T.CLAVESERVICIO)  
        WHERE D.CLAVESERVICIO = 108001 AND CLAVEINGRESOVIRTUAL = ? AND SYSDATE BETWEEN D.FECHAINICIO AND D.FECHAFIN
```


## GuardaDecCasas.java

### obtenClavesServicio(int, Connection)
```sql
SELECT D.CLAVEHISTORIALINGRESO  AS CLAVEHISTORIALINGRESO
        FROM INC_HISTORIALING D
        INNER JOIN IC_SERVICIOS T ON (D.CLAVEINGRESOVIRTUAL= T.CLAVESERVICIO)
        WHERE D.CLAVESERVICIO = 111001 AND CLAVEINGRESOVIRTUAL = ? AND SYSDATE BETWEEN D.FECHAINICIO AND D.FECHAFIN
```

## INC_HistorialIng.java

### DatosHistorial(int)
``` sql
select h.Claveingresovirtual,
           h.Claveservicio,
           h.Clavehistorialingreso,
           h.Tarifa,
           h.Porcentaje,
           h.Salariosmin,
           h.Claveformacalculo,
           v.descripcion,
           h.FechaInicioAplica,
           h.fechafinaplica 
   from inc_IngresosVirtuales v 
        inner join inc_historialing h on (v.Claveingresovirtual=h.Claveingresovirtual) 
   where h.claveservicio=? 
         and trunc(sysdate) between trunc(h.fechainicio) and  trunc(h.fechafin) 
         and h.CLAVEINGRESOVIRTUAL not in(640011)
   union 
    select h.Claveingresovirtual,
           h.Claveservicio,
           h.Clavehistorialingreso,
           h.Tarifa,
           h.Porcentaje,
           h.Salariosmin,
           h.Claveformacalculo,
           v.descripcion,
           h.FechaInicioAplica,
           h.fechafinaplica 
   from inc_IngresosVirtuales v
        inner join inc_historialing h on (v.Claveingresovirtual=h.Claveingresovirtual) 
   where h.claveservicio=? 
        and h.fechafin is null 
        and h.CLAVEINGRESOVIRTUAL not in(640011)
```


### DatosHistorialSP(int)
``` sql
SELECT h.Claveingresovirtual,
       h.Claveservicio,
       h.Clavehistorialingreso,
       h.Tarifa,
       h.Porcentaje,
       h.Salariosmin,
       h.Claveformacalculo,
       v.descripcion,
       h.FechaInicioAplica,
       h.fechafinaplica
  FROM inc_IngresosVirtuales v
       INNER JOIN inc_historialing h
          ON (v.Claveingresovirtual = h.Claveingresovirtual)
 WHERE     h.claveservicio = ?
       AND v.fechafin IS NULL
       AND TRUNC (SYSDATE) BETWEEN TRUNC (h.fechainicio)
                               AND TRUNC (h.fechafin)
       AND TRUNC (SYSDATE) BETWEEN TRUNC (h.FechaInicioAplica)
                               AND TRUNC (h.fechafinaplica)
```


### DatosHistorial(int, String)
``` sql
SELECT h.Claveingresovirtual,
       h.Claveservicio,
       h.Clavehistorialingreso,
       h.Tarifa,
       h.Porcentaje,
       h.Salariosmin,
       h.Claveformacalculo,
       v.descripcion,
       h.FechaInicioAplica,
       h.fechafinaplica
  FROM inc_IngresosVirtuales v
       INNER JOIN inc_historialing h
          ON (v.Claveingresovirtual = h.Claveingresovirtual)
```


### DatosHistorialExec(int, String)
``` sql
 SELECT h.Claveingresovirtual,
       h.Claveservicio,
       h.Clavehistorialingreso,
       h.Tarifa,
       h.Porcentaje,
       h.Salariosmin,
       h.Claveformacalculo,
       v.descripcion,
       h.FechaInicioAplica,
       h.fechafinaplica
  FROM inc_IngresosVirtuales v
       INNER JOIN inc_historialing h
          ON (v.Claveingresovirtual = h.Claveingresovirtual)
```


### DatosHistorial()
``` sql
SELECT h.Claveingresovirtual,
       h.Claveservicio,
       h.Clavehistorialingreso,
       h.Tarifa,
       h.Porcentaje,
       h.Salariosmin,
       h.Claveformacalculo,
       v.descripcion,
       h.FechaInicioAplica,
       h.fechafinaplica
  FROM inc_IngresosVirtuales v
       INNER JOIN inc_historialing h
          ON (v.Claveingresovirtual = h.Claveingresovirtual)
 WHERE     v.fechafin IS NULL
       AND TRUNC (SYSDATE) BETWEEN TRUNC (h.fechainicio)
                               AND TRUNC (h.fechafin)
```


### DatosHistorialAccesorio(int)
``` sql 
SELECT h.claveingresovirtual,
       h.claveservicio,
       h.clavehistorialingreso,
       h.tarifa,
       h.porcentaje,
       h.salariosmin,
       h.claveformacalculo,
       v.descripcion,
       h.fechainicioaplica,
       h.fechafinaplica
  FROM INC_INGRESOSVIRTUALES v
       INNER JOIN INC_HISTORIALING h
          ON (v.claveingresovirtual = h.claveingresovirtual)
       INNER JOIN IC_SERVICIOS S ON (V.claveingresovirtual = S.claveservicio)
 WHERE     v.fechafin IS NULL
       AND TRUNC (SYSDATE) BETWEEN TRUNC (h.fechainicio)
                               AND TRUNC (h.fechafin)
       AND s.clavetipoacc = ?
```


## ConstruyeEstructuraXML.java

### detalleServicioDescuento(Concepto, int, String, Connection)

``` sql
select  DS.ClaveServicio as Clave, trim(CS.Descripcion) as Descripcion, CS.Articulo as Articulo, 0 as total, 0 as Importe, SUM(DS.Importe) as Descuento
from INT_DETALLEINGVIRTUAL DS INNER JOIN INC_HISTORIALING H ON (DS.ClaveOperacionServ=?  AND H.CLAVESERVICIO=? AND DS.CLAVEHISTORIALINGRESO=H.CLAVEHISTORIALINGRESO)
        INNER JOIN IC_Servicios CS on (CS.ClaveServicio=DS.ClaveServicio)
        group by DS.ClaveServicio, CS.Descripcion, CS.Articulo
```


## datosDetalleRefCFDIDao.java

### consultaDetServDescuento(int, int)

``` sql 
SELECT DS.ClaveServicio AS CLAVE,
          trim(CS.Descripcion)  AS DESCRIPCION,
          CS.Articulo           AS ARTICULO,
          0                     AS TOTAL,
          0                     AS IMPORTE,
          SUM(DS.Importe)       AS DESCUENTO
        FROM INT_DETALLEINGVIRTUAL DS
        INNER JOIN INC_HISTORIALING H
        ON (DS.ClaveOperacionServ   = ? 
        AND H.CLAVESERVICIO         = ? 
        AND DS.CLAVEHISTORIALINGRESO=H.CLAVEHISTORIALINGRESO)
        INNER JOIN IC_Servicios CS
        ON (CS.ClaveServicio=DS.ClaveServicio)
        GROUP BY DS.ClaveServicio,
          CS.Descripcion,
          CS.Articulo
```


## dao_Constancias.java

### contruye_lista_it_servicios_tenencia_sub(String, Connection)

``` sql
SELECT   CLAVE, DESCRIPCION, REFERENCIA, TO_CHAR(INGRESO, 'FM9,999,999,999,999,999') AS INGRESO,\n"  
        + " TO_CHAR(SUBSIDIO, 'FM9,999,999,999,999,999') AS SUBSIDIO, TO_CHAR(FECHAPAGO, 'DD-MM-YYYY') AS FECHAPAGO FROM( \n"  
        + " SELECT DS.CLAVESERVICIO AS CLAVE, CS.DESCRIPCION, TT.REFERENCIA,\n"  
        + " DS.TOTAL AS INGRESO, 0 AS SUBSIDIO  , GG.FECHAPAGO\n"  
        + " FROM IT_SERVICIOS TT \n"  
        + " INNER JOIN IT_CARGABANCO GG ON (TT.REFERENCIA=GG.REFERENCIA)\n"  
        + " INNER JOIN IT_DETALLESERVICIO DS ON(TT.CLAVEOPERACIONSERV = DS.CLAVEOPERACIONSERV)\n"  
        + " INNER JOIN IC_SERVICIOS CS ON (CS.CLAVESERVICIO=DS.CLAVESERVICIO)  \n"  
        + " WHERE DS.CLAVEOPERACIONSERV = (SELECT CLAVEOPERACIONSERV FROM IT_SERVICIOS WHERE REFERENCIA = ?)\n"  
        + " UNION ALL \n"  
        + " SELECT HH.CLAVEINGRESOVIRTUAL, CS.DESCRIPCION, TT.REFERENCIA,  0 AS INGRESO, DS.IMPORTE AS SUBSIDIO , GG.FECHAPAGO \n"  
        + " FROM IT_SERVICIOS TT \n"  
        + " INNER JOIN IT_CARGABANCO GG ON (TT.REFERENCIA=GG.REFERENCIA)\n"  
        + " INNER JOIN INT_DETALLEINGVIRTUAL DS  ON (TT.CLAVEOPERACIONSERV=DS.CLAVEOPERACIONSERV)\n"  
        + " INNER JOIN INC_HISTORIALING HH ON (DS.CLAVEHISTORIALINGRESO=HH.CLAVEHISTORIALINGRESO)\n"  
        + " INNER JOIN IC_SERVICIOS CS ON (CS.CLAVESERVICIO=HH.CLAVEINGRESOVIRTUAL) \n"  
        + " WHERE DS.CLAVEOPERACIONSERV = (SELECT CLAVEOPERACIONSERV FROM IT_SERVICIOS WHERE REFERENCIA = ?) )ORDER BY CLAVE";
```


## DaoDerechosCV.java
### OptenServicios(Connection)


``` sql
Select s.CLAVESERVICIO,s.DESCRIPCION\n"  
        + " from ic_servicios s  \n"  
        + " inner join ic_historialServ h on (s.CLAVESERVICIO=h.CLAVESERVICIO) \n"  
        + " left outer join inc_historialing hi on (s.claveservicio=hi.claveservicio and trunc(sysdate) between trunc(hi.fechainicio) and  trunc(hi.fechafin) ) \n"  
        + " where  s.ClaveServicio in (212116,212119,212172,212120,212118,212014,212016,212017,212020)  \n"  
        + " and s.fechafin is null and trunc(sysdate) between trunc(h.fechainicio) and  trunc(h.fechafin)  \n"  
        + " order by descripcion
```

## DaoDiferencias.java
### ObtenServicios(Connection, int)
``` sql
SELECT DISTINCT S.CLAVESERVICIO,S.DESCRIPCION\n"  
        + " FROM IC_SERVICIOS S  \n"  
        + " INNER JOIN IC_HISTORIALSERV H ON (S.CLAVESERVICIO=H.CLAVESERVICIO) \n"  
        + " LEFT OUTER JOIN INC_HISTORIALING HI ON (S.CLAVESERVICIO=HI.CLAVESERVICIO AND TRUNC(SYSDATE) BETWEEN TRUNC(HI.FECHAINICIO) AND  TRUNC(HI.FECHAFIN) ) \n"  
        + " WHERE  S.CLAVESERVICIO IN (108010, 108001, 506092, 505045)  \n"  
        + " AND S.FECHAFIN IS NULL AND TRUNC(SYSDATE) BETWEEN TRUNC(H.FECHAINICIO) AND  TRUNC(H.FECHAFIN)  \n"  
        + " ORDER BY DESCRIPCION
```

``` sql 
SELECT S.CLAVESERVICIO,S.DESCRIPCION \n"  
        + " FROM IC_SERVICIOS S INNER JOIN IC_HISTORIALSERV H ON (S.CLAVESERVICIO=H.CLAVESERVICIO) \n"  
        + " WHERE S.CLAVEDEPENDENCIA=0 AND S.CLAVEGPODEPENCIA=0 AND S.CLAVEFORMULARIO=14 AND S.FECHAFIN IS NULL AND TRUNC(SYSDATE)\n"  
        + " BETWEEN TRUNC(H.FECHAINICIO) AND  TRUNC(H.FECHAFIN) ORDER BY DESCRIPCION";
```

``` sql
SELECT DISTINCT S.CLAVESERVICIO,S.DESCRIPCION\n"  
        + " FROM IC_SERVICIOS S  \n"  
        + " INNER JOIN IC_HISTORIALSERV H ON (S.CLAVESERVICIO=H.CLAVESERVICIO) \n"  
        + " LEFT OUTER JOIN INC_HISTORIALING HI ON (S.CLAVESERVICIO=HI.CLAVESERVICIO AND TRUNC(SYSDATE) BETWEEN TRUNC(HI.FECHAINICIO) AND  TRUNC(HI.FECHAFIN) ) \n"  
        + " WHERE  S.CLAVESERVICIO IN (705001, 705002, 705003, 705004)  \n"  
        + " AND S.FECHAFIN IS NULL AND TRUNC(SYSDATE) BETWEEN TRUNC(H.FECHAINICIO) AND  TRUNC(H.FECHAFIN)  \n"  
        + " ORDER BY DESCRIPCION
```

``` sql
SELECT DISTINCT S.CLAVESERVICIO,S.DESCRIPCION\n"  
        + " FROM IC_SERVICIOS S  \n"  
        + " INNER JOIN IC_HISTORIALSERV H ON (S.CLAVESERVICIO=H.CLAVESERVICIO) \n"  
        + " LEFT OUTER JOIN INC_HISTORIALING HI ON (S.CLAVESERVICIO=HI.CLAVESERVICIO AND TRUNC(SYSDATE) BETWEEN TRUNC(HI.FECHAINICIO) AND  TRUNC(HI.FECHAFIN) ) \n"  
        + " WHERE  S.CLAVESERVICIO IN (308001, 308002, 308003, 308004)  \n"  
        + " AND S.FECHAFIN IS NULL AND TRUNC(SYSDATE) BETWEEN TRUNC(H.FECHAINICIO) AND  TRUNC(H.FECHAFIN)  \n"  
        + " ORDER BY DESCRIPCION
```

``` sql
SELECT DISTINCT S.CLAVESERVICIO,S.DESCRIPCION\n"  
        + " FROM IC_SERVICIOS S  \n"  
        + " INNER JOIN IC_HISTORIALSERV H ON (S.CLAVESERVICIO=H.CLAVESERVICIO) \n"  
        + " LEFT OUTER JOIN INC_HISTORIALING HI ON (S.CLAVESERVICIO=HI.CLAVESERVICIO AND TRUNC(SYSDATE) BETWEEN TRUNC(HI.FECHAINICIO) AND  TRUNC(HI.FECHAFIN) ) \n"  
        + " WHERE  S.CLAVESERVICIO IN (307001, 307002, 505014)  \n"  
        + " AND S.FECHAFIN IS NULL AND TRUNC(SYSDATE) BETWEEN TRUNC(H.FECHAINICIO) AND  TRUNC(H.FECHAFIN)  \n"  
        + " ORDER BY DESCRIPCION
```

``` sql
SELECT DISTINCT S.CLAVESERVICIO,S.DESCRIPCION\n"  
        + " FROM IC_SERVICIOS S  \n"  
        + " INNER JOIN IC_HISTORIALSERV H ON (S.CLAVESERVICIO=H.CLAVESERVICIO) \n"  
        + " LEFT OUTER JOIN INC_HISTORIALING HI ON (S.CLAVESERVICIO=HI.CLAVESERVICIO AND TRUNC(SYSDATE) BETWEEN TRUNC(HI.FECHAINICIO) AND  TRUNC(HI.FECHAFIN) ) \n"  
        + " WHERE  S.CLAVESERVICIO IN (702001, 702002, 702003, 702004) \n"  
        + " AND S.FECHAFIN IS NULL AND TRUNC(SYSDATE) BETWEEN TRUNC(H.FECHAINICIO) AND  TRUNC(H.FECHAFIN)  \n"  
        + " ORDER BY DESCRIPCION
```

``` sql 
SELECT DISTINCT S.CLAVESERVICIO,S.DESCRIPCION\n"  
        + " FROM IC_SERVICIOS S  \n"  
        + " INNER JOIN IC_HISTORIALSERV H ON (S.CLAVESERVICIO=H.CLAVESERVICIO) \n"  
        + " LEFT OUTER JOIN INC_HISTORIALING HI ON (S.CLAVESERVICIO=HI.CLAVESERVICIO AND TRUNC(SYSDATE) BETWEEN TRUNC(HI.FECHAINICIO) AND  TRUNC(HI.FECHAFIN) ) \n"  
        + " WHERE  S.CLAVESERVICIO IN (111001, 111002, 505053, 506096) \n"  
        + " AND S.FECHAFIN IS NULL AND TRUNC(SYSDATE) BETWEEN TRUNC(H.FECHAINICIO) AND  TRUNC(H.FECHAFIN)  \n"  
        + " ORDER BY DESCRIPCION
```

``` sql
SELECT DISTINCT S.CLAVESERVICIO,S.DESCRIPCION\n"  
        + " FROM IC_SERVICIOS S  \n"  
        + " INNER JOIN IC_HISTORIALSERV H ON (S.CLAVESERVICIO=H.CLAVESERVICIO) \n"  
        + " LEFT OUTER JOIN INC_HISTORIALING HI ON (S.CLAVESERVICIO=HI.CLAVESERVICIO AND TRUNC(SYSDATE) BETWEEN TRUNC(HI.FECHAINICIO) AND  TRUNC(HI.FECHAFIN) ) \n"  
        + " WHERE  S.CLAVESERVICIO IN (110001, 110002, 505052, 506095) \n"  
        + " AND S.FECHAFIN IS NULL AND TRUNC(SYSDATE) BETWEEN TRUNC(H.FECHAINICIO) AND  TRUNC(H.FECHAFIN)  \n"  
        + " ORDER BY DESCRIPCION
```

## DaoDiferenciasHosp.java
### OptenServicios(Connection)

````sql
SELECT DISTINCT s.CLAVESERVICIO, s.DESCRIPCION
    FROM ic_servicios s
         INNER JOIN ic_historialServ h ON (s.CLAVESERVICIO = h.CLAVESERVICIO)
         LEFT OUTER JOIN inc_historialing hi
            ON (    s.claveservicio = hi.claveservicio
                AND TRUNC (SYSDATE) BETWEEN TRUNC (hi.fechainicio)
                                        AND TRUNC (hi.fechafin))
   WHERE     s.ClaveServicio IN (106001,
                                 106010,
                                 106002,
                                 106020,
                                 505035,
                                 505044,
                                 506072,
                                 517029)
         AND s.fechafin IS NULL
         AND TRUNC (SYSDATE) BETWEEN TRUNC (h.fechainicio)
                                 AND TRUNC (h.fechafin)
ORDER BY descripcionn
```