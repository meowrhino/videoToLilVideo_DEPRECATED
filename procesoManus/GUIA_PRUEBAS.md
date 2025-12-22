# Guía de Pruebas - videoToLilVideo

**Fecha**: 22 de diciembre de 2025  
**Versión**: Con bitrates específicos implementados  
**Estado**: ✅ Listo para probar

---

## 🎯 Objetivo de las Pruebas

Verificar que las **3 opciones de compresión** funcionan correctamente y producen tamaños **significativamente diferentes** según el CRF y bitrate máximo asignado.

---

## 📋 Preparación

### 1. Recargar la Página
```
Ctrl + Shift + R (forzar recarga)
```

### 2. Video de Prueba Recomendado
Usa el mismo video que has estado probando:
- **Nombre**: `RVP_GLOSS_2025_MASTER_h264.mp4`
- **Tamaño**: 49.11 MB
- **Duración**: 0:41
- **Resolución**: 1920x1080 (se escalará a 1280x720)

---

## 🧪 Pruebas a Realizar

### Prueba 1: Alta Calidad (CRF 30)

**Pasos**:
1. Selecciona **"Alta Calidad"** (botón izquierdo)
2. Arrastra o selecciona el video de prueba
3. Espera a que termine la conversión
4. Revisa los logs

**Resultados Esperados**:
```
⚡ USANDO CRF 30 con bitrate máximo 2500k
🎯 CRF configurado: 30 (menor = mejor calidad)
FFmpeg args: ... -crf 30 -b:v 2500k ...
✅ COMPLETADO - CRF 30
📊 Bitrate resultante: 2000-2300 kbps  ← Debe estar en este rango
💾 Tamaño: 10-12 MB  ← Debe estar en este rango
```

**Verificar**:
- ✅ Bitrate entre 2000-2300 kbps
- ✅ Tamaño entre 10-12 MB
- ✅ Reducción ~75-80%

---

### Prueba 2: Balance (CRF 33)

**Pasos**:
1. Selecciona **"Balance"** (botón central)
2. Arrastra o selecciona el **mismo video** de nuevo
3. Espera a que termine la conversión
4. Revisa los logs

**Resultados Esperados**:
```
⚡ USANDO CRF 33 con bitrate máximo 1500k
🎯 CRF configurado: 33 (menor = mejor calidad)
FFmpeg args: ... -crf 33 -b:v 1500k ...
✅ COMPLETADO - CRF 33
📊 Bitrate resultante: 1200-1500 kbps  ← Debe estar en este rango
💾 Tamaño: 6-8 MB  ← Debe estar en este rango
```

**Verificar**:
- ✅ Bitrate entre 1200-1500 kbps
- ✅ Tamaño entre 6-8 MB
- ✅ Reducción ~84-88%
- ✅ **~35% más pequeño que Alta Calidad**

---

### Prueba 3: Máxima Compresión (CRF 37)

**Pasos**:
1. Selecciona **"Máx. Compresión"** (botón derecho)
2. Arrastra o selecciona el **mismo video** de nuevo
3. Espera a que termine la conversión
4. Revisa los logs

**Resultados Esperados**:
```
⚡ USANDO CRF 37 con bitrate máximo 1000k
🎯 CRF configurado: 37 (menor = mejor calidad)
FFmpeg args: ... -crf 37 -b:v 1000k ...
✅ COMPLETADO - CRF 37
📊 Bitrate resultante: 800-1000 kbps  ← Debe estar en este rango
💾 Tamaño: 4-5 MB  ← Debe estar en este rango
```

**Verificar**:
- ✅ Bitrate entre 800-1000 kbps
- ✅ Tamaño entre 4-5 MB
- ✅ Reducción ~90-92%
- ✅ **~55% más pequeño que Alta Calidad**

---

## 📊 Tabla de Verificación

| Opción | Bitrate Esperado | Tamaño Esperado | Reducción | Nombre Archivo |
|--------|------------------|-----------------|-----------|----------------|
| **Alta Calidad** | 2000-2300 kbps | 10-12 MB | ~75-80% | `..._alta.webm` |
| **Balance** | 1200-1500 kbps | 6-8 MB | ~84-88% | `..._balance.webm` |
| **Máxima** | 800-1000 kbps | 4-5 MB | ~90-92% | `..._max.webm` |

---

## 🔍 Qué Buscar en los Logs

### ✅ Señales de Éxito

1. **Bitrate máximo correcto**:
   ```
   ⚡ USANDO CRF 30 con bitrate máximo 2500k
   ```

2. **Comando FFmpeg correcto**:
   ```
   FFmpeg args: ... -crf 30 -b:v 2500k ...
   ```
   ❌ **NO debe aparecer** `-b:v 0`

3. **Bitrates diferentes**:
   - Alta: ~2100 kbps
   - Balance: ~1400 kbps
   - Máxima: ~900 kbps

4. **Tamaños significativamente diferentes**:
   - Alta: ~11 MB
   - Balance: ~7 MB (-36%)
   - Máxima: ~5 MB (-55%)

### ❌ Señales de Problema

1. **Bitrates muy similares**:
   ```
   Alta: 2100 kbps
   Balance: 2000 kbps  ← Solo 5% de diferencia (MAL)
   Máxima: 1900 kbps  ← Solo 10% de diferencia (MAL)
   ```

