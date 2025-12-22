# Investigación: Parámetros Óptimos de Compresión de Video para Web

## Fuentes Consultadas
1. **Think Branded Media** - Best Video Resolution & Bitrate for Fast Web Loading
2. **Pixflow** - Video Compression Guide: Bitrate, Resolution, & Quality
3. **Dacast** - Best Video Codec for Streaming & Quality in 2025

## Recomendaciones de Resolución

| Resolución | Uso Recomendado | Características |
|-----------|-----------------|-----------------|
| **720p (HD)** | Móvil, carga rápida | 1280 x 720, buena velocidad de carga |
| **1080p (Full HD)** | Web general, redes sociales | 1920 x 1080, mejor balance calidad-peso |
| **4K (Ultra HD)** | Contenido premium, futuro-proof | 3840 x 2160, requiere mucho ancho de banda |

**Recomendación para videoToLilVideo: 1080p máximo (escalable hacia abajo)**

## Recomendaciones de Bitrate

### Para Web Delivery (VBR - Variable Bitrate):
- **1080p**: 8-12 Mbps (recomendado: 10 Mbps)
- **720p**: 4-6 Mbps (recomendado: 5 Mbps)
- **480p**: 2-3 Mbps (recomendado: 2.5 Mbps)

### Notas Importantes:
- **VBR (Variable Bitrate)** es preferible a CBR para web
- VBR ajusta el bitrate según la complejidad de la escena
- Produce archivos más pequeños con mínima pérdida de calidad

## Codec Recomendado

### Opciones Principales:
1. **H.264 (AVC)** - ✅ RECOMENDADO PARA videoToLilVideo
   - Compatibilidad universal (todos los dispositivos últimos 10 años)
   - Balance excelente calidad-compresión
   - Estándar de facto para streaming web
   - Compresión ~50% mejor que generaciones anteriores

2. **H.265 (HEVC)**
   - ~50% más eficiente que H.264
   - Mejor para 4K
   - Menor compatibilidad en algunos dispositivos antiguos

3. **AV1**
   - Mejor compresión que H.265
   - Aún en adopción, menos compatible

## Parámetros Recomendados para videoToLilVideo

### Configuración Primaria (Balanceada):
```
Codec: H.264 (libx264)
Bitrate: Variable (VBR)
Resolución: Escalar a 1080p máximo (mantener aspect ratio)
Frame Rate: 30 fps (estándar web)
Audio Codec: AAC-LC
Audio Bitrate: 128 kbps
Preset: medium (balance velocidad-compresión)
```

### Configuración Alternativa (Máxima Compresión):
```
Codec: H.264 (libx264)
Bitrate: 5-8 Mbps
Resolución: 720p máximo
Frame Rate: 24 fps
Audio Bitrate: 96 kbps
Preset: slow (mejor compresión, más tiempo)
```

## Cálculo de Tamaño de Archivo

**Fórmula**: File Size (MB) = (Bitrate en Mbps × Duración en segundos) / 8

**Ejemplos:**
- 10 min video 1080p @ 10 Mbps = 750 MB
- 10 min video 720p @ 5 Mbps = 375 MB
- 10 min video 480p @ 2.5 Mbps = 187.5 MB

## Formatos de Entrada Soportados

FFmpeg soporta prácticamente todos los formatos:
- MP4, MOV, AVI, WebM, FLV, MKV, 3GP, WMV, etc.

## Estrategia para videoToLilVideo

1. **Detectar resolución original del video**
2. **Si es mayor a 1080p**: escalar a 1080p
3. **Aplicar H.264 con VBR 8-10 Mbps**
4. **Mantener 30 fps**
5. **Audio AAC-LC 128 kbps**
6. **Salida: MP4**

Esto debería proporcionar:
- ✅ Buena calidad visual
- ✅ Archivo significativamente más pequeño
- ✅ Compatibilidad universal
- ✅ Carga rápida en web
