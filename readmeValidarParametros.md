# Análisis funcional atómico: Validación de parámetros y existencia en Siebel

## Objetivo
Garantizar que la información enviada a la aplicación es suficiente, está bien formada, y corresponde con registros existentes (o ausentes, según el contexto) en el CRM Siebel.

## Pasos funcionales

### 1. Contar campos
- Se espera una cadena `CommandLine` con al menos N campos separados por `|`
- Cada tipo de operación (interlocutor, local, etc.) espera una estructura distinta

### 2. Mapeo de campos
Se mapean los campos del parámetro a variables internas:

**Para interlocutor:**
- `id_interlocutor`
- `nombre`
- `nif`

**Para local:**
- `codigo_local`
- `direccion`
- `cp`

### 3. Validaciones sintácticas
- NIF válido (formato/longitud)
- Código numérico correcto
- Campos obligatorios no vacíos

### 4. Consulta en Siebel
- Llamada a componente NVO `n_cst_so_permventana` o similar
- `EXISTS_IN_SIEBEL(id_interlocutor)` devuelve booleano o estado

### 5. Lógica de control
- Si se intenta alta y ya existe en Siebel → error
- Si se intenta modificación y no existe → error

### 6. Mensajes al usuario
- Si falla una validación, se muestra con `MessageBox("Error: campo obligatorio XYZ")`

## Resultado
- Si todo es correcto → se permite continuar
- Si hay error → se bloquea el flujo con código de retorno o salida del proceso

## Diagrama de flujo

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

[Volver al diagrama de flujo principal](./readmeOpenAI002.md)