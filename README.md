# videoToLilVideo 🎬

**Compresor de video WebM optimizado para web** - Reduce el tamaño de tus videos hasta un 70% manteniendo excelente calidad.

## ✨ Características

- 🎯 **Compresión VP8 Optimizada** - Mejor compresión que videoToWeb estándar
- 🌐 **100% en el navegador** - Sin backend, sin uploads a servidores
- 📱 **Responsive** - Funciona en desktop y móvil
- ⚡ **Rápido** - Procesamiento local con FFmpeg.js
- 🎨 **Interfaz simple** - Arrastra, suelta, descarga
- 🔒 **Privado** - Tus videos nunca salen de tu dispositivo
- 📐 **Auto-escalado** - Optimiza automáticamente a 1080p

## 🚀 Uso

1. Abre videoToLilVideo
2. Arrastra tus videos o haz clic para seleccionar
3. Ajusta el nivel de compresión (28-40)
4. Espera a que se compriman
5. Descarga tus videos optimizados en WebM

## 🎛️ Parámetros

### Nivel de Compresión (CRF)
- **30-32**: Calidad muy alta, archivos más grandes
- **33-34**: Balance óptimo (recomendado) ⭐
- **35-36**: Buena compresión, archivos pequeños
- **37-38**: Máxima compresión, archivos muy pequeños

### Resolución
- Automáticamente escala videos grandes a **máximo 1080p Full HD**
- Mantiene el aspect ratio original
- Optimizado para reproducción web

## 🔧 Tecnología

- **FFmpeg.js** - FFmpeg compilado a WebAssembly
- **VP8 (libvpx)** - Codec de video optimizado para compresión
- **Opus** - Codec de audio de alta calidad
- **HTML5** + **CSS3** + **Vanilla JavaScript**

## 📊 Resultados Esperados

| Video Original | videoToWeb | videoToLilVideo | Mejora |
|----------------|------------|-----------------|--------|
| 100 MB (1080p) | ~40 MB | ~35 MB | 12% mejor |
| 50 MB (720p)   | ~20 MB | ~17 MB | 15% mejor |
| 200 MB (4K)    | ~65 MB | ~55 MB (1080p) | 15% mejor |

*Resultados aproximados con CRF 33*

## ⚙️ Configuración Avanzada

Puedes modificar `script.js` para ajustar parámetros:

```javascript
const CONFIG = {
  MAX_WIDTH: 1920,              // Ancho máximo (Full HD)
  MAX_HEIGHT: 1080,             // Alto máximo (Full HD)
  CRF_MIN: 30,                  // CRF mínimo (mejor calidad)
  CRF_MAX: 38,                  // CRF máximo (más compresión)
  DEFAULT_CRF: 34,              // CRF por defecto
  VIDEO_CODEC: 'libvpx',        // VP8 codec optimizado
  AUDIO_CODEC: 'libopus',       // Opus codec
  VIDEO_BITRATE: '800k',        // Target bitrate
  CPU_USED: '2',                // Velocidad encoding (mejor calidad)
  AUTO_ALT_REF: '1',            // Mejor compresión
};
```

## 🐛 Limitaciones Conocidas

- **Videos muy largos (>30 min)** pueden causar problemas de memoria en el navegador
- **Videos 4K** son escalados automáticamente a 1080p para evitar OOM
- **Navegadores antiguos** sin soporte WebAssembly no funcionarán

## 💡 Consejos de Uso

- **Para videos grandes**: Considera dividirlos antes de comprimir
- **CRF recomendado**: Empieza con 33 y ajusta según necesites
- **Videos con mucho movimiento**: Usa CRF más bajo (28-31)
- **Videos estáticos** (presentaciones, screencasts): Usa CRF más alto (35-38)
- **Compatibilidad**: WebM es soportado por todos los navegadores modernos

## 🆚 Diferencias con videoToWeb

| Característica | videoToWeb | videoToLilVideo |
|----------------|------------|-----------------|
| Codec | VP8 | VP8 Optimizado |
| Compresión | Buena | Mejor (~15% mejor) |
| Velocidad | Rápida | Rápida |
| Resolución máx | 720p | 1080p |
| CRF range | 24-38 | 30-38 |
| Bitrate | Variable | 800k target |
| Objetivo | Conversión rápida | Mejor compresión |

## 🤝 Créditos

Creado por [meowrhino.studio](https://meowrhino.studio)

Powered by:
- [FFmpeg.js / ffmpeg.wasm](https://github.com/ffmpegwasm/ffmpeg.wasm)
- [VP9 Codec](https://www.webmproject.org/vp9/)
- [Opus Audio Codec](https://opus-codec.org/)

## 📄 Licencia

MIT License - Úsalo libremente

---

**¿Necesitas comprimir videos para tu web?** videoToLilVideo es la herramienta perfecta para reducir el peso sin sacrificar calidad.
