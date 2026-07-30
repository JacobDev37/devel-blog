---
layout: post
title:  "Cómo descargar un video que se reproduce en el navegador"
date:   2026-07-29 19:11:04 -0600
categories: updates
---

En muchas plataformas de cursos o sitios web no existe un botón para descargar los videos. Sin embargo, si el reproductor utiliza **HLS (M3U8)**, es posible obtener la URL del stream desde las herramientas de desarrollo del navegador y descargarlo con **VLC Media Player**.

> **Importante:** Este método solo debe utilizarse para descargar contenido al que tengas autorización de acceso y respetando los términos de uso de la plataforma.

---

## Paso 1: Obtener la URL del stream

1. Abre la página donde se reproduce el video.
2. Presiona **F12** para abrir las herramientas de desarrollo.
3. Ve a la pestaña **Network** (Red).
4. Filtra las solicitudes escribiendo:

```
m3u
```

5. Recarga la página (**F5**).

Si el sitio utiliza HLS, aparecerá uno o varios archivos con extensión **`.m3u8`**.

---

## Paso 2: Identificar el archivo correcto

Existen dos escenarios comunes.

### Caso 1: Video y audio juntos

El primer archivo `.m3u8` suele contener una lista de los segmentos del video (`.ts`).

Su contenido se verá similar a esto:

```text
#EXTM3U
#EXTINF:3.000,
seg-1.ts
#EXTINF:3.000,
seg-2.ts
#EXTINF:3.000,
seg-3.ts
```

Solo necesitas copiar la URL de ese archivo.

---

### Caso 2: Video y audio separados

Algunas plataformas utilizan un **manifest** principal que contiene varias calidades y pistas de audio.

Por ejemplo:

```text
#EXTM3U
#EXT-X-MEDIA:TYPE=AUDIO,...
#EXT-X-STREAM-INF:BANDWIDTH=748797
QualityLevels(...)/Manifest(video)
```

En este caso encontrarás:

- Un manifiesto principal.
- Un manifiesto para el video.
- Un manifiesto para el audio.

Deberás copiar las URL del video y del audio por separado.

---

## Paso 3: Copiar la URL

Haz clic derecho sobre el archivo `.m3u8` o `Manifest(...)` y selecciona **Copy Link Address** o **Copiar dirección del enlace**.

---

# Descargar con VLC

## Cuando el video incluye el audio

Ejecuta:

```bash
vlc "https://sitio/video.m3u8" \
    --sout file:video.mp4 \
    --play-and-exit
```

VLC descargará todos los segmentos y generará un único archivo `video.mp4`.

---

## Cuando el video y el audio están separados

### Descargar el video

```bash
vlc "https://sitio/video.m3u8" \
    --sout file:video-only.mp4 \
    --play-and-exit
```

### Descargar el audio

```bash
vlc "https://sitio/audio.m3u8" \
    --sout file:audio.mp4 \
    --play-and-exit
```

### Unir ambos archivos

```bash
vlc video-only.mp4 \
    --input-slave=audio.mp4 \
    --sout file:video.mp4 \
    --play-and-exit
```

### Eliminar los archivos temporales

Windows:

```cmd
del video-only.mp4
del audio.mp4
```

Linux/macOS:

```bash
rm video-only.mp4 audio.mp4
```

---

# ¿Qué es un archivo M3U8?

Un archivo **M3U8** no contiene el video, sino una **lista de reproducción** que indica dónde se encuentran todos los fragmentos del contenido.

Ejemplo:

```text
#EXTM3U
#EXTINF:3.000,
seg-1.ts
#EXTINF:3.000,
seg-2.ts
#EXTINF:3.000,
seg-3.ts
```

El reproductor descarga cada segmento de forma secuencial y los reproduce como si fueran un solo video.

---

# ¿Por qué algunas plataformas separan el audio y el video?

Separar ambas pistas permite:

- Ofrecer varias resoluciones (720p, 1080p, 4K, etc.).
- Utilizar distintos códecs de audio.
- Ahorrar ancho de banda.
- Cambiar de calidad durante la reproducción sin interrumpir el video.

El reproductor sincroniza automáticamente ambas pistas mientras reproduce el contenido.

---

# Resumen

1. Abrir **F12 → Network**.
2. Filtrar por **`m3u`**.
3. Recargar la página.
4. Copiar la URL del archivo **`.m3u8`**.
5. Descargar con **VLC**.
6. Si existen pistas separadas, descargar video y audio individualmente y unirlos posteriormente.
