
## Tablas Usadas

```sql
public boolean insertaDierenciasErogaciones(Connection conn) {  
    boolean Resultado = false;  
    String sql = "insert into INT_SOLIDIFEROGA (CLAVESOLICITUD, IDEJERCICIO, IDPERIODO, REC) \n";  
    sql += "values (?, ?, ?, ?) ";  
    try (PreparedStatement stmt = conn.prepareStatement(sql);) {  
        stmt.setQueryTimeout(Constantes.TIMEOUT_INSERT);  
        stmt.setInt(1, this.CLAVESOLICITUD);  
        stmt.setInt(2, this.IDEJERCICIO);  
        stmt.setInt(3, this.IDPERIODO);  
        stmt.setString(4, this.REC);  
        stmt.executeUpdate();  
        Resultado = true;  
    } catch (Exception E1) {  
        Error++;  
        RegistraError("DIFE", E1.getMessage(), Thread.currentThread().getStackTrace()[2].getMethodName(), "E1", sql, strUsuario, strOficina);  
    }  
    return Resultado;  
}
```

En este caso se genera una declaración por la diferencia registrada.

```sql
public void inserta_int_declaracion_diferencias(Connection conn) throws Exception {  
    String sql = " INSERT INTO DECLARACION(";  
    sql += "FOLIO, TOTALPAGAR, FECHA_REG, BASE, ACTUALIZACION, RECARGO, BONIFICACION, IMPORTECOMP, REC, IDPERIODO, IDEJERCICIO, IDBASECALC, NUMEMP, IDESTADODEC, M2, IMPTOCARGO, \n";  
    sql += " IMPTOANT, IDTIPODEC, OTROSESTIMULOS, DFECHAANT,TOTALAFAVOR, CREFERENCIA, CLAVE_TIPOSUBSIDIO_ERO, NUMEMPCONSUB, NUMEMPSINSUB, BASEGRAVPROPIA, BASEGRAVRETENIDA,\n";  
    sql += " IMPUESTOFAVOR, ACTUALIZACIONFAVOR, RECARGOFAVOR, IDTIPOENVIO)\n";  
    sql += " VALUES(? ,? , SYSDATE, ?, ?,?, 0, 0, ?, ?, ?, 1, ?, 2, 0, ?, 0, 1, 0, NULL, 0, ?, NULL, 0, ?, ?, 0, 0, 0, 0, 3)";  
    try (PreparedStatement stmt = conn.prepareStatement(sql);) {  
        stmt.setInt(1, FOLIO);  
        stmt.setDouble(2, TOTALPAGAR);  
        stmt.setDouble(3, BASE);  
        stmt.setDouble(4, ACTUALIZACION);  
        stmt.setDouble(5, RECARGO );  
        stmt.setString(6, REC);  
        stmt.setInt(7, IDPERIODO);  
        stmt.setInt(8, IDEJERCICIO);  
        stmt.setInt(9, NUMEMP);  
        stmt.setDouble(10, IMPTOCARGO);  
        stmt.setString(11, REFERENCIA);  
        stmt.setInt(12, NUMEMP);  
        stmt.setDouble(13, BASE);  
        stmt.setQueryTimeout(Constantes.TIMEOUT_INSERT);  
        stmt.executeUpdate();  
    } catch (Exception e) {  
        RegistraError("ISERTP", e.getMessage(), e.getMessage() + "-" + e.getCause(), Thread.currentThread().getStackTrace()[2].getMethodName() + " Error al  inserta_int_declaracion_diferencias ", this.getClass().getName(), " " + sql, strUsuario, strOficina);  
        throw new Exception("Error al inserta_int_declaracion_diferencias  " + e.getMessage());  
    }  
}
```

