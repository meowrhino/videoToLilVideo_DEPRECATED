# Análisis de Resultados Reales vs Predicciones

**Fecha**: 22 de diciembre de 2025  
**Video de prueba**: RVP_GLOSS_2025_MASTER_h264.mp4  
**Original**: 49.11 MB, 1920x1080, 41s

---

## 📊 Tabla Comparativa

| Opción | CRF | Predicción | Resultado Real | Diferencia | Bitrate Predicho | Bitrate Real |
|--------|-----|------------|----------------|------------|------------------|--------------|
| **Alta Calidad** | 30 | 10-12 MB | **11 MB** ✅ | +0.5 MB | 1900-2300 kbps | **2144 kbps** ✅ |
| **Balance** | 33 | 7-9 MB | **10.2 MB** ❌ | +2.7 MB | 1400-1800 kbps | **1989 kbps** ❌ |
| **Máx. Compresión** | 37 | 4-6 MB | **9.5 MB** ❌ | +4.5 MB | 800-1200 kbps | **1854 kbps** ❌ |

---

## 🔍 Análisis Detallado

### ✅ Alta Calidad (CRF 30) - CORRECTO
```
Predicción: 10-12 MB, 1900-2300 kbps
Resultado:  11 MB, 2144 kbps
Estado: ✅ DENTRO DEL RANGO ESPERADO
```

**Observaciones**:
- Tamaño exactamente en el rango predicho
- Bitrate perfecto (2144 kbps)
- Calidad excelente según usuario
- **Funciona correctamente**

---

### ❌ Balance (CRF 33) - INCORRECTO
```
Predicción: 7-9 MB, 1400-1800 kbps
Resultado:  10.2 MB, 1989 kbps
Diferencia: +2.7 MB, +389 kbps
Estado: ❌ FUERA DEL RANGO (muy alto)
```

**Observaciones**:
- Tamaño 30% mayor de lo esperado
- Bitrate similar a CRF 30 (debería ser menor)
- Apenas difiere de Alta Calidad
- **NO funciona como esperado**

---

### ❌ Máxima Compresión (CRF 37) - INCORRECTO
```
Predicción: 4-6 MB, 800-1200 kbps
Resultado:  9.5 MB, 1854 kbps
Diferencia: +4.5 MB, +854 kbps
Estado: ❌ FUERA DEL RANGO (muy alto)
```

**Observaciones**:
- Tamaño 90% mayor de lo esperado
- Bitrate casi idéntico a Balance
- Apenas difiere de Alta Calidad
- **NO funciona como esperado**

---

## 🐛 Problema Identificado

### Los 3 CRF generan tamaños MUY similares

| Opción | Tamaño | Diferencia con Alta |
|--------|--------|---------------------|
| Alta (CRF 30) | 11 MB | - |
| Balance (CRF 33) | 10.2 MB | -7% |
| Máxima (CRF 37) | 9.5 MB | -14% |

**Esperado**:
- CRF 30 → 11 MB
- CRF 33 → ~7 MB (-36%)
- CRF 37 → ~5 MB (-55%)

**Real**:
- CRF 30 → 11 MB
- CRF 33 → 10.2 MB (-7%) ← Demasiado alto
- CRF 37 → 9.5 MB (-14%) ← Demasiado alto

---

## 🔍 Análisis de Logs FFmpeg

### Alta Calidad (CRF 30)
```
Lsize= 10656kB time=00:00:41.09 bitrate=2124.2kbits/s
✅ COMPLETADO - CRF 30
📊 Bitrate resultante: 2145 kbps
💾 Tamaño: 10.50 MB
```
✅ **Correcto**

### Balance (CRF 33)
```
Lsize= 9546kB time=00:00:41.09 bitrate=1903.1kbits/s
✅ COMPLETADO - CRF 33
📊 Bitrate resultante: 1990 kbps
💾 Tamaño: 9.74 MB
```
❌ **Bitrate demasiado alto para CRF 33**

