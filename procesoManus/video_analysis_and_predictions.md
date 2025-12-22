# Análisis de Videos y Predicciones

## 📊 Análisis de Videos Subidos

Video original: **RVP_GLOSS_2025_MASTER_h264.mp4**
- Tamaño: 49.11 MB
- Duración: 41.096 segundos
- Resolución original: 1920x1080
- Resolución convertida: 1280x720

### Video (2) - 4.26 MB
```
Tamaño: 4,261,898 bytes (4.06 MB)
Bitrate: 829 kbps
Duración: 41.096s
Resolución: 1280x720
```

### Video (4) - 9.52 MB
```
Tamaño: 9,521,460 bytes (9.08 MB)
Bitrate: 1,853 kbps
Duración: 41.096s
Resolución: 1280x720
```

### Video (5) - 10.22 MB
```
Tamaño: 10,216,183 bytes (9.74 MB)
Bitrate: 1,988 kbps
Duración: 41.096s
Resolución: 1280x720
```

---

## 🔍 Observaciones

1. **Video (2)** tiene el bitrate más bajo (829 kbps) → Probablemente CRF alto o limitado por bitrate
2. **Video (4)** y **(5)** tienen bitrates similares (~1850-1990 kbps) → CRF similar
3. La diferencia entre (4) y (5) es mínima (135 kbps) → Sugiere que el CRF no está funcionando correctamente

---

## 🎯 Predicciones con CRF Puro (SIN -b:v)

Para tu video específico (720p, 41s, contenido con movimiento):

| Opción | CRF | Bitrate Esperado | Tamaño Esperado | Reducción |
|--------|-----|------------------|-----------------|-----------|
| **Alta Calidad** | 30 | 2500-3000 kbps | 12-15 MB | ~70-75% |
| **Balance** | 33 | 1500-2000 kbps | 7-10 MB | ~80-85% |
| **Máx. Compresión** | 37 | 800-1200 kbps | 4-6 MB | ~88-92% |

---

## 📋 Valores de Referencia VP8

### Escala de Calidad VP8 (CRF)
- **CRF 4-10**: Calidad casi sin pérdida (muy pesado)
- **CRF 10-20**: Calidad excelente
- **CRF 20-30**: Calidad alta (recomendado para web)
- **CRF 30-40**: Calidad media (balance compresión/calidad)
- **CRF 40-50**: Calidad baja
- **CRF 50-63**: Calidad muy baja

### Nuestros Valores Actuales
- **CRF 30**: Límite superior de "calidad alta"
- **CRF 33**: Zona de "calidad media-alta"
- **CRF 37**: Zona de "calidad media"

---

## ✅ Verificación Post-Fix

Después de quitar `-b:v`, deberías ver:

### CRF 30 (Alta Calidad)
```
✅ Bitrate: 2500-3000 kbps
✅ Tamaño: 12-15 MB
✅ Calidad: Excelente para web
```

### CRF 33 (Balance)
```
✅ Bitrate: 1500-2000 kbps
✅ Tamaño: 7-10 MB
✅ Calidad: Muy buena para web
```

### CRF 37 (Máx. Compresión)
```
✅ Bitrate: 800-1200 kbps
✅ Tamaño: 4-6 MB
✅ Calidad: Buena para web
```

---

## 🐛 Debugging: Qué Buscar en los Logs

### 1. Comando FFmpeg
Debe mostrar:
```
-crf 30  (sin -b:v)
```

### 2. Bitrate Final
```
Lsize= XXXXX bitrate= XXXX kbits/s
```
- CRF 30 → ~2500-3000 kbps
- CRF 33 → ~1500-2000 kbps
- CRF 37 → ~800-1200 kbps

### 3. Tamaño Final
- CRF 30 > CRF 33 > CRF 37 (en tamaño)
- Diferencia clara entre cada opción (no similar)

---

## 📝 Logs Útiles para Debugging

Agregar al log:
1. ✅ **CRF usado**: "Usando CRF: 30"
2. ✅ **Comando FFmpeg completo**: Para verificar parámetros
3. ✅ **Bitrate final**: Del output de FFmpeg
4. ✅ **Tamaño final**: En MB con 2 decimales
5. ✅ **Reducción**: Porcentaje vs original

Quitar del log:
1. ❌ Progreso frame por frame (demasiado verbose)
2. ❌ Warnings innecesarios
3. ❌ Información redundante
