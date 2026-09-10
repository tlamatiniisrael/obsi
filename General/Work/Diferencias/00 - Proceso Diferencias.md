
- Esta sobre una sola estructura
- No usaron OOP esta procedimental
- Estas estan implementadas
	- [Erogaciones](Erogaciones)
	- [Movilidad](Movilidad)
	- [Servicios Ambientales](Servicios Ambientales)
	- [Isan](Isan)
	- [Casas de Empeño](Casas Empeño)
	- [Gases Contaminantes](Gases Contaminantes)
	- [Bebidas](Bebidas)
	- [Notarios](Notarios)

## Tablas Usadas

```sql
public int InsertaSoliServi(Connection conn1){  
int error=0;  
  String sql="";     
  PreparedStatement stmt =  null;  
    
  try {  
      sql= "insert into IT_Soliservi(RFC,CURP,CLAVESOLICITUD,APELLIDOP,APELLIDOM,NOMBRE,CLAVEOPERACIONSERV,CLAVEPERIODO,CP,DESCRIPCION) ";  
      sql+= " values(?,?,?,?,?,?,?,?,?,?)";  
      stmt = conn1.prepareStatement(sql);  
      stmt.setQueryTimeout(Constantes.TIMEOUT_INSERT);  
      stmt.setString(1,RFC);  
      stmt.setString(2,CURP);  
      stmt.setInt(3,ClaveSolicitud);  
      stmt.setString(4,ApellidoP);  
      stmt.setString(5,ApellidoM);  
      stmt.setString(6,Nombre);  
      stmt.setInt(7,ClaveOperacionServ);  
      stmt.setInt(8,ClavePeriodo);  
      stmt.setString(9,CP);  
      stmt.setString(10,Descripcion);  
      stmt.executeUpdate();  
  }  
  catch (SQLException E1) {  
     try {  
    conn1.rollback();  
    conn1.setAutoCommit(true);  
    error++;  
      log.error("Error IT_SoliServi.InsertaSoliServi, origen:"+origen, E1);  
    RegistraError(this.getClass().getName(), E1.getMessage(), Thread.currentThread().getStackTrace()[2].getMethodName(), "E1", sql, strUsuario, strOficina);  
     throw E1;  
  }   
  catch (SQLException E2) {  
    // TODO  
    log.error("Error IT_SoliServi.InsertaSoliServi, origen:"+origen, E2);  
    RegistraError(this.getClass().getName(), E2.getMessage(), Thread.currentThread().getStackTrace()[2].getMethodName(), "E2", "", strUsuario, strOficina);  
  }  
  }  
  finally{  
      BD_Util.bdClose(stmt);  
  }  
  return(error);  
}
```

