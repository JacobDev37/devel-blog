---
layout: post
title:  "Lazy Loading nativo en HTML"
date:   2026-07-29 19:11:04 -0600
categories: updates
---

El atributo `loading` permite implementar **Lazy Loading** en imágenes e `iframe` sin necesidad de escribir JavaScript ni utilizar librerías externas.

Cuando se utiliza `loading="lazy"`, el navegador retrasa la descarga del recurso hasta que está próximo a aparecer en la pantalla, reduciendo el tiempo de carga inicial y el consumo de ancho de banda.

> **Nota:** Este atributo forma parte del estándar HTML y actualmente solo está disponible para las etiquetas `<img>` e `<iframe>`.

---

# Uso básico

Para habilitar la carga diferida, simplemente agrega el atributo `loading="lazy"`.

```html
<img src="imagen.jpg" loading="lazy" alt="Descripción">

<iframe
  src="video.html"
  loading="lazy">
</iframe>
```

No es necesario escribir JavaScript: el navegador se encarga de decidir cuándo comenzar la descarga.

---

# Valores del atributo `loading`

El atributo acepta tres valores.

| Valor | Descripción |
|--------|-------------|
| `lazy` | Retrasa la carga hasta que el elemento esté próximo a mostrarse. |
| `eager` | Descarga el recurso inmediatamente. |
| `auto` | El navegador decide automáticamente cuándo cargarlo. |

Si el atributo no se especifica, el comportamiento predeterminado es:

```html
loading="auto"
```

---

# Ejemplos

## Imagen con Lazy Loading

```html
<img src="foto.jpg"
     loading="lazy"
     alt="Foto">
```

---

## Imagen de carga inmediata

Ideal para elementos visibles al abrir la página, como un banner principal.

```html
<img src="hero.jpg"
     loading="eager"
     alt="Banner principal">
```

---

## Con `<picture>`

Solo la etiqueta `<img>` necesita el atributo `loading`.

```html
<picture>
  <source media="(min-width: 768px)" srcset="grande.jpg">
  <source srcset="pequena.jpg">

  <img
    src="fallback.jpg"
    loading="lazy"
    alt="">
</picture>
```

---

## Con `srcset`

También funciona sin problemas.

```html
<img
  src="imagen-small.jpg"
  srcset="
    imagen-small.jpg 320w,
    imagen-medium.jpg 640w,
    imagen-large.jpg 1024w"
  sizes="100vw"
  loading="lazy"
  alt="">
```

---

## En iframes

```html
<iframe
  src="video.html"
  loading="lazy">
</iframe>
```

También funciona con contenido externo, por ejemplo:

```html
<iframe
  src="https://www.youtube.com/embed/VIDEO_ID"
  loading="lazy">
</iframe>
```

---

# Compatibilidad

La mayoría de los navegadores modernos soportan `loading="lazy"` de forma nativa.

Si un navegador no lo soporta:

- Ignorará el atributo.
- La página seguirá funcionando normalmente.
- Simplemente no obtendrá el beneficio del Lazy Loading.

Este enfoque se conoce como **Mejora Progresiva (Progressive Enhancement)**.

---

# Detectar soporte

Si necesitas saber si el navegador soporta esta característica:

```javascript
const hasNativeLazyLoad =
  "loading" in HTMLImageElement.prototype;
```

También puede comprobarse para `iframe`:

```javascript
const hasIframeLazyLoad =
  "loading" in HTMLIFrameElement.prototype;
```

---

# Compatibilidad con navegadores antiguos

Si necesitas dar soporte a navegadores que no implementan Lazy Loading de forma nativa, puedes:

1. Detectar si existe soporte.
2. Utilizar una librería como **vanilla-lazyload** únicamente cuando sea necesario.

De esta forma los navegadores modernos utilizan la implementación nativa y solo los antiguos descargan la dependencia adicional.

---

# ¿Debo usar `loading="lazy"` en todas las imágenes?

**No.**

Las imágenes visibles inmediatamente al cargar la página **no deberían utilizar Lazy Loading**.

Por ejemplo:

```html
<!-- Correcto -->
<img src="hero.jpg"
     loading="eager">

<!-- Correcto -->
<img src="producto.jpg"
     loading="lazy">
```

Una buena regla es:

- ✅ Usar `loading="lazy"` para contenido que aparece después de hacer scroll.
- ❌ Evitarlo en logotipos, banners, imágenes del encabezado y cualquier elemento visible desde el inicio.

---

# ¿Por qué algunas imágenes parecen descargarse de todos modos?

Es normal observar solicitudes de red incluso cuando se utiliza Lazy Loading.

Esto ocurre por dos motivos.

## 1. El navegador obtiene metadatos

Para evitar cambios en el diseño de la página (Layout Shift), algunos navegadores descargan una pequeña parte del archivo para conocer:

- Dimensiones
- Metadatos
- Información necesaria para reservar el espacio correspondiente

No significa que la imagen completa haya sido descargada.

---

## 2. La descarga comienza antes de llegar al viewport

El navegador no espera a que la imagen sea completamente visible.

En realidad comienza la descarga **unos cientos o miles de píxeles antes**, de forma que cuando el usuario llegue a ella ya esté disponible.

La distancia depende de varios factores, entre ellos:

- Velocidad de la conexión.
- Rendimiento del dispositivo.
- Estrategias internas del navegador.

En conexiones lentas suele iniciar la descarga con mayor anticipación para evitar que el usuario vea espacios vacíos mientras espera.

---

# Buenas prácticas

- Usa `loading="lazy"` únicamente en contenido fuera del primer viewport.
- Utiliza `loading="eager"` para imágenes críticas.
- Especifica siempre `width` y `height` para reducir el **Cumulative Layout Shift (CLS)**.
- Combina Lazy Loading con formatos modernos como **WebP** o **AVIF** para obtener un mejor rendimiento.

---

# Resumen

- `loading="lazy"` implementa Lazy Loading sin JavaScript.
- Solo funciona en `<img>` e `<iframe>`.
- Reduce el tiempo de carga inicial y el consumo de ancho de banda.
- Los navegadores que no lo soportan simplemente ignoran el atributo.
- No debe utilizarse en imágenes visibles desde el inicio de la página.
- Es una mejora sencilla que puede aportar beneficios importantes en rendimiento y experiencia de usuario.
