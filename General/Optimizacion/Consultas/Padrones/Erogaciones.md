

## Catalogo de Obligaciones

``` sql
select * from rgc_obligacion order by claveobligacion;
```

## Contribuyentes por año especifico

``` sql
select count(distinct rec) 
from rga_obligacion_cont 
where claveobligacion=5
and extract(year from fechainicio) <=2025
and ( fechafin is null or fechafin <= to_date('2026-01-01','yyyy-mm-dd' ) );

```



