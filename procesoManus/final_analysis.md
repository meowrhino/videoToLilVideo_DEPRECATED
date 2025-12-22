# Análisis Completo del Problema de Tamaños Variables

## 📊 Cronología de Resultados

| Video | Tamaño | Hora | Commit | Comando FFmpeg |
|-------|--------|------|--------|----------------|
| (1) | 4.3 MB | 13:07 | Inicial | `-crf X -b:v 800k` |
| (2) | 4.3 MB | 13:14 | Inicial | `-crf X -b:v 800k` |
| (3) | 11 MB | 14:37 | 72468be | `-crf X -b:v 0` ✅ |
| (4) | 9.5 MB | 14:42 | 72468be | `-crf X -b:v 0` ✅ |
| (5) | 10.2 MB | 15:22 | 72468be | `-crf X -b:v 0` ✅ |
| (6) | 2.5 MB | 15:48 | 68049d4 | `-crf X` (sin -b:v) ❌ |
| (7) | 2.6 MB | 15:54 | 68049d4 | `-crf X` (sin -b:v) ❌ |

---

## 🎯 DESCUBRIMIENTO CLAVE

### ✅ **Versión que FUNCIONÓ** (Videos 3, 4, 5)
```javascript
'-crf', crfValue.toString(),
'-b:v', '0',  // ← ESTO FUNCIONÓ
```
**Resultado**: 9.5-11 MB (razonable para 720p)

### ❌ **Versión que FALLÓ** (Videos 6, 7)
```javascript
'-crf', crfValue.toString(),  // Sin -b:v
```
**Resultado**: 2.5-2.6 MB (demasiado pequeño)

---

## 🐛 EL PROBLEMA

**Contraintuitivo pero cierto:**
- `-b:v 0` en VP8 **SÍ funciona** y permite CRF puro
- **Quitar `-b:v`** hace que VP8 use un bitrate por defecto muy bajo

### Documentación vs Realidad

**Lo que dice la documentación:**
> "If neither `-b:v` nor `-crf` are set, the encoder will use a low default bitrate"

**Interpretación INCORRECTA:**
- Pensamos que `-b:v 0` = "sin bitrate especificado"
- Por eso lo quitamos

**Realidad:**
- `-b:v 0` = "sin límite de bitrate, usa CRF puro" ✅
- Sin `-b:v` = "usa bitrate por defecto bajo" ❌

---

## 🔧 SOLUCIÓN

**VOLVER al commit 72468be** que tenía `-b:v 0`.

Ese commit funcionaba correctamente:
- Videos (3), (4), (5) → 9.5-11 MB
- Tamaños razonables
- CRF funcionando

---

## 📝 Comando FFmpeg Correcto

```javascript
const ffmpegArgs = [
  '-i', inputName,
  '-c:v', 'libvpx',
  '-crf', crfValue.toString(),
  '-b:v', '0',  // ← MANTENER ESTO
  '-quality', 'good',
  '-c:a', 'libopus',
  '-cpu-used', '2',
  '-deadline', 'good',
  '-auto-alt-ref', '1',
  '-lag-in-frames', '25',
  '-threads', '4'
];
```

---

## 🎯 Acción Inmediata

1. **Revertir** el último commit (68049d4)
2. **Volver** a usar `-b:v 0`
3. **Probar** de nuevo con las 3 opciones de CRF

---

## 💡 Lección Aprendida

**En VP8/libvpx:**
- `-b:v 0` NO significa "sin bitrate"
- `-b:v 0` significa "sin LÍMITE de bitrate"
- Es necesario para CRF puro

La documentación es confusa, pero los resultados empíricos son claros:
- **Con `-b:v 0`**: 9.5-11 MB ✅
- **Sin `-b:v`**: 2.5-2.6 MB ❌
