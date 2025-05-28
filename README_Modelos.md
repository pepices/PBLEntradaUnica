# Modelos Disponibles en Ollama

## Tabla de Modelos

| Modelo | Tamaño | Uso Principal | Ventajas | Requisitos | Ejemplo de Pull |
|--------|---------|---------------|-----------|------------|-----------------|
| **llama2** | 7B | General | Buen balance rendimiento/recursos | 8GB RAM | `ollama pull llama2` |
| **llama2:13b** | 13B | General | Mejor calidad que 7B | 16GB RAM | `ollama pull llama2:13b` |
| **llama2:70b** | 70B | Tareas complejas | Máxima calidad | 32GB+ RAM | `ollama pull llama2:70b` |
| **mistral** | 7B | Chat | Excelente rendimiento | 8GB RAM | `ollama pull mistral` |
| **mixtral** | 8x7B | Tareas avanzadas | Mejor que Mistral | 16GB+ RAM | `ollama pull mixtral` |
| **codellama** | 7B | Programación | Optimizado para código | 8GB RAM | `ollama pull codellama` |
| **codellama:13b** | 13B | Programación | Mejor que 7B | 16GB RAM | `ollama pull codellama:13b` |
| **neural-chat** | 7B | Chat | Bueno para conversación | 8GB RAM | `ollama pull neural-chat` |
| **orca-mini** | 3B | Chat ligero | Muy eficiente | 4GB RAM | `ollama pull orca-mini` |
| **deepseek-coder** | 6.7B | Programación | Especializado en código | 8GB RAM | `ollama pull deepseek-coder` |

## Características Adicionales

| Característica | Descripción | Modelos que la tienen |
|----------------|-------------|----------------------|
| Multilingüe | Buen rendimiento en múltiples idiomas | mistral, neural-chat |
| Código | Optimizado para programación | codellama, deepseek-coder |
| Chat | Optimizado para conversación | neural-chat, orca-mini |
| General | Bueno para tareas generales | llama2, mistral |
| Eficiente | Bajo consumo de recursos | orca-mini, llama2:7b |
| Potente | Alta calidad de respuestas | llama2:70b, mixtral |

## Recomendaciones por Caso de Uso

### 1. Desarrollo de Software
- **Mejor opción:** `codellama:13b`
- **Alternativa ligera:** `codellama:7b`

### 2. Chat General
- **Mejor opción:** `mistral`
- **Alternativa ligera:** `orca-mini`

### 3. Tareas Complejas
- **Mejor opción:** `llama2:70b`
- **Alternativa:** `mixtral`

### 4. Recursos Limitados
- **Mejor opción:** `orca-mini`
- **Alternativa:** `llama2:7b`

### 5. Multilingüe
- **Mejor opción:** `mistral`
- **Alternativa:** `neural-chat`

## Notas Importantes

- Los requisitos de RAM son aproximados
- El rendimiento real puede variar según el hardware
- Algunos modelos pueden requerir GPU para mejor rendimiento
- Los tiempos de respuesta varían según el tamaño del modelo

## Instalación

Para instalar cualquier modelo, usa el comando:
```bash
ollama pull [nombre-modelo]
```

Por ejemplo:
```bash
ollama pull mistral
ollama pull codellama:13b
ollama pull neural-chat
```

## Verificación

Para ver los modelos instalados:
```bash
ollama list
```

## Actualización

Para actualizar un modelo a su última versión:
```bash
ollama pull [nombre-modelo] --latest
``` 