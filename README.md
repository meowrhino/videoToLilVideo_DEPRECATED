# videoToLilVideo 🎬

**Compresor de video WebM optimizado para web** - Reduce el tamaño de tus videos hasta un 70% manteniendo excelente calidad.

## ✨ Características

- 🎯 **Compresión VP9** - Codec de última generación para máxima eficiencia
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
- **28-30**: Calidad muy alta, archivos más grandes
- **31-33**: Balance óptimo (recomendado) ⭐
- **34-37**: Buena compresión, archivos pequeños
- **38-40**: Máxima compresión, archivos muy pequeños

### Resolución
- Automáticamente escala videos grandes a **máximo 1080p Full HD**
- Mantiene el aspect ratio original
- Optimizado para reproducción web

## 🔧 Tecnología

- **FFmpeg.js** - FFmpeg compilado a WebAssembly
- **VP9 (libvpx-vp9)** - Codec de video de última generación
- **Opus** - Codec de audio de alta calidad
- **HTML5** + **CSS3** + **Vanilla JavaScript**

## 📊 Resultados Esperados

| Video Original | Después de videoToLilVideo | Reducción |
|----------------|----------------------------|-----------|
| 100 MB (1080p) | ~30 MB                     | ~70%      |
| 50 MB (720p)   | ~15 MB                     | ~70%      |
| 200 MB (4K)    | ~50 MB (escalado a 1080p)  | ~75%      |

*Resultados aproximados con CRF 33*

## ⚙️ Configuración Avanzada

Puedes modificar `script.js` para ajustar parámetros:

```javascript
const CONFIG = {
  MAX_WIDTH: 1920,              // Ancho máximo (Full HD)
  MAX_HEIGHT: 1080,             // Alto máximo (Full HD)
  CRF_MIN: 28,                  // CRF mínimo (mejor calidad)
  CRF_MAX: 40,                  // CRF máximo (más compresión)
  DEFAULT_CRF: 33,              // CRF por defecto
  VIDEO_CODEC: 'libvpx-vp9',    // VP9 codec
  AUDIO_CODEC: 'libopus',       // Opus codec
  CPU_USED: '4',                // Velocidad encoding (0-5)
  ROW_MT: '1',                  // Multithreading
};
```

## 🐛 Limitaciones Conocidas

- **Videos muy largos (>30 min)** pueden causar problemas de memoria en el navegador
- **Videos 4K** son escalados automáticamente a 1080p para evitar OOM
- **Navegadores antiguos** sin soporte WebAssembly no funcionarán
- **VP9 es más lento** que VP8, pero ofrece mejor compresión

## 💡 Consejos de Uso

- **Para videos grandes**: Considera dividirlos antes de comprimir
- **CRF recomendado**: Empieza con 33 y ajusta según necesites
- **Videos con mucho movimiento**: Usa CRF más bajo (28-31)
- **Videos estáticos** (presentaciones, screencasts): Usa CRF más alto (35-38)
- **Compatibilidad**: WebM es soportado por todos los navegadores modernos

## 🆚 Diferencias con videoToWeb

| Característica | videoToWeb | videoToLilVideo |
|----------------|------------|-----------------|
| Codec | VP8 | VP9 |
| Compresión | Buena | Excelente (~30% mejor) |
| Velocidad | Rápida | Media |
| Resolución máx | 720p | 1080p |
| CRF range | 24-38 | 28-40 |
| Objetivo | Conversión rápida | Máxima compresión |

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
