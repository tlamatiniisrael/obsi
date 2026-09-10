

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



### Cero

```sql
String sql = " INSERT INTO DECLARACION(";  
sql += "FOLIO, TOTALPAGAR, FECHA_REG, BASE, ACTUALIZACION, RECARGO, BONIFICACION, IMPORTECOMP, REC, IDPERIODO, IDEJERCICIO, IDBASECALC, NUMEMP, IDESTADODEC, M2, IMPTOCARGO, \n";  
sql += " IMPTOANT, IDTIPODEC, LICENCIA,OTROSESTIMULOS, DFECHAANT,TOTALAFAVOR, CREFERENCIA, CLAVE_TIPOSUBSIDIO_ERO, NUMEMPCONSUB, NUMEMPSINSUB, BASEGRAVPROPIA, BASEGRAVRETENIDA,\n";  
sql += " IMPUESTOFAVOR, ACTUALIZACIONFAVOR, RECARGOFAVOR, IDTIPOENVIO, FOLIO_PRE, SALDOAFAVOR)\n";  
sql += " values (?, 0,SYSDATE, 0, 0, 0, 0, 0, ?, ?, ?, 1, 0, 2, null, 0, 0, ?, null, null, null, 0, ' ', null, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0)";
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

