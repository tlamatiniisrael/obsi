

## Declaración Mensual

```sql
sql += "insert into INT_DECCASASEMP (";  
sql += " FOLIO, TOTALPAGAR, FECHA_REG, BASE,";  
sql += " ACTUALIZACION,RECARGO, IMPORTECOMP,";  
sql += " REC, IDPERIODO,IDEJERCICIO, IDESTADODEC,";  
sql += " IMPTOCARGO , ";  
sql += " IMPTOANT, IDTIPODEC,DFECHAANT, TOTALAFAVOR, ";  
sql += " CREFERENCIA, IMPUESTOFAVOR, ACTUALIZACIONFAVOR,";  
sql += " RECARGOFAVOR, SALDOAFAVOR) ";  
sql += " values (?,?,SYSDATE,?,?,?,?,?,?,?,?,?,?,?,";  
if (IDTIPODEC > 1 && IMPTOANT > 0) {  
    sql += "to_date('" + DFECHAANT + "', 'dd-mm-yyyy'),?,?,?,?,?,?)";  
} else {  
    sql += "null,?,?,?,?,?,?)";  
}
```


## Declaración Cero


```sql
sql += "insert into INT_DECCASASEMP_CERO (";  
sql += " FOLIO, REC, FECHA_REG, IDPERIODO,IDEJERCICIO,";  
sql += " ID_CAT_DEC_CERO , MOTIVO, ";  
sql += " CREFERENCIA,  IDTIPODEC) ";  
sql += " values (?,?,SYSDATE,?,?,?,?,' ',?)";
```


Cabe aclarar que la declaracion en cero tambien se graba en la tabla mensual, la fk es el folio