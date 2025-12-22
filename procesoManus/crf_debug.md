# Debug del Flujo CRF

## Flujo Actual

1. **Usuario selecciona botón** → `updateCRFFromButton()` → `state.crf = crf`
2. **Usuario sube archivo** → `handleFiles()` → `videoData.crf = state.crf` (captura CRF actual)
3. **Conversión** → `convertVideo()` → `crfValue = videoData.crf ?? state.crf`

## Problema Identificado

El flujo parece correcto. El CRF se captura en el momento de subir el archivo y se usa ese valor en la conversión.

## Posibles Causas del Bug

### 1. **Problema con los valores de CRF en CONFIG**
Los valores podrían estar mal definidos o invertidos.

### 2. **Problema con dataset.crf en HTML**
Los botones podrían tener valores incorrectos en `data-crf`.

### 3. **Problema con parseInt()**
El CRF podría no estar parseándose correctamente.

### 4. **Problema con VP8 y CRF**
VP8 podría estar interpretando los valores CRF de manera diferente.

## Verificación Necesaria

1. Revisar valores en HTML: `data-crf="30"`, `data-crf="33"`, `data-crf="37"`
2. Revisar CONFIG: `CRF_MIN: 30, CRF_MAX: 37, DEFAULT_CRF: 33`
3. Verificar logs de FFmpeg para confirmar qué CRF se está pasando
4. Investigar si VP8 usa CRF de manera diferente a H.264
