# Comparativa de Codecs Disponibles en FFmpeg.js (WASM para Navegador)

## Resumen de Investigación

Después de investigar FFmpeg.js y sus capacidades, aquí está la comparativa de formatos de salida disponibles:

---

## ✅ Codecs DISPONIBLES en FFmpeg.js WASM

### 1. **WebM con VP8** (Actual en videoToWeb)
- **Codec Video**: libvpx (VP8)
- **Codec Audio**: libopus / libvorbis
- **Extensión**: `.webm`
- **Compatibilidad**: ✅ Todos los navegadores modernos
- **Compresión**: ⭐⭐⭐ (Buena)
- **Calidad**: ⭐⭐⭐ (Buena)
- **Velocidad**: ⭐⭐⭐⭐ (Rápida)
- **Limitaciones**: Compresión menos eficiente que VP9 o H.264

**Veredicto**: Funciona bien, pero no es el más eficiente.

---

### 2. **WebM con VP9** 
- **Codec Video**: libvpx-vp9 (VP9)
- **Codec Audio**: libopus
- **Extensión**: `.webm`
- **Compatibilidad**: ✅ Todos los navegadores modernos (Chrome, Firefox, Edge, Safari 14.1+)
- **Compresión**: ⭐⭐⭐⭐⭐ (Excelente - ~30% mejor que VP8)
- **Calidad**: ⭐⭐⭐⭐⭐ (Excelente)
- **Velocidad**: ⭐⭐ (Más lenta que VP8, pero aceptable)
- **Limitaciones**: Más lento que VP8, puede dar problemas de memoria con videos muy grandes

**Veredicto**: **MEJOR OPCIÓN** si funciona sin crashear.

---

### 3. **MP4 con H.264** ❌ (NO DISPONIBLE por defecto)
- **Codec Video**: libx264
- **Codec Audio**: aac
- **Extensión**: `.mp4`
- **Disponibilidad**: ❌ **NO incluido en builds estándar de FFmpeg.js por licencias**
- **Compresión**: ⭐⭐⭐⭐⭐ (Excelente)
- **Calidad**: ⭐⭐⭐⭐⭐ (Excelente)
- **Compatibilidad**: ✅ Universal

**Veredicto**: ❌ No disponible sin compilar FFmpeg.js personalizado (muy complejo).

---

### 4. **MP4 con MPEG-4** (Alternativa básica)
- **Codec Video**: mpeg4
- **Codec Audio**: aac
- **Extensión**: `.mp4`
- **Disponibilidad**: ⚠️ Puede estar disponible, pero es antiguo
- **Compresión**: ⭐⭐ (Regular)
- **Calidad**: ⭐⭐⭐ (Aceptable)
- **Compatibilidad**: ✅ Universal

**Veredicto**: Inferior a VP8/VP9, no recomendado.

---

### 5. **AVI con MJPEG** (Motion JPEG)
- **Codec Video**: mjpeg
- **Codec Audio**: mp3 / pcm
- **Extensión**: `.avi`
- **Disponibilidad**: ✅ Disponible
- **Compresión**: ⭐ (Muy pobre - archivos enormes)
- **Calidad**: ⭐⭐⭐⭐ (Buena)
- **Compatibilidad**: ✅ Universal

**Veredicto**: ❌ Archivos demasiado grandes, no sirve para web.

---

### 6. **MOV con H.264** ❌ (NO DISPONIBLE)
- **Codec Video**: libx264
- **Codec Audio**: aac
- **Extensión**: `.mov`
- **Disponibilidad**: ❌ Mismo problema que MP4 (requiere libx264)

**Veredicto**: ❌ No disponible.

---

## 📊 Tabla Comparativa Resumida

| Formato | Codec | Disponible | Compresión | Calidad | Velocidad | Recomendado |
|---------|-------|------------|------------|---------|-----------|-------------|
| **WebM (VP9)** | libvpx-vp9 | ✅ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐ | **🏆 SÍ** |
| **WebM (VP8)** | libvpx | ✅ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ✅ Sí |
| **MP4 (H.264)** | libx264 | ❌ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ❌ No disponible |
| **MP4 (MPEG-4)** | mpeg4 | ⚠️ | ⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ❌ No |
| **AVI (MJPEG)** | mjpeg | ✅ | ⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ❌ No |

---

## 🎯 Recomendación Final para videoToLilVideo

### **Opción 1: WebM con VP9** (RECOMENDADA)
```bash
ffmpeg -i input.* -c:v libvpx-vp9 -crf 30 -b:v 0 \
  -vf "scale='min(1920,iw)':'min(1080,ih)':force_original_aspect_ratio=decrease" \
  -c:a libopus -b:a 128k output.webm
```

**Ventajas:**
- ✅ Mejor compresión disponible en FFmpeg.js
- ✅ ~30% más eficiente que VP8
- ✅ Calidad excelente
- ✅ Compatible con todos los navegadores modernos
- ✅ Sin necesidad de backend

**Desventajas:**
- ⚠️ Más lento que VP8
- ⚠️ Puede dar problemas de memoria con videos 4K muy largos

---

### **Opción 2: WebM con VP8** (Fallback seguro)
```bash
ffmpeg -i input.* -c:v libvpx -crf 30 -b:v 0 \
  -vf "scale='min(1920,iw)':'min(1080,ih)':force_original_aspect_ratio=decrease" \
  -c:a libopus -b:a 128k output.webm
```

**Ventajas:**
- ✅ Más estable que VP9
- ✅ Más rápido
- ✅ Menos problemas de memoria

**Desventajas:**
- ⚠️ Compresión menos eficiente

---

## 🔧 Estrategia Propuesta

**videoToLilVideo debería usar VP9 con fallback a VP8:**

1. **Intentar VP9 primero** (mejor compresión)
2. **Si falla o es muy lento**, usar VP8 automáticamente
3. **Limitar resolución a 1080p** para evitar problemas de memoria
4. **Usar CRF 28-35** para balance calidad-peso

---

## ❌ Por qué NO podemos usar MP4/H.264

FFmpeg.js **NO incluye libx264** en sus builds estándar porque:
- **Licencias**: x264 tiene licencias GPL que complican su distribución en WASM
- **Tamaño**: Incluir x264 aumentaría significativamente el tamaño del WASM
- **Complejidad**: Requiere compilar FFmpeg personalizado desde cero

**Alternativa**: Si realmente necesitas MP4, tendrías que:
1. Compilar FFmpeg.js personalizado con libx264 (muy complejo)
2. Usar un backend con FFmpeg nativo (contradice el objetivo de GitHub Pages)

---

## 🎬 Conclusión

Para **videoToLilVideo** en GitHub Pages sin backend:

**🏆 Mejor opción: WebM con VP9**
- Extensión: `.webm`
- Codec: VP9 + Opus
- CRF: 28-35 (menor = mejor calidad)
- Resolución máxima: 1080p
- Resultado: ~60-70% de reducción de tamaño con excelente calidad

¿Quieres que implemente videoToLilVideo con VP9 optimizado?
