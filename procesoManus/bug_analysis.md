# Análisis del Bug de Logs Mezclados

## 🔍 Evidencia del Problema

### Logs Reportados

**Alta Calidad (CRF 30):**
```
Lsize= 2264kB bitrate= 451.4kbits/s
✅ COMPLETADO - CRF 30
📊 Bitrate resultante: 492 kbps
💾 Tamaño: 2.41 MB (-95.1%)
```

**Máx. Compresión (CRF 37):**
```
Lsize= 2251kB bitrate= 448.8kbits/s
✅ COMPLETADO - CRF 37
📊 Bitrate resultante: 498 kbps
💾 Tamaño: 2.44 MB (-95.0%)
```

### Videos Descargados

**Video (6) - "Alta Calidad":**
- Tamaño: 2,526,240 bytes (2.41 MB)
- Bitrate: 491 kbps

**Video (7) - "Máx. Compresión":**
- Tamaño: 2,558,641 bytes (2.44 MB)
- Bitrate: 498 kbps

---

## ⚠️ PROBLEMA CRÍTICO

**Los dos videos son CASI IDÉNTICOS:**
- Diferencia de tamaño: Solo 32 KB (1.3%)
- Diferencia de bitrate: Solo 7 kbps (1.4%)

**Esto NO es normal:**
- CRF 30 debería pesar ~12-15 MB
- CRF 37 debería pesar ~4-6 MB
- Diferencia esperada: ~8-10 MB

**Ambos videos pesan ~2.4 MB**, que es **MUCHO menos** de lo esperado incluso para CRF 37.

---

## 🐛 Diagnóstico

### Problema 1: CRF No Está Funcionando
El bitrate de ~450-500 kbps es **extremadamente bajo** para 720p. Esto sugiere:
1. El CRF NO se está aplicando correctamente
2. O hay otro límite de bitrate oculto

### Problema 2: Logs Mezclados
Los logs se están escribiendo en tarjetas incorrectas porque:
1. El `state.currentVideoId` se sobrescribe cuando empiezas la segunda conversión
2. Los logs usan `state.currentVideoId` en lugar del ID específico del video

---

## 🔍 Causa Raíz

Mirando el código:
```javascript
state.currentVideoId = videoData.id;  // Se sobrescribe
```

Cuando subes dos videos del mismo archivo:
1. Video 1 empieza a convertirse → `state.currentVideoId = video1_id`
2. Video 2 se agrega a la cola → `state.currentVideoId = video2_id` (SOBRESCRIBE)
3. Los logs de Video 1 se escriben en Video 2 porque usan `state.currentVideoId`

---

## 🔧 Solución

### Fix 1: Logs por VideoData ID
En lugar de usar `state.currentVideoId`, pasar el `videoData.id` directamente a los logs.

### Fix 2: Verificar Comando FFmpeg
Asegurarse de que NO hay ningún límite de bitrate oculto.

### Fix 3: Debug CRF
Agregar log para ver exactamente qué CRF se está pasando a FFmpeg.

---

## 🎯 Acción Inmediata

1. Revisar si hay algún parámetro de FFmpeg que limite el bitrate
2. Verificar que el CRF se está pasando correctamente
3. Arreglar el sistema de logs para usar el ID correcto
