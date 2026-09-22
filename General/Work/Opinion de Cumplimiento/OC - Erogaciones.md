

### Mensual

```sql
sql = " Insert into declaracion(";  
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
sql += "NUMEMP,";  
sql += "IDESTADODEC,";  
sql += "M2,";  
sql += "IMPTOCARGO,";  
sql += "IMPTOANT,";  
sql += "IDTIPODEC,";  
sql += "LICENCIA,";  
sql += "OTROSESTIMULOS,";  
sql += "DFECHAANT, TOTALAFAVOR, ";  
sql += "NUMEMPSINSUB, NUMEMPCONSUB, ";  
sql += "BASEGRAVPROPIA, BASEGRAVRETENIDA, ";  
sql += "IMPUESTOFAVOR, ACTUALIZACIONFAVOR, RECARGOFAVOR, CLAVE_TIPOSUBSIDIO_ERO, IDTIPOENVIO,SALDOAFAVOR )";  
sql += " values(?, ?,SYSDATE,?,?,?, ?,?,?,?,?, ?,?,?,?,?, ?,?,?,?,";  
if ((objsession.decimpComple > 0) && !((objsession.decfechaComple.equals("--/--/----")))) {  
    sql += "to_date('" + objsession.decfechaComple + "', 'dd-mm-yyyy'), ?, ?, ?, ?, ?, ?, ?, ?, ?, 1, ?)";  
} else {  
    sql += "null, ?, ?, ?, ?, ?, ?, ?, ?, ? ,1,?)";  
}
```

**Se guardan con totalpagar > 0 y periodo entre 1 y 12**

### Cero

```sql
String sql = " INSERT INTO DECLARACION(";  
sql += "FOLIO, TOTALPAGAR, FECHA_REG, BASE, ACTUALIZACION, RECARGO, BONIFICACION, IMPORTECOMP, REC, IDPERIODO, IDEJERCICIO, IDBASECALC, NUMEMP, IDESTADODEC, M2, IMPTOCARGO, \n";  
sql += " IMPTOANT, IDTIPODEC, LICENCIA,OTROSESTIMULOS, DFECHAANT,TOTALAFAVOR, CREFERENCIA, CLAVE_TIPOSUBSIDIO_ERO, NUMEMPCONSUB, NUMEMPSINSUB, BASEGRAVPROPIA, BASEGRAVRETENIDA,\n";  
sql += " IMPUESTOFAVOR, ACTUALIZACIONFAVOR, RECARGOFAVOR, IDTIPOENVIO, FOLIO_PRE, SALDOAFAVOR)\n";  
sql += " values (?, 0,SYSDATE, 0, 0, 0, 0, 0, ?, ?, ?, 1, 0, 2, null, 0, 0, ?, null, null, null, 0, ' ', null, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0)";
```

```sql
sql = "INSERT INTO DECLARACION_CERO(FOLIO, REC, FECHA_REG, IDPERIODO, IDEJERCICIO, ID_CAT_DEC_CERO, MOTIVO, CREFERENCIA, IDTIPODEC, IDBASECALC) "  
        + " VALUES(?,?,SYSDATE, ?, ?, ?, ?, ' ', ?, 1)";
```

**La diferencia es que se guarda con totalapagar = 0 y idestadodec=2**



### Anual

