

### Pendientes Validar

``` sql
select
                    ct.folio_consecutivo as folio, 
                    tem.desc_movto as tramite, to_char(ct.fecha_iniciotramite,'dd/mm/yyyy') as fecha, 
                    st.descripcion_estadotramite as estado, pr.correo_electronico as correo,
                    pr.id_movto as movimiento, 
                    pr.id_prerequisito as prerequisito, 
                    ct.IDESTATUSTRAMITE as estatus, ct.prioridad as prioridad, pr.CODIGO_SEGURIDAD,pr.ID_MOVTO as movto
                    ,ct.claveusuario
                    from
                    tet_control_tramite ct join tet_prerequisitos pr on(ct.id_prerequisito=pr.id_prerequisito)
                    join tec_tramiteselec tem on(pr.anio_genera=tem.anio_genera and pr.id_movto=tem.id_movto) 
                    join tec_estatustramite st on(st.idestatustramite=ct.idestatustramite)
                    join tea_perfilporusuario ppu on (ct.CLAVEUSUARIO = ppu.CLAVEUSUARIO)
                    where 
                    ct.idestatustramite in(5,11)  
                    and ppu.clavemodulo = 3
                    and ppu.idperfil in (7,10)  
                    order by ct.fecha_iniciotramite desc;
```


### Datos Usuario

``` sql
select 
                u.claveusuario,u.nombreusuario,
                u.claveoficina,o.nombreoficina,
                du.nombre||' '||du.apaterno||' '||du.amaterno as nombre,
                ppu.idperfil,pt.perfil, ppu.clavemodulo    
                from 
                sgp_usuarios u join sgp_oficina o on(u.claveoficina=o.claveoficina)
                join sgp_datosusuario du on(du.claveusuario=u.claveusuario)
                join tea_perfilporusuario ppu on(ppu.claveusuario=u.claveusuario)
                join tec_perfilestramelec pt on(pt.idperfil=ppu.idperfil)
                where 
                 u.claveusuario=805600 and ppu.activo = 1;
```

