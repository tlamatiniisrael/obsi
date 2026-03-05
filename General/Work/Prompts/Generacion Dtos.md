
```
Genera un DTO Java optimizado para JDK 11 basado en el siguiente DDL.

Reglas obligatorias:

- Usar java.time (LocalDateTime o LocalDate según corresponda).
- Usar tipos primitivos cuando la columna sea NOT NULL y lo permita.
- Si la columna es nullable, usar wrapper u objeto.
- NUMBER sin decimales → long o int según tamaño lógico.
- NUMBER con decimales → BigDecimal.
- VARCHAR2 → String.
- DATE de Oracle → LocalDateTime.
- No usar java.util.Date ni Calendar.
- Implementar Serializable.
- Incluir serialVersionUID.
- Generar getters y setters.
- No agregar explicaciones.
- No generar la entidad JPA.
- Solo generar el DTO final limpio.

DDL:
<Pega aquí el DDL>
```

Prompt Mejorado

```
Genera un DTO Java optimizado para JDK 11 basado en el siguiente DDL.

Reglas obligatorias:

TIPOS:
- Usar java.time (LocalDateTime o LocalDate según corresponda).
- Usar tipos primitivos cuando la columna sea NOT NULL y lo permita.
- Si la columna es nullable, usar wrapper u objeto.
- NUMBER sin decimales → long o int según tamaño lógico.
- NUMBER con decimales → BigDecimal.
- VARCHAR2 → String.
- DATE de Oracle → LocalDateTime.
- No usar java.util.Date ni Calendar.

VALIDACIONES (Bean Validation):
- Si la columna es NOT NULL → agregar @NotNull (si es objeto).
- Si es primitivo y NOT NULL → no agregar @NotNull.
- VARCHAR2 con tamaño definido → agregar @Size(max = N).
- VARCHAR2 NOT NULL → agregar @NotBlank.
- NUMBER NOT NULL → agregar @NotNull si es wrapper.
- Respetar exactamente las restricciones del DDL.
- No inventar validaciones que no existan en el DDL.
- Usar javax.validation.constraints.* (compatible con JDK 11 y Java EE).

ESTRUCTURA:
- Implementar Serializable.
- Incluir serialVersionUID.
- Convertir nombres de columnas a camelCase.
- Generar getters y setters.
- No agregar explicaciones.
- No generar la entidad JPA.
- Solo generar el DTO final limpio.

DDL:
<Pega aquí el DDL>
```