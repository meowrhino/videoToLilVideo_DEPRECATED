# Hallazgos Clave sobre VP8 CRF

## 🔑 Información Crítica de la Documentación Oficial

### Rango de CRF en VP8
- **Rango**: 4-63 (por defecto)
- **Valor recomendado**: 10 como punto de partida
- **Menor valor** = Mejor calidad
- **Mayor valor** = Peor calidad

### ⚠️ ADVERTENCIA IMPORTANTE

**De la documentación oficial:**
> "Important: If neither `-b:v` nor `-crf` are set, the encoder will use a low default bitrate and your result will probably look very bad. Always supply one of these options—ideally both."

**Cuando usas CRF + bitrate:**
> "In this case, the target bitrate becomes the **maximum allowed bitrate**"

### 🐛 EL PROBLEMA ENCONTRADO

Estamos usando:
```bash
-crf 30 -b:v 0
```

**Pero `-b:v 0` NO significa "sin límite"** en VP8. Significa que:
1. Si no se especifica `-b:v`, el encoder usa un bitrate muy bajo por defecto
2. `-b:v 0` podría estar siendo interpretado como "sin bitrate especificado"
3. El encoder entonces **ignora el CRF** y usa su propio bitrate por defecto

## 🔧 Solución Correcta para VP8

**Opción 1: CRF puro SIN bitrate**
```bash
-crf 30  # Sin -b:v
```

**Opción 2: CRF con bitrate máximo alto**
```bash
-crf 30 -b:v 10M  # Bitrate máximo muy alto para no limitar
```

## 📊 Valores CRF Recomendados para VP8

Según la documentación y pruebas:
- **CRF 4-10**: Calidad muy alta (archivos grandes)
- **CRF 10-20**: Calidad alta
- **CRF 20-30**: Calidad media-alta
- **CRF 30-40**: Calidad media
- **CRF 40-50**: Calidad baja
- **CRF 50-63**: Calidad muy baja

## 🎯 Conclusión

**Nuestros valores actuales (30, 33, 37) están en el rango medio-bajo de VP8.**

Para mejor calidad deberíamos usar:
- **Alta Calidad**: CRF 10-15
- **Balance**: CRF 20-25
- **Máx. Compresión**: CRF 30-35

**O mantener los valores actuales pero QUITAR `-b:v 0` completamente.**
