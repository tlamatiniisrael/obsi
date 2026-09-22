
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

