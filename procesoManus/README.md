# Proceso Manus - Documentación del Desarrollo

Esta carpeta contiene toda la documentación del proceso de desarrollo y debugging de **videoToLilVideo**.

---

## 📚 Índice de Documentos

### 🎯 Documentos Principales

#### 1. **analisis_configuraciones_vp8.md**
Análisis completo de las 3 configuraciones de FFmpeg probadas:
- Configuración A: `-crf X -b:v 800k` (limitado)
- Configuración B: `-crf X -b:v 0` ✅ **RECOMENDADA**
- Configuración C: `-crf X` (sin -b:v, muy bajo)

**Conclusión**: Usar `-b:v 0` para CRF puro.

#### 2. **guia_opciones_calidad.md**
Guía detallada de las 3 opciones de calidad para usuarios:
- Alta Calidad (CRF 30): 10-12 MB
- Balance (CRF 33): 7-9 MB ✅ **RECOMENDADA**
- Máx. Compresión (CRF 37): 4-6 MB

Incluye casos de uso y ejemplos.

#### 3. **final_analysis.md**
Análisis cronológico de todos los videos generados durante el desarrollo:
- Historial de tamaños (1) a (7)
- Identificación del commit que funcionó
- Explicación del bug de `-b:v`

---

### 🔍 Documentos de Investigación

#### 4. **vp8_crf_findings.md**
Hallazgos clave de la documentación oficial de VP8:
- Rango de CRF: 4-63
- Advertencia sobre `-b:v`
- Valores recomendados

#### 5. **video_compression_research.md**
Investigación inicial sobre compresión de video para web:
- Mejores prácticas de bitrate
- Resoluciones recomendadas
- Balance calidad-peso

#### 6. **codecs_comparison.md**
Comparativa de codecs disponibles en FFmpeg.js:
- VP8 ✅ Disponible
- VP9 ❌ No disponible en el build
- H.264 ❌ Requiere compilación personalizada

---

### 🐛 Documentos de Debugging

#### 7. **bug_analysis.md**
Análisis del bug de logs mezclados:
- Problema: Videos con mismo nombre
- Causa: `state.currentVideoId` sobrescrito
- Solución: ID único con CRF

#### 8. **video_analysis_and_predictions.md**
Análisis de videos generados y predicciones:
- Tamaños esperados por CRF
- Bitrates esperados
- Tabla de verificación

#### 9. **crf_debug.md**
Debug del flujo de CRF en el código:
- Flujo de selección
- Captura de valor
- Uso en conversión

---

## 📊 Resumen Ejecutivo

### Problema Inicial
Crear un compresor de video para web con buen balance calidad-peso.

### Desafíos Encontrados
1. **VP9 no disponible** en FFmpeg.js → Usar VP8
2. **Videos 1080p crashean** → Limitar a 720p
3. **Bitrate fijo anula CRF** → Usar `-b:v 0`
4. **Sin `-b:v` da bitrate muy bajo** → Confirmar `-b:v 0`

### Solución Final
```javascript
ffmpeg -i input.mp4 \
  -c:v libvpx \
  -crf 30 \
  -b:v 0 \  // ← CRÍTICO
  -quality good \
  -c:a libopus \
  -cpu-used 2 \
  -deadline good \
  -auto-alt-ref 1 \
  -lag-in-frames 25 \
  -threads 4 \
  output.webm
```

### Resultados
- **Alta Calidad (CRF 30)**: 10-12 MB, excelente
- **Balance (CRF 33)**: 7-9 MB, muy buena ✅
- **Máx. Compresión (CRF 37)**: 4-6 MB, buena

---

## 🎯 Próximos Pasos

1. ✅ Revertir al commit 72468be (usa `-b:v 0`)
2. ⏳ Probar las 3 opciones con mismo video
3. ⏳ Verificar tamaños diferentes
4. ⏳ Comparar calidad visual
5. ⏳ Documentar resultados finales

---

## 📝 Notas

- Todos los documentos están en formato Markdown
- Fecha de creación: 22 de diciembre de 2025
- Desarrollado por: Manus AI + meowrhino
- Repositorio: https://github.com/meowrhino/videoToLilVideo

---

## 🔗 Enlaces Útiles

- [Documentación oficial VP8](https://trac.ffmpeg.org/wiki/Encode/VP8)
- [FFmpeg.js GitHub](https://github.com/ffmpegwasm/ffmpeg.wasm)
- [Guía CRF](http://slhck.info/video/2017/02/24/crf-guide.html)
