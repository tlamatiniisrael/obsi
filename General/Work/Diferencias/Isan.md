
## Tablas Usadas

```sql
public boolean insertaDierenciasIsan(Connection conn) {  
    boolean Resultado = false;  
    String sql = "insert into INT_SOLIDIFISAN (CLAVESOLICITUD, IDEJERCICIO, IDPERIODO, REC) \n";  
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