2. **Tamaños muy similares**:
   ```
   Alta: 11 MB
   Balance: 10.5 MB  ← Solo 5% de diferencia (MAL)
   Máxima: 10 MB  ← Solo 9% de diferencia (MAL)
   ```

3. **Bitrate máximo incorrecto en logs**:
   ```
   ⚡ USANDO CRF 30 con bitrate máximo 0  ← MAL
   ```

---

## 🎬 Prueba de Calidad Visual

Después de las 3 conversiones:

1. **Descarga los 3 videos**:
   - `RVP_GLOSS_2025_MASTER_h264_alta.webm`
   - `RVP_GLOSS_2025_MASTER_h264_balance.webm`
   - `RVP_GLOSS_2025_MASTER_h264_max.webm`

2. **Reproduce los 3 videos** en tu reproductor

3. **Compara la calidad**:
   - **Alta Calidad**: Debe verse excelente, sin artefactos visibles
   - **Balance**: Debe verse muy bien, ligeros artefactos en movimiento rápido
   - **Máxima**: Debe verse bien, algunos artefactos visibles pero aceptables

4. **Verifica el tamaño en disco**:
   - Deben ser **significativamente diferentes** (no solo 5-10%)
   - Diferencia mínima esperada: **30-50%** entre Alta y Máxima

---

## 📝 Reporte de Resultados

### Formato de Reporte

```markdown
## Resultados de Pruebas

### Alta Calidad (CRF 30)
- Bitrate resultante: XXXX kbps
- Tamaño final: XX.XX MB
- Reducción: XX.X%
- Calidad visual: ⭐⭐⭐⭐⭐ / ⭐⭐⭐☆☆ / ⭐☆☆☆☆

### Balance (CRF 33)
- Bitrate resultante: XXXX kbps
- Tamaño final: XX.XX MB
- Reducción: XX.X%
- Calidad visual: ⭐⭐⭐⭐⭐ / ⭐⭐⭐☆☆ / ⭐☆☆☆☆

### Máxima Compresión (CRF 37)
- Bitrate resultante: XXXX kbps
- Tamaño final: XX.XX MB
- Reducción: XX.X%
- Calidad visual: ⭐⭐⭐⭐⭐ / ⭐⭐⭐☆☆ / ⭐☆☆☆☆

### Observaciones
- ¿Los tamaños son significativamente diferentes? Sí / No
- ¿Los bitrates están en los rangos esperados? Sí / No
- ¿La calidad visual es aceptable? Sí / No
- ¿Algún problema o bug? Describe aquí
```

---

## 🐛 Problemas Comunes

### Problema 1: Tamaños Muy Similares

**Síntoma**: Los 3 videos pesan casi lo mismo (diferencia <10%)

**Causa**: El bitrate máximo no se está aplicando correctamente

**Solución**:
1. Verifica que el código esté actualizado (último commit)
2. Fuerza recarga de la página (Ctrl+Shift+R)
3. Revisa los logs para confirmar que aparece `-b:v 2500k` (no `-b:v 0`)

### Problema 2: Conversión Falla

**Síntoma**: "Error al convertir el video"

**Causa**: FFmpeg.js crasheó o se quedó sin memoria

**Solución**:
1. Recarga la página
2. Prueba con un video más pequeño primero
3. Verifica que el video no sea 4K (debe escalarse a 720p automáticamente)

### Problema 3: Logs No Aparecen

**Síntoma**: No ves los logs con emojis (⚡, 🎯, etc.)

**Causa**: Código antiguo sin los nuevos logs

**Solución**:
1. Fuerza recarga de la página (Ctrl+Shift+R)
2. Limpia caché del navegador
3. Verifica que el archivo `script.js` esté actualizado

---

## ✅ Criterios de Éxito

La implementación es **exitosa** si:

1. ✅ **Bitrates diferentes**:
   - Alta: ~2100 kbps
   - Balance: ~1400 kbps (-33%)
   - Máxima: ~900 kbps (-57%)

2. ✅ **Tamaños diferentes**:
   - Alta: ~11 MB
   - Balance: ~7 MB (-36%)
   - Máxima: ~5 MB (-55%)

3. ✅ **Calidad visual aceptable**:
   - Alta: Excelente
   - Balance: Muy buena
   - Máxima: Buena (algunos artefactos pero aceptable)

4. ✅ **Logs correctos**:
   - Aparece "⚡ USANDO CRF X con bitrate máximo Xk"
   - Aparece "-b:v Xk" en FFmpeg args (no "-b:v 0")
   - Aparece "📊 Bitrate resultante: X kbps"

---

## 🎯 Próximos Pasos

Si las pruebas son exitosas:
1. ✅ Confirmar que todo funciona correctamente
2. ✅ Probar con otros tipos de videos (deportes, presentaciones, etc.)
3. ✅ Documentar los resultados finales
4. ✅ Considerar deployment a GitHub Pages

Si las pruebas fallan:
1. ❌ Reportar los resultados con capturas de pantalla
2. ❌ Compartir los logs completos
3. ❌ Investigar y ajustar según sea necesario

---

**¡Buena suerte con las pruebas!** 🚀

Si tienes algún problema o pregunta, revisa la documentación en `procesoManus/` o reporta los resultados.
