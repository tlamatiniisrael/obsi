
## Tablas Usadas

```sql
public boolean insertaDiferenciasNotarios(Connection conn) {  
    boolean Resultado = false;  
    String sql = "insert into INT_SOLIDIFNOTARIOS (CLAVESOLICITUD, IDEJERCICIO, IDPERIODO, REC, ESCRITURA ,FECHA) \n";  
    sql += "values (?, ?, ?, ?, ?,?) ";  
    try (PreparedStatement stmt = conn.prepareStatement(sql);) {  
        stmt.setQueryTimeout(Constantes.TIMEOUT_INSERT);  
        stmt.setInt(1, this.CLAVESOLICITUD);  
        stmt.setInt(2, this.IDEJERCICIO);  
        stmt.setInt(3, this.IDPERIODO);  
        stmt.setString(4, this.REC);  
        stmt.setString(5, this.ESCRITURA);  
        stmt.setDate(6, new java.sql.Date(this.FECHA.getTime()));  
        stmt.executeUpdate();  
        Resultado = true;  
    } catch (Exception E1) {  
        Error++;  
        RegistraError("DIFE", E1.getMessage(), Thread.currentThread().getStackTrace()[2].getMethodName(), "E1", sql, strUsuario, strOficina);  
    }  
    return Resultado;  
}
```

