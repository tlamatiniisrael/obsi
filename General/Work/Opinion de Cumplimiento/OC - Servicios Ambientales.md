

## Declaración Mensual

```sql
sql="insert into Int_DeclaracionServAmb (FolioServAmb, REC, ClaveServAmb, IdPeriodo, \n";  
sql+="IdEjercicio, ImporteMes1, ImporteMes2, ImporteCobrado, Actualizacion, Recargos, TotalPagar,IDTIPODEC) \n";  
sql+="values (?,?,?,?,?,?,?,?,?,?,?,?)";
```


## Declaración Cero

```sql
sql=" Insert into int_DetalleAmb_cero(";  
sql+="FOLIO,          ";  
sql+="REC,            ";  
sql+="FECHA_REG,      ";  
sql+="IDPERIODO,      ";  
sql+="IDEJERCICIO,    ";  
sql+="ID_CAT_DEC_CERO,";  
sql+="MOTIVO)";  
sql+=" values(?, ?,SYSDATE,?,?,?, ?)";
```

