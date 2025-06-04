# Proceso de Validación de Parámetros y Existencia en Siebel

## Descripción General

Este documento detalla el proceso de validación de parámetros y verificación de existencia en Siebel CRM para la aplicación SICCOD-CRM.

## Flujo de Validación

```mermaid
flowchart TD
    Start["Inicio de Validación de parámetros y existencia en Siebel"]
    Start --> ParseParams["Extraer campos desde cadena CommandLine"]
    ParseParams --> CheckCount{"¿Número de parámetros correcto?"}
    CheckCount -- No --> ErrorCount["Mostrar error: Número de parámetros insuficiente"]
    ErrorCount --> EndErr1["Fin con error"]

    CheckCount -- Sí --> ValidateSyntax["Validar sintaxis: NIF, código, longitud, etc."]
    ValidateSyntax --> CheckFields{"¿Campos requeridos presentes?"}
    CheckFields -- No --> ErrorFields["Mostrar error: Campo obligatorio vacío"]
    ErrorFields --> EndErr2["Fin con error"]

    CheckFields -- Sí --> CheckSiebel["Consultar existencia previa en CRM Siebel"]
    CheckSiebel --> IsAlta{"¿Operación es Alta?"}
    IsAlta -- Sí --> ExistsAlta{"¿Ya existe en Siebel?"}
    ExistsAlta -- Sí --> ErrorAlta["Mostrar error: Ya existe en Siebel"]
    ErrorAlta --> EndErr3["Fin con error"]
    ExistsAlta -- No --> EndOk1["Fin: Validación exitosa (Alta permitida)"]

    IsAlta -- No --> ExistsMod{"¿No existe en Siebel?"}
    ExistsMod -- Sí --> ErrorMod["Mostrar error: No existe en Siebel para modificar"]
    ErrorMod --> EndErr4["Fin con error"]
    ExistsMod -- No --> EndOk2["Fin: Validación exitosa (Modificación permitida)"]
```

**Nota:** El flujo de validación se ejecuta secuencialmente, verificando cada aspecto antes de proceder con la operación principal. Si alguna validación falla, se muestra un mensaje de error y se interrumpe el proceso.

## Detalle de Validaciones

### 1. Validación de Formato de Parámetros
- Verifica la estructura de la cadena de entrada
- Comprueba la presencia de campos obligatorios
- Valida el formato de los datos (fechas, números, etc.)

### 2. Verificación de Existencia en Siebel
- Consulta la existencia del registro en Siebel CRM
- Verifica la consistencia de los datos
- Comprueba el estado del registro

### 3. Validación de Permisos de Usuario
- Verifica los permisos del usuario en Siebel
- Comprueba el nivel de acceso requerido
- Valida las restricciones de seguridad

### 4. Comprobación de Integridad de Datos
- Verifica la consistencia de los datos
- Comprueba las reglas de negocio
- Valida las dependencias entre campos

### 5. Confirmación de Sincronización
- Verifica el estado de la sincronización
- Comprueba la integridad de la transacción
- Confirma el registro en el histórico

## Códigos de Retorno

| Código | Descripción |
|--------|-------------|
| 0 | Validación exitosa |
| 1 | Error en formato de parámetros |
| 2 | Registro no encontrado en Siebel |
| 3 | Usuario sin permisos |
| 4 | Error de integridad de datos |
| 5 | Error en sincronización |

## Notas Técnicas

- Las validaciones se ejecutan en secuencia
- Cada paso debe completarse exitosamente para continuar
- Los errores detienen el proceso y retornan el código correspondiente
- Se registran todas las validaciones en el log del sistema

[Volver al diagrama principal](./readmeOpenAI002.md)