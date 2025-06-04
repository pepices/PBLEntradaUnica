# Proceso de Validación de Parámetros y Existencia en Siebel

## Descripción General

Este documento detalla el proceso de validación de parámetros y verificación de existencia en Siebel CRM para la aplicación SICCOD-CRM.

## Flujo de Validación

```mermaid
flowchart TD
    Start["Inicio de validación</br>de parámetros y existencia</br>en Siebel"]
    Start --> ParseParams["Extraer campos</br>desde la cadena CommandLine"]
    ParseParams --> CheckCount{"¿Número de</br>parámetros correcto?"}
    CheckCount -- No --> ErrorCount["Error:</br>parámetros insuficientes"]
    ErrorCount --> EndErr1["Fin con error"]

    CheckCount -- Sí --> ValidateSyntax["Validar sintaxis:</br>NIF, códigos, longitud"]
    ValidateSyntax --> CheckFields{"¿Campos obligatorios</br>están presentes?"}
    CheckFields -- No --> ErrorFields["Error:</br>campo obligatorio vacío"]
    ErrorFields --> EndErr2["Fin con error"]

    CheckFields -- Sí --> CheckSiebel["Consultar existencia</br>previa en Siebel CRM"]
    CheckSiebel --> IsAlta{"¿Operación solicitada</br>es un Alta?"}
    
    IsAlta -- Sí --> ExistsAlta{"¿Ya existe el objeto</br>en Siebel CRM?"}
    ExistsAlta -- Sí --> ErrorAlta["Error:</br>ya existe en Siebel"]
    ErrorAlta --> EndErr3["Fin con error"]
    ExistsAlta -- No --> EndOk1["Validación exitosa:</br>Alta permitida"]

    IsAlta -- No --> ExistsMod{"¿No existe el objeto</br>en Siebel CRM?"}
    ExistsMod -- Sí --> ErrorMod["Error:</br>no existe en Siebel"]
    ErrorMod --> EndErr4["Fin con error"]
    ExistsMod -- No --> EndOk2["Validación exitosa:</br>Modificación permitida"]
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