

### Declaraciones por rango de años
``` sql
SELECT a.rec,
       a.ejercicio             idejercicio,
       b.descripcion           ejercicio,
       a.periodo               idperiodo,
       c.descripcion           periodo,
       a.id_Estadodec          idestadodec,
       d.descripcion           estado,
       a.totalapagar,
       COALESCE (
          m.denominacion,
          f.nombre || ' ' || f.primerapellido || ' ' || f.segundoapellido)
          nombre,
       COALESCE (m.rfc, f.rfc) rfc
  FROM int_declaracion a
       INNER JOIN cat_ejercicio b ON (a.ejercicio = b.idejercicio)
       INNER JOIN cat_periodo c ON (a.periodo = c.IDPERIODO)
       INNER JOIN cat_estadodec d ON (a.id_estadodec = d.idestadodec)
       LEFT JOIN rgp_p_moral m ON (a.rec = m.rec)
       LEFT JOIN rgp_p_fisica f ON (a.rec = f.rec)
	 WHERE a.ejercicio >= 22;
```

### Declaraciones en cero
``` sql
SELECT a.rec,
       a.idejercicio             idejercicio,
       b.descripcion           ejercicio,
       a.idperiodo               idperiodo,
       c.descripcion           periodo,
       COALESCE (
          m.denominacion,
          f.nombre || ' ' || f.primerapellido || ' ' || f.segundoapellido)
          nombre,
       COALESCE (m.rfc, f.rfc) rfc
  FROM int_declaracion_cero a
       INNER JOIN cat_ejercicio b ON (a.idejercicio = b.idejercicio)
       INNER JOIN cat_periodo c ON (a.idperiodo = c.IDPERIODO)
       LEFT JOIN rgp_p_moral m ON (a.rec = m.rec)
       LEFT JOIN rgp_p_fisica f ON (a.rec = f.rec)
 WHERE a.idejercicio >= 22;
```