```sql
sql=" Insert into declaracion(";  
    sql+="FOLIO,";  
    sql+="TOTALPAGAR,";  
    sql+="FECHA_REG,";  
    sql+="BASE,";  
    sql+="ACTUALIZACION,";  
    sql+="RECARGO,";  
    sql+="BONIFICACION,";  
    sql+="IMPORTECOMP,";  
    sql+="REC,";  
    sql+="IDPERIODO,";  
    sql+="IDEJERCICIO,";  
    sql+="IDBASECALC,";  
    sql+="NUMEMP,";  
    sql+="IDESTADODEC,";  
    sql+="M2,";  
    sql+="IMPTOCARGO,";  
    sql+="IMPTOANT,";  
    sql+="IDTIPODEC,";  
    sql+="LICENCIA,";  
    sql+="OTROSESTIMULOS,";  
    sql+="DFECHAANT)";  
    sql+=" values(?, ?,SYSDATE,?,?,?, ?,?,?,?,?, ?,?,?,?,?, ?,?,?,?,?) ";    
      
//guardar los datos de la declaracion  
    pstmt = conn.prepareStatement(sql);  
    pstmt.setQueryTimeout(Constantes.TIMEOUT_INSERT);  
    pstmt.setInt(1,folio);  
    pstmt.setDouble(2,txttotal);  
  //04/10/2006  
  //CHANGE-REQUEST: SE CAMBIA POR EL SYSDATE DE LA BASE DE DATOS  //pstmt.setTimestamp(3, new java.sql.Timestamp(System.currentTimeMillis()));  //  
  pstmt.setLong(3,txtbasegrav);  
  pstmt.setDouble(4,txtactualizacion);  
  pstmt.setDouble(5,txtrecargo);  
  pstmt.setDouble(6,txtbonificacion);  
  pstmt.setDouble(7,txtimportecomp);  
  pstmt.setString(8, REC);  
  pstmt.setInt(9,cmbperiodo);//System.out.println("cmbperiodo:"+cmbperiodo);  
  pstmt.setInt(10,cmbejercicio);//System.out.println("cmbejercicio:"+cmbejercicio);  
  pstmt.setInt(11,cmbbasecalc);  
  pstmt.setInt(12,txtempleados);  
  pstmt.setInt(13,1);  
  pstmt.setInt(14,txtm2);  
  pstmt.setDouble(15,txtimpcargo);  
  pstmt.setDouble(16,txtimpganado);  
  pstmt.setInt(17,cmbtipodec);  
  pstmt.setString(18, txtliciencia);  
  pstmt.setDouble(19,txtestimulos);  
  if ((txtimpganado>0)&&!((txtfechaipc.equals("----/--/--")))){  
//if (txtimpganado>0){  
     java.sql.Date date = java.sql.Date.valueOf(txtfechaipc);  
     pstmt.setDate(20,date);  
  }  else  
  {  
   //////////////////////////////////////////////////////  
   //pstmt.setTimestamp(21, new java.sql.Timestamp(System.currentTimeMillis()));   //////////////////////////////////////////////////////   pstmt.setDate(20,null);  
  }    pstmt.executeUpdate();
```


**Aquí la diferencia es el periodo de acuerdo al catalogo las anuales sin periodo 13.**

y el detalle se encuentra en :

```sql
select * from det_impdec_mes
where folio=2734716;

select *
from declaracion d
inner join det_impdec_mes a on ( d.folio=a.folio )
where
1=1
and d.rec='18100043917'
and d.idejercicio=26 
and d.idperiodo=13;
```


### Ahora la consulta original

```sql
/* Formatted on 14/09/2026 03:22:05 p. m. (QP5 v5.294) */
  SELECT X.REC1,
         X.PERIODO   AS EJERCICIO,
         X.EJERCICIO AS PERIODO,
         P.DESCRIPCION AS DESCPERIODO,
         RFC_FISICO,
         NOMBRE,
         PRIMERAPELLIDO,
         SEGUNDOAPELLIDO,
         RFC_MORAL,
         DENOMINACION,
         FECHAINIACTEST
    FROM (SELECT REC AS REC1,
                 PERIODO,
                 EJERCICIO,
                 SYSDATE,
                 FECHAHABIL,
                 FECHAINIACTEST,
                 FECHAINICIOOBLIGACION,
                 RFC_FISICO,
                 NOMBRE,
                 PRIMERAPELLIDO,
                 SEGUNDOAPELLIDO,
                 RFC_MORAL,
                 DENOMINACION
            FROM (SELECT U.REC,
                         U.FECHAINIACTEST,
                         O.FECHAINICIO AS FECHAINICIOOBLIGACION,
                         F.RFC       AS RFC_FISICO,
                         F.NOMBRE,
                         F.PRIMERAPELLIDO,
                         F.SEGUNDOAPELLIDO,
                         M.RFC       AS RFC_MORAL,
                         M.DENOMINACION
                    FROM RGP_CONTRIBUYENTE U
                         LEFT OUTER JOIN RGP_P_MORAL M ON (U.REC = M.REC)
                         LEFT OUTER JOIN RGP_P_FISICA F ON (U.REC = F.REC)
                         INNER JOIN SGP_USUARIOS K ON (U.REC = K.NOMBREUSUARIO)
                         INNER JOIN RGA_OBLIGACION_CONT O
                            ON (    U.REC = O.REC
                                AND O.CLAVEOBLIGACION = 5
                                AND O.FECHAFIN IS NULL
                                AND U.CLAVESITCONT IN (1, 3))
                   WHERE U.REC = ?)
                 LEFT OUTER JOIN INC_CALVIGENCIAS J
                    ON (    J.CLAVETIPOOPERSERV = 2
                        AND EJERCICIO NOT IN (13, 22)
                        AND J.FECHAHABIL <= TRUNC (SYSDATE)
                        AND J.PERIODO > 2020)) X
         INNER JOIN CAT_PERIODO P ON (X.EJERCICIO = P.IDPERIODO)
         LEFT OUTER JOIN
         (SELECT D.REC AS REC2, D.IDPERIODO, D.IDEJERCICIO
            FROM DECLARACION D
                 INNER JOIN INC_CALVIGENCIAS A
                    ON (    A.PERIODO = (D.IDEJERCICIO + 2000)
                        AND A.EJERCICIO = D.IDPERIODO
                        AND A.CLAVETIPOOPERSERV = 2
                        AND (   IDESTADODEC IN (3, 5)
                             OR (IDESTADODEC = 2 AND TOTALPAGAR = 0))
                        AND D.IDEJERCICIO > 13
                        AND D.FECHA_REG BETWEEN TIMESTAMP '2016-01-01 00:00:00'
                                            AND SYSDATE)) Z
            ON (    REC1 = REC2
                AND X.PERIODO = (Z.IDEJERCICIO + 2000)
                AND X.EJERCICIO = Z.IDPERIODO)
   WHERE     Z.REC2 IS NULL
         AND TO_NUMBER (X.PERIODO || LPAD (X.EJERCICIO, 2, '0')) >=
                TO_NUMBER (TO_CHAR (X.FECHAINICIOOBLIGACION, 'YYYYMM'))
ORDER BY X.REC1, X.PERIODO, X.EJERCICIO;
```


