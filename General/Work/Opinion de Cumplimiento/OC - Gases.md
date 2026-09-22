
## Declaración Mensual


```sql
sql += "insert into INT_DECGASES (";  
sql += " FOLIO, TOTALPAGAR, FECHA_REG, BASE,";  
sql += " ACTUALIZACION,RECARGO, IMPORTECOMP,";  
sql += " REC, IDPERIODO,IDEJERCICIO, IDESTADODEC,";  
sql += " IMPTOCARGO , CO2, CH4, N2O, OTROS, ";  
sql += " IMPTOANT, IDTIPODEC,DFECHAANT, TOTALAFAVOR, ";  
sql += " CREFERENCIA, IMPUESTOFAVOR, ACTUALIZACIONFAVOR,";  
sql += " RECARGOFAVOR, SALDOAFAVOR, IMPTOTPAGADO) ";  
sql += " values (?,?,SYSDATE,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,";  
if (IDTIPODEC > 1 && IMPTOANT > 0) {  
    sql += "to_date('" + DFECHAANT + "', 'dd-mm-yyyy'),?,?,?,?,?,?,?)";  
} else {  
    sql += "null,?,?,?,?,?,?,?)";  
}
```

## Declaración Cero


```sql
sql += "insert into INT_DECGASES_CERO (";  
sql += " FOLIO, REC, FECHA_REG, IDPERIODO,IDEJERCICIO,";  
sql += " ID_CAT_DEC_CERO , MOTIVO, ";  
sql += " CREFERENCIA,  IDTIPODEC) ";  
sql += " values (?,?,SYSDATE,?,?,?,?,' ',?)";
```


## Declaración Anual


```sql
sql += "insert into INT_DECGASES (";  
sql += " FOLIO, TOTALPAGAR, FECHA_REG, BASE,";  
sql += " ACTUALIZACION,RECARGO, IMPORTECOMP,";  
sql += " REC, IDPERIODO,IDEJERCICIO, IDESTADODEC,";  
sql += " IMPTOCARGO , CO2, CH4, N2O, OTROS, ";  
sql += " IMPTOANT, IDTIPODEC,DFECHAANT, TOTALAFAVOR, ";  
sql += " CREFERENCIA, IMPUESTOFAVOR, ACTUALIZACIONFAVOR,";  
sql += " RECARGOFAVOR, SALDOAFAVOR, IMPTOTPAGADO) ";  
sql += " values (?,?,SYSDATE,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,";  
if (IDTIPODEC > 1 && IMPTOANT > 0) {  
    sql += "to_date('" + DFECHAANT + "', 'dd-mm-yyyy'),?,?,?,?,?,?,?)";  
} else {  
    sql += "null,?,?,?,?,?,?,?)";  
}
```

Se graba en la tabla principal con idPeriodo=13, que es periodo anual.

