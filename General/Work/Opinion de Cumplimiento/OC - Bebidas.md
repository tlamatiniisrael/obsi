

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

