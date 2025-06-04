# F22: Registrar en histórico

## Objetivo
Registrar en una tabla o log los eventos realizados (alta/modificación) en el contexto SICCOD-CRM para auditoría, seguimiento y trazabilidad.

## Pasos funcionales

### Recopilación de datos
Se recoge un conjunto de datos comunes para todas las operaciones:

- ID del usuario que ejecutó
- Timestamp del evento
- Tipo de operación (alta, modificación, sincronización)
- ID del objeto (interlocutor/local/etc.)
- Resultado (ok, error, código CRM, etc.)

### Formato estructurado
Se construye una estructura tipo registro/log:

```json
{
  "usuario": "jsanchepollackz",
  "fecha": "2025-06-04 10:21",
  "tipo_operacion": "alta_interlocutor",
  "id_objeto": "1-7RAAH",
  "resultado": "ok"
}
```

### Inserción en histórico

- Si es un DataWindow → se usa `dw_historial.InsertRow() + SetItem()`
- Si es directo a BD → `INSERT INTO t_historial_operaciones (...) VALUES (...)`
- Si es remoto → se llama a un componente Jaguar: `n_cst_do_log_operacion.InsertLog(...)`

### Control de errores

- Si la inserción falla, no se bloquea el proceso principal pero se muestra Warning: no se pudo registrar en histórico.
- Se pueden almacenar en local para sincronizar luego.

### Confirmación visual

- En algunos casos se muestra `MessageBox("Operación registrada exitosamente")`.

### Resultado

- El evento queda registrado.
- La aplicación puede auditar eventos por usuario, tipo, y resultado.




```mermaid
flowchart TD
    Start["Inicio del proceso</br>Registrar en histórico"]
    Start --> BuildLog["Construir estructura</br>con datos del evento"]
    BuildLog --> ChooseDestino{"¿Dónde registrar</br>el evento?"}

    ChooseDestino -- Base de datos --> InsertSQL["Ejecutar INSERT</br>en tabla de histórico"]
    InsertSQL --> LogResult1{"¿Inserción en BD</br>fue exitosa?"}
    LogResult1 -- No --> Warn1["Aviso:</br>registro en BD fallido"]
    LogResult1 -- Sí --> End1["Registro exitoso</br>en base de datos"]

    ChooseDestino -- Jaguar remoto --> CallRemote["Llamar componente Jaguar</br>para log remoto"]
    CallRemote --> LogResult2{"¿Llamada remota</br>exitosa?"}
    LogResult2 -- No --> Warn2["Aviso:</br>registro remoto fallido"]
    LogResult2 -- Sí --> End2["Registro exitoso</br>vía Jaguar"]

    ChooseDestino -- DataWindow local --> SetDW["Insertar fila en</br>DataWindow de histórico"]
    SetDW --> CommitDW["Ejecutar Commit</br>en DataWindow"]
    CommitDW --> LogResult3{"¿Commit exitoso?"}
    LogResult3 -- No --> Warn3["Aviso:</br>error al guardar en DW"]
    LogResult3 -- Sí --> End3["Registro exitoso</br>en DataWindow"]
```


[Volver al diagrama principal](./readmeOpenAI002.md)