Y POR FIN LA NUEVA CONSULTA CON MENSUAL, CERO Y ANUAL.

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
        SELECT D.IDEJERCICIO+2000 AS PERIODO, D.IDPERIODO AS EJERCICIO
        FROM DECLARACION D
        INNER JOIN INC_CALVIGENCIAS A
            ON A.PERIODO = (D.IDEJERCICIO + 2000)
            AND A.EJERCICIO = D.IDPERIODO
            AND A.CLAVETIPOOPERSERV = 2
        WHERE D.REC = '18100043917'  -- <--- Movido aquí adentro para mejor rendimiento
          AND (D.IDESTADODEC IN (3, 5) OR (D.IDESTADODEC = 2 AND D.TOTALPAGAR = 0))
          AND D.IDEJERCICIO > 13
          AND D.FECHA_REG >= TIMESTAMP '2016-01-01 00:00:00'
          AND D.IDPERIODO NOT IN (13,22)
        UNION ALL
        -- Parte 2: Declaraciones con detalle de meses
        SELECT D.IDEJERCICIO+2000 AS PERIODO, M.MES AS EJERCICIO
        FROM DECLARACION D
        INNER JOIN det_impdec_mes M ON D.folio = M.folio
        INNER JOIN INC_CALVIGENCIAS A
            ON A.PERIODO = (D.IDEJERCICIO + 2000)
            AND A.EJERCICIO = D.IDPERIODO
            AND A.CLAVETIPOOPERSERV = 2
        WHERE D.REC = '18100043917'  -- <--- Movido aquí adentro para mejor rendimiento
          AND (D.IDESTADODEC IN (3, 5) OR (D.IDESTADODEC = 2 AND D.TOTALPAGAR = 0))
          AND D.IDEJERCICIO > 13
          AND D.FECHA_REG >= TIMESTAMP '2016-01-01 00:00:00'
    )
) pagos 
    ON cat.PERIODO = pagos.PERIODO 
    AND cat.EJERCICIO = pagos.EJERCICIO
WHERE cat.CLAVETIPOOPERSERV = 2
  -- Nota: ADD_MONTHS es más exacto que 365*5 porque considera años bisiestos
  AND cat.FECHAHABIL >= ADD_MONTHS(SYSDATE, -60) 
  AND pagos.PERIODO IS NULL  -- <--- CLAVE: Esto nos deja SOLO los que NO pagó
  AND cat.ejercicio not in (13,22)
ORDER BY cat.PERIODO, cat.EJERCICIO;
```


Con antelcion se debera sacar los datos del rec:

```sql
select * from rgp_contribuyente where rec='18100043917';
```

Para pasar la fecha de inicio de obligación, ya que no podemos cobrarle antes de iniciar las actividades.


