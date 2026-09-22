
## Declaración Mensual

```sql
private final static String SQL_INSERT  
        = "insert into INT_DECLARACIONRS ( FOLIO, FCH_REG, BASE, IMPTOCARGO, TOTALPAGAR, ACTUALIZACION, RECARGO, IMPORTECOMP, REC, IDPERIODO, IDEJERCICIO, IDESTADODEC, IMPTOANT, IDTIPODEC, DFCHANT, TOTALAFAVOR, REFERENCIA, FOLIOCOMP, SALDOFAVORCOMP, IMPORTEPAGADOCOMP, PROPIA ) " +  
        "values ( ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ? )";
```


## Declaración Cero

```sql
private final static String SQL_INSERT  
        = "insert into INT_DECLARACIONRS ( FOLIO, FCH_REG, BASE, IMPTOCARGO, TOTALPAGAR, ACTUALIZACION, RECARGO, IMPORTECOMP, REC, IDPERIODO, IDEJERCICIO, IDESTADODEC, IMPTOANT, IDTIPODEC, DFCHANT, TOTALAFAVOR, REFERENCIA, FOLIOCOMP, SALDOFAVORCOMP, IMPORTEPAGADOCOMP, PROPIA ) " +  
        "values ( ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ? )";
```


Aquí lo que cambio es que se agrego un nuevo idestadodec, para declaración en cero.

