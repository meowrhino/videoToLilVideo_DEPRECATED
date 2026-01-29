# Proceso de Desarrollo - videoToLilVideo

## 2026-01-29 16:00 - Fix: Worker se queda colgado en conversiones

### Sinopsis
La conversión de videos se quedaba colgada a medio proceso (típicamente al 1%) después de la primera conversión o con ciertos archivos. El problema fue identificado tras revisar una conversación previa con Claude sobre videoToWeb.

### Problema Identificado

El código tenía una condición que **reutilizaba el worker de FFmpeg** después de la primera conversión:

```javascript
if (!state.ffmpeg || !state.ffmpegLoaded) {
  // Crear worker
} else {
  debugLog('[convertVideo] Reutilizando worker ya cargado');
}
```

Este build específico de **ffmpeg.js no soporta bien la reutilización** del worker. Después de una ejecución, el worker queda en un estado inconsistente y las siguientes conversiones se cuelgan o fallan silenciosamente.

### Síntomas Observados

1. Primera conversión funciona correctamente
2. Segunda conversión (sin recargar la página) se queda colgada al 1-2%
3. Los logs muestran que FFmpeg está procesando frames pero el progreso no avanza
4. El worker no responde o responde muy lentamente

### Solución Implementada

Modificar la función `convertVideo()` para **siempre terminar y recrear el worker** antes de cada conversión, eliminando la lógica de reutilización:

```javascript
// Terminar worker anterior si existe
if (state.ffmpeg) {
  try {
    state.ffmpeg.terminate();
    debugLog('[convertVideo] Worker anterior terminado');
  } catch (e) {
    debugLog('[convertVideo] Error terminando worker anterior:', e);
  }
  state.ffmpeg = null;
}

// Crear nuevo worker
state.ffmpeg = new Worker(CONFIG.WORKER_PATH);
state.ffmpegLoaded = false;

await new Promise((resolve, reject) => {
  const timeout = setTimeout(() => reject(new Error('Timeout esperando worker')), CONFIG.WORKER_READY_TIMEOUT);
  state.ffmpeg.onmessage = (e) => {
    if (e.data.type === 'ready') {
      clearTimeout(timeout);
      debugLog('[convertVideo] ✓ Worker listo');
      state.ffmpegLoaded = true;
      resolve();
    }
  };
  state.ffmpeg.onerror = (err) => {
    clearTimeout(timeout);
    reject(err);
  };
});
```

### Cambios Realizados

**Archivo**: `script.js` (líneas 238-275)

- Eliminada la condición `if (!state.ffmpeg || !state.ffmpegLoaded)`
- Añadido bloque para terminar worker anterior con try-catch
- Siempre se crea un nuevo worker antes de cada conversión
- Actualizado comentario para explicar por qué no se reutiliza el worker

### Resultado Esperado

- Las conversiones múltiples sin recargar la página ahora funcionarán correctamente
- Cada conversión tendrá un worker limpio sin estado residual
- Eliminación de cuelgues y comportamientos inconsistentes

### Notas Técnicas

Este comportamiento es específico del build de ffmpeg.js usado en el proyecto. Otros builds o versiones más modernas de ffmpeg.wasm podrían soportar reutilización del worker, pero este build particular requiere recreación completa.

La penalización de performance por recrear el worker (~1-2 segundos) es mínima comparada con el tiempo total de conversión (varios minutos para videos largos) y es un trade-off necesario para garantizar estabilidad.

### Referencias

- Conversación con Claude sobre videoToWeb donde se identificó el mismo problema
- Commit c612ffb de videoToWeb que funcionaba correctamente con recreación de worker