### Máxima Compresión (CRF 37)
```
Lsize= 8419kB time=00:00:41.09 bitrate=1678.4kbits/s
✅ COMPLETADO - CRF 37
📊 Bitrate resultante: 1854 kbps
💾 Tamaño: 9.08 MB
```
❌ **Bitrate demasiado alto para CRF 37**

---

## 🎯 Causa Raíz del Problema

### Hipótesis 1: CRF no se está aplicando correctamente
**Evidencia**:
- CRF 30, 33 y 37 producen bitrates muy similares
- Diferencia de solo ~300 kbps entre CRF 30 y 37
- Esperado: ~1500 kbps de diferencia

**Posible causa**:
- Algún parámetro está limitando el rango de CRF
- `-b:v 0` no está funcionando como esperado en FFmpeg.js
- VP8 en WASM tiene limitaciones de CRF

### Hipótesis 2: Parámetros adicionales interfieren
**Evidencia**:
- `-cpu-used 2` podría estar limitando la compresión
- `-deadline good` podría estar priorizando velocidad
- `-quality good` podría estar sobrescribiendo CRF

**Posible causa**:
- Conflicto entre `-crf` y `-quality`
- Parámetros de velocidad limitan compresión

### Hipótesis 3: VP8 en FFmpeg.js tiene rango limitado
**Evidencia**:
- CRF 37 debería comprimir mucho más
- Bitrate mínimo ~1700 kbps (no baja de ahí)
- Posible límite interno del build WASM

**Posible causa**:
- FFmpeg.js compilado con límites conservadores
- VP8 en WASM no soporta CRF alto efectivamente

---

## 🔬 Pruebas Necesarias

### 1. Verificar comando FFmpeg real
```bash
# Ver exactamente qué comando se está ejecutando
# Buscar en logs: "FFmpeg args:"
```

### 2. Probar sin parámetros de velocidad
```javascript
// Quitar:
'-cpu-used', '2',
'-deadline', 'good',
```

### 3. Probar con -quality best
```javascript
// Cambiar:
'-quality', 'good'  // a:
'-quality', 'best'
```

### 4. Probar CRF más extremos
```javascript
// Probar:
CRF 10 (muy alta calidad)
CRF 50 (muy baja calidad)
```

---

## 💡 Teoría Principal

**El problema NO es `-b:v 0`** (Alta Calidad funciona perfectamente).

**El problema es que CRF 33 y 37 no están comprimiendo lo suficiente.**

Posibles razones:

1. **`-quality good` sobrescribe CRF parcialmente**
   - VP8 tiene dos sistemas de control: CRF y quality
   - Pueden estar en conflicto

2. **`-cpu-used 2` limita compresión**
   - Valor más bajo = más tiempo = mejor compresión
   - Valor 2 podría estar limitando CRF alto

3. **FFmpeg.js tiene límite interno**
   - Build WASM podría tener bitrate mínimo ~1700 kbps
   - CRF >30 no funciona efectivamente

---

## 🎯 Recomendaciones

### Opción A: Ajustar parámetros de calidad
```javascript
// Para CRF 33 y 37, usar:
'-quality', 'best',  // En lugar de 'good'
'-cpu-used', '0',    // En lugar de '2'
```

### Opción B: Usar bitrate target en lugar de CRF
```javascript
// Para Balance y Máxima:
'-b:v', '1200k',  // Balance
'-b:v', '800k',   // Máxima
// Sin -crf
```

### Opción C: Ajustar valores CRF
```javascript
// Si CRF 37 no comprime suficiente:
CRF_ALTA: 25,      // En lugar de 30
CRF_BALANCE: 30,   // En lugar de 33
CRF_MAXIMA: 35,    // En lugar de 37
```

---

## 📊 Conclusión

**Alta Calidad (CRF 30) funciona perfectamente** ✅

**Balance y Máxima NO comprimen lo suficiente** ❌

**Causa probable**: Conflicto entre `-crf` y otros parámetros de calidad/velocidad en VP8.

**Solución recomendada**: Probar Opción A (ajustar quality y cpu-used para CRF altos).

---

**Siguiente paso**: Implementar pruebas para validar hipótesis.
