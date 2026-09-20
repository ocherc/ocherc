# SEO Handoff — sgagents.vercel.app

> **Para la sesión de Claude Code que ejecute esto:** este documento es el brief completo.
> Contiene hallazgos ya verificados (no los re-audites desde cero) y el código listo para aplicar.
> Auditoría original: 20/09/2026, con la skill `seo-audit` v2.0.1.

---

## 0. Antes de empezar — lo que necesitás del usuario

La auditoría se hizo **de forma remota** (solo HTTP), sin acceso al código fuente.
Arrancá preguntando:

1. **¿Dónde está el repo del sitio?** (ruta local o repo de GitHub). Es un único archivo HTML estático desplegado en Vercel.
2. **¿Ya compró un dominio propio?** Si sí, la migración sube a prioridad absoluta y todo lo demás se aplica directamente sobre el dominio nuevo.
3. **¿Tiene Google Search Console configurado?** (al 20/09/2026 no había meta tag de verificación en el HTML).

No apliques cambios a ciegas: **leé el HTML actual antes de editar**, porque puede haber cambiado desde la auditoría.

---

## 1. Contexto del negocio

| Dato | Valor |
|---|---|
| Sitio | https://sgagents.vercel.app/ |
| Marca | SG Agents |
| Dueño | Santiago Grazziani (consultoría unipersonal) |
| Rubro | Automatización de procesos con IA y agentes autónomos (B2B) |
| Stack que vende | n8n, LLMs, WhatsApp API, Google Sheets, Telegram, Shopify, Stripe/MercadoPago |
| Ubicación | Rosario, Santa Fe, Argentina (inferido del prefijo +54 9 341) |
| Idioma | Español (es-AR, voseo) |
| Contacto | WhatsApp +54 9 341 559 8979 · santiago.grazziani@gmail.com |
| CTA principal | WhatsApp directo (no hay formulario) |
| Precios públicos | U$D300 (Agente Conversacional) · U$D1.000 (Estructura Agéntica Pro) · A medida (Infraestructura Custom) |
| Único caso de estudio | "Pilatin" — estudio de pilates, gestión de turnos por WhatsApp con n8n + Google Sheets |

**Objetivo SEO:** captar leads comerciales de PyMEs y emprendedores que buscan automatizar operaciones.

---

## 2. Estado técnico verificado (20/09/2026)

**Arquitectura:** un único archivo HTML estático. 36.917 bytes crudos, 7,4 KB con Brotli.
CSS 100% inline en un `<style>`. **Cero tags `<script>`.** Cero tags `<img>` (todos los íconos son emojis).
Toda la navegación son anclas internas (`#servicios`, `#precios`, `#portfolio`, `#beneficios`, `#contacto`).
551 palabras de contenido visible.

### Lo que YA está bien — no lo rompas

- HTTPS + `Strict-Transport-Security: max-age=63072000; includeSubDomains; preload`
- `http://` → `https://` con 308
- Brotli activo, TTFB 208 ms, `X-Vercel-Cache: HIT`
- `<html lang="es">` correcto
- `<meta name="viewport" content="width=device-width, initial-scale=1.0">` correcto
- 404 devuelve HTTP 404 real (sin soft 404)
- Sin `X-Robots-Tag` restrictivo → el sitio es indexable
- Google Fonts ya usa `&display=swap`
- **El sitio es muy rápido. Cualquier cambio que agregue JS o imágenes pesadas es una regresión.**

### Hallazgos confirmados (con evidencia)

| # | Hallazgo | Evidencia | Impacto |
|---|---|---|---|
| 1 | Dominio `.vercel.app`, no propio | URL del sitio | Alto |
| 2 | Sitio de una sola URL | 7 links internos, todos anclas | Alto |
| 3 | Cero structured data | 0 bloques `application/ld+json`, 0 microdata, **0 tags `<script>`** → concluyente, no es falso negativo | Alto |
| 4 | `og:image` roto | `https://sgagents.vercel.app/tu-imagen-preview.jpg` → **HTTP 404** (placeholder del template) | Alto |
| 5 | Sin canonical | 0 `<link rel="canonical">` | Medio-Alto |
| 6 | Sin robots.txt | `/robots.txt` → HTTP 404 | Medio |
| 7 | Sin sitemap.xml | `/sitemap.xml` y `/sitemap_index.xml` → HTTP 404 | Medio |
| 8 | Sin favicon | `/favicon.ico` → HTTP 404, y sin `<link rel="icon">` | Bajo-Medio |
| 9 | Title sin keyword | `SG Agents - Automatización Inteligente` (38 chars, arranca con marca sin volumen) | Medio-Alto |
| 10 | H1 sin keyword | `Tu negocio en automático` | Medio-Alto |
| 11 | Jerarquía de headings rota | Sección "Tecnología de Punta": h2 → **h4** (saltea h3). Footer usa h4 para columnas de nav | Bajo-Medio |
| 12 | `meta keywords` obsoleto | Presente; Google lo ignora desde 2009 | Bajo |
| 13 | Faltan meta sociales | Sin `twitter:image`, `og:locale`, `og:site_name` | Medio |
| 14 | Sin preconnect a Google Fonts | Único recurso render-blocking | Bajo-Medio |
| 15 | Claims sin evidencia | "50+ Proyectos", "80% Ahorro", "60% menos costos", "95% menos trabajo manual" — sin fuente, testimonio ni logo | Medio-Alto |
| 16 | Santiago no aparece en la página | Solo en `meta keywords` (ignorado) y en el texto del link de WhatsApp | Medio-Alto |
| 17 | Sin señales locales | "Rosario"/"Argentina" no aparecen en ningún lado del contenido | Medio-Alto |
| 18 | Sin privacidad/términos/formulario | 0 tags `<form>`; único canal es WhatsApp + `mailto:` | Medio |
| 19 | Typo en contenido | Sección "Consultoría de Flujos": "Asesoramiento **strategic**" (quedó en inglés) | Bajo |
| 20 | Cero visibilidad orgánica | Búsqueda de `"sgagents.vercel.app"` y de la marca → sin resultados | — |

### No verificado — pendiente

- **Core Web Vitals reales:** la API de PageSpeed Insights devolvió HTTP 429 (cuota diaria agotada, es compartida y sin API key). Corré https://pagespeed.web.dev/ manualmente.
- **Estado de indexación en Google:** requiere Search Console.

---

## 3. FASE 1 — Quick wins (~2-3 h, todo en el HTML actual)

> Aplicá estos cambios sobre el `<head>` del archivo HTML existente.
> **Leé el head actual primero** y reemplazá tag por tag — no pises el archivo entero.

### 3.1 Reemplazar el `<head>` (meta tags)

Cambios respecto al actual: title reescrito, canonical nuevo, `meta keywords` eliminado,
`og:image` apuntando a un archivo que sí existe, `twitter:image` agregado,
`og:locale` + `og:site_name` agregados, preconnect agregado, favicon agregado.

```html
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Automatización con Agentes de IA para Empresas | SG Agents</title>

<meta name="description" content="Automatizá procesos operativos con agentes de IA y n8n. Eliminá tareas repetitivas y escalá sin contratar. Consulta inicial sin cargo.">
<meta name="robots" content="index, follow">
<meta name="author" content="Santiago Grazziani">
<link rel="canonical" href="https://sgagents.vercel.app/">

<link rel="icon" href="/favicon.ico" sizes="32x32">
<link rel="icon" href="/icon.svg" type="image/svg+xml">
<link rel="apple-touch-icon" href="/apple-touch-icon.png">

<meta property="og:type" content="website">
<meta property="og:site_name" content="SG Agents">
<meta property="og:locale" content="es_AR">
<meta property="og:url" content="https://sgagents.vercel.app/">
<meta property="og:title" content="SG Agents - Tu negocio en automático con IA">
<meta property="og:description" content="Eliminá tareas repetitivas, reducí errores y escalá tu operación con flujos e infraestructura inteligente.">
<meta property="og:image" content="https://sgagents.vercel.app/og-image.jpg">
<meta property="og:image:width" content="1200">
<meta property="og:image:height" content="630">
<meta property="og:image:alt" content="SG Agents - Automatización con agentes de IA">

<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:url" content="https://sgagents.vercel.app/">
<meta name="twitter:title" content="SG Agents - Tu negocio en automático con IA">
<meta name="twitter:description" content="Eliminá tareas repetitivas, reducí errores y escalá tu operación con flujos e infraestructura inteligente.">
<meta name="twitter:image" content="https://sgagents.vercel.app/og-image.jpg">

<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;700&display=swap" rel="stylesheet">
```

**Notas:**

- El `title` nuevo tiene 58 caracteres — entra completo en el SERP.
- La `description` nueva tiene 137 caracteres e incorpora un CTA.
- Los tags de Twitter pasaron de `property=` a `name=` (es lo correcto para Twitter/X; el anterior usaba `property`).
- **`/og-image.jpg` hay que crearlo** (1200×630 px) — ver tarea 3.4.

### 3.2 Agregar JSON-LD

Pegar **al final del `<head>`**. Datos extraídos del contenido real del sitio.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@graph": [
    {
      "@type": "ProfessionalService",
      "@id": "https://sgagents.vercel.app/#organization",
      "name": "SG Agents",
      "url": "https://sgagents.vercel.app/",
      "description": "Automatización de procesos operativos e infraestructura con inteligencia artificial y agentes autónomos.",
      "image": "https://sgagents.vercel.app/og-image.jpg",
      "email": "santiago.grazziani@gmail.com",
      "telephone": "+5493415598979",
      "priceRange": "$$",
      "founder": { "@id": "https://sgagents.vercel.app/#santiago" },
      "areaServed": [
        { "@type": "City", "name": "Rosario" },
        { "@type": "Country", "name": "Argentina" }
      ],
      "address": {
        "@type": "PostalAddress",
        "addressLocality": "Rosario",
        "addressRegion": "Santa Fe",
        "addressCountry": "AR"
      },
      "sameAs": [
        "COMPLETAR: URL de LinkedIn",
        "COMPLETAR: URL de Instagram o GitHub"
      ]
    },
    {
      "@type": "Person",
      "@id": "https://sgagents.vercel.app/#santiago",
      "name": "Santiago Grazziani",
      "jobTitle": "Consultor en Automatización e IA",
      "email": "santiago.grazziani@gmail.com",
      "worksFor": { "@id": "https://sgagents.vercel.app/#organization" },
      "knowsAbout": ["n8n", "Agentes de IA", "Automatización de procesos", "WhatsApp Business API", "Integración de APIs"],
      "sameAs": ["COMPLETAR: URL de LinkedIn"]
    },
    {
      "@type": "WebSite",
      "@id": "https://sgagents.vercel.app/#website",
      "url": "https://sgagents.vercel.app/",
      "name": "SG Agents",
      "inLanguage": "es-AR",
      "publisher": { "@id": "https://sgagents.vercel.app/#organization" }
    },
    {
      "@type": "Service",
      "name": "Agentes de Conversación IA",
      "description": "Asistentes inteligentes para WhatsApp, web o Telegram que razonan y responden al instante. Reservas, soporte y ventas.",
      "serviceType": "Desarrollo de agentes conversacionales",
      "provider": { "@id": "https://sgagents.vercel.app/#organization" },
      "areaServed": { "@type": "Country", "name": "Argentina" }
    },
    {
      "@type": "Service",
      "name": "Automatización de Procesos",
      "description": "Integración de herramientas (CRM, email, Sheets) sin código mediante flujos avanzados. Leads, pagos y notificaciones.",
      "serviceType": "Automatización de procesos de negocio",
      "provider": { "@id": "https://sgagents.vercel.app/#organization" },
      "areaServed": { "@type": "Country", "name": "Argentina" }
    },
    {
      "@type": "Service",
      "name": "Dashboard y Reportes",
      "description": "Visualización de datos en tiempo real con análisis personalizados. Métricas, seguimiento y decisiones.",
      "serviceType": "Business intelligence",
      "provider": { "@id": "https://sgagents.vercel.app/#organization" },
      "areaServed": { "@type": "Country", "name": "Argentina" }
    },
    {
      "@type": "Service",
      "name": "Integraciones Avanzadas",
      "description": "Conexión de APIs y servicios complejos: Instagram, Shopify, Stripe y más. E-commerce, redes y pagos.",
      "serviceType": "Integración de sistemas",
      "provider": { "@id": "https://sgagents.vercel.app/#organization" },
      "areaServed": { "@type": "Country", "name": "Argentina" }
    },
    {
      "@type": "Service",
      "name": "Infraestructura IA Custom",
      "description": "Entrenamiento de modelos de IA y agentes autónomos con datos operativos específicos. Análisis, predicción y optimización.",
      "serviceType": "Desarrollo de infraestructura de IA",
      "provider": { "@id": "https://sgagents.vercel.app/#organization" },
      "areaServed": { "@type": "Country", "name": "Argentina" }
    },
    {
      "@type": "Service",
      "name": "Consultoría de Flujos",
      "description": "Asesoramiento estratégico para identificar qué automatizar primero en la operación. Auditoría, planificación y roadmap.",
      "serviceType": "Consultoría en automatización",
      "provider": { "@id": "https://sgagents.vercel.app/#organization" },
      "areaServed": { "@type": "Country", "name": "Argentina" }
    },
    {
      "@type": "OfferCatalog",
      "name": "Planes SG Agents",
      "itemListElement": [
        {
          "@type": "Offer",
          "name": "Agente Conversacional",
          "description": "Asistente de IA para canales de mensajería. Integración oficial con WhatsApp, hasta 1000 flujos de conversación al mes, soporte y optimización por 2 semanas.",
          "price": "300",
          "priceCurrency": "USD",
          "availability": "https://schema.org/InStock",
          "seller": { "@id": "https://sgagents.vercel.app/#organization" }
        },
        {
          "@type": "Offer",
          "name": "Estructura Agéntica Pro",
          "description": "Automatización completa de punta a punta con múltiples herramientas interconectadas, agentes con lógica personalizada, bases de datos estructuradas y reportes automáticos. Soporte técnico por 1 mes.",
          "price": "1000",
          "priceCurrency": "USD",
          "availability": "https://schema.org/InStock",
          "seller": { "@id": "https://sgagents.vercel.app/#organization" }
        },
        {
          "@type": "Offer",
          "name": "Infraestructura Custom",
          "description": "Proyecto operativo a medida: análisis profundo del ecosistema, arquitectura personalizada, integraciones ilimitadas, agentes multitarea y soporte continuo.",
          "availability": "https://schema.org/InStock",
          "seller": { "@id": "https://sgagents.vercel.app/#organization" }
        }
      ]
    }
  ]
}
</script>
```

**Importante:** hay dos campos `COMPLETAR` (`sameAs`). Preguntale al usuario por sus URLs de
LinkedIn / Instagram / GitHub. Si no las tiene, **borrá el array `sameAs`** — no dejes placeholders
en producción.

### 3.3 Cambiar el H1

Actual: `Tu negocio en automático` → sin ninguna keyword.

```html
<h1>Automatizá tu negocio con agentes de IA</h1>
```

Mantené el copy original como subtítulo si aporta (`<p>` debajo del H1). Es un solo H1 por página.

### 3.4 Crear los archivos estáticos faltantes

Todos van en la raíz de lo que Vercel sirve como público.

**`robots.txt`**

```
User-agent: *
Allow: /

Sitemap: https://sgagents.vercel.app/sitemap.xml
```

**`sitemap.xml`**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://sgagents.vercel.app/</loc>
    <lastmod>2026-09-20</lastmod>
    <changefreq>monthly</changefreq>
    <priority>1.0</priority>
  </url>
</urlset>
```

> Actualizá `<lastmod>` a la fecha real del deploy, y sumá una `<url>` por cada página nueva de la Fase 3.

**Imágenes a crear** (no las puede generar la sesión de código — pedíselas al usuario):

- `/og-image.jpg` — 1200×630 px, con logo + propuesta de valor legible en miniatura
- `/favicon.ico` — 32×32
- `/icon.svg`
- `/apple-touch-icon.png` — 180×180

### 3.5 Arreglar el typo

En la sección "Consultoría de Flujos": `Asesoramiento strategic` → `Asesoramiento estratégico`

### 3.6 Search Console

Guiá al usuario para dar de alta https://search.google.com/search-console, verificar el dominio
(el método de meta tag HTML es el más simple acá) y enviar el sitemap.
**Sin esto no hay forma de medir nada de lo que sigue.**

---

## 4. FASE 2 — Estructura y confianza (2 semanas)

### 4.1 Migrar a dominio propio ← hacelo ANTES de crear contenido

Comprar `sgagents.com` / `.ai` / `.com.ar`, agregarlo en Vercel (Settings → Domains),
configurar 301 desde `.vercel.app`, y actualizar **todas** las URLs absolutas:
`canonical`, `og:url`, `twitter:url`, todos los `@id` del JSON-LD, `robots.txt` y `sitemap.xml`.
Después, agregar la propiedad nueva en Search Console y usar la herramienta de cambio de dirección.

### 4.2 Arreglar jerarquía de headings

- Sección "Tecnología de Punta": los `<h4>` (n8n, Modelos LLM, WhatsApp API, Google Sheets, APIs Integradas, Telegram Bot, Shopify, Stripe/MP) → pasar a `<h3>`.
- Footer: los `<h4>` de columna (Soluciones, Compañía, Contacto) → convertir en `<div>`/`<p>` con estilo. Son navegación, no estructura de contenido.

### 4.3 Página `/sobre-mi/`

Foto real, recorrido profesional, certificaciones, links a LinkedIn/GitHub.
Marcar con el `Person` schema ya definido arriba y enlazarla desde el header o footer.
**Para una consultoría unipersonal, esto es la señal de E-E-A-T más importante que falta.**

### 4.4 Respaldar los claims numéricos

Cada número del sitio ("50+ Proyectos", "80% Ahorro de tiempo", "60% menos costos",
"95% Menos trabajo manual", "0 Turnos duplicados") necesita respaldo o hay que bajarlo.
Para Pilatin: nombre y cargo de quien lo dirige, cita textual, link a su perfil, captura del flujo de n8n.

### 4.5 Señales locales

Mencionar Rosario / Santa Fe / Argentina en el contenido visible.
El `LocalBusiness`/`ProfessionalService` schema con `address` y `areaServed` ya está en el JSON-LD de arriba.
Crear perfil de Google Business Profile.

### 4.6 Páginas legales y formulario

`/privacidad/`, `/terminos/`, y un formulario de contacto real
(hoy el único canal es WhatsApp + `mailto:`; hay 0 tags `<form>`).

---

## 5. FASE 3 — Contenido (mes 1-3)

### 5.1 Desarmar la landing en páginas reales

Es el techo estructural más grande: 6 servicios distintos comparten una sola URL.

```
/                                        → home (keyword principal)
/servicios/agentes-ia-whatsapp/
/servicios/automatizacion-procesos-n8n/
/servicios/integraciones-api/
/servicios/dashboards-reportes/
/servicios/infraestructura-ia-custom/
/servicios/consultoria-automatizacion/
/casos/pilatin-pilates-whatsapp/
/precios/
/sobre-mi/
/blog/
```

Cada página de servicio: 800-1.200 palabras. Estructura: problema → solución → cómo funciona → caso → CTA.
Un `Service` schema por página (mover el correspondiente del `@graph` de la home).
Agregar `BreadcrumbList` schema. Actualizar el sitemap con cada URL nueva.

### 5.2 Blog — intención informacional

Ideas iniciales: "cómo automatizar WhatsApp con n8n", "agente de IA vs chatbot: diferencias reales",
"cuánto cuesta automatizar un negocio en Argentina", "n8n vs Make vs Zapier".

### 5.3 Backlinks

Directorios de la comunidad n8n, Product Hunt, casos en LinkedIn, podcasts locales de negocios,
directorios de agencias de IA en español.

---

## 6. Criterios de aceptación — Fase 1

Verificá cada uno antes de dar la fase por cerrada:

- [ ] `curl -sI https://sgagents.vercel.app/og-image.jpg` → **HTTP 200** (hoy el og:image da 404)
- [ ] `curl -sI https://sgagents.vercel.app/robots.txt` → **HTTP 200**
- [ ] `curl -sI https://sgagents.vercel.app/sitemap.xml` → **HTTP 200**
- [ ] `curl -sI https://sgagents.vercel.app/favicon.ico` → **HTTP 200**
- [ ] `curl -s https://sgagents.vercel.app/ | grep -c 'application/ld+json'` → **1 o más**
- [ ] JSON-LD sin errores en https://search.google.com/test/rich-results
- [ ] JSON-LD sin ningún texto `COMPLETAR` en producción
- [ ] `<link rel="canonical">` presente y apuntando a la URL correcta
- [ ] `<meta name="keywords">` eliminado
- [ ] Un solo `<h1>`, y contiene keyword
- [ ] `title` entre 50 y 60 caracteres
- [ ] Preview correcto en https://www.opengraph.xyz/ (pegá la URL del sitio)
- [ ] Sin la palabra "strategic" en el HTML
- [ ] Search Console verificado y sitemap enviado
- [ ] **No hay regresión de performance:** `curl -so /dev/null -w "%{time_starttransfer} %{size_download}\n" -H "Accept-Encoding: br" https://sgagents.vercel.app/` debe seguir cerca de **0,2 s / ~7,5 KB**

---

## 7. Reglas de trabajo

- **No rompas lo que funciona.** El sitio carga en 7,4 KB sin JavaScript. No metas frameworks, librerías ni imágenes pesadas. Si una mejora de SEO cuesta performance, consultá antes.
- **Leé el HTML actual antes de editar.** Puede haber cambiado desde el 20/09/2026.
- **No inventes datos en el schema.** Si falta una URL de perfil o un dato de contacto, preguntá o borrá el campo. Nunca dejes placeholders en producción.
- **No infles los claims.** Varios números del sitio no tienen respaldo — el trabajo es respaldarlos o bajarlos, no agregar más.
- **Verificá con las herramientas correctas.** `curl` y `web_fetch` no detectan schema inyectado por JS. Acá da igual (el sitio tiene 0 tags `<script>`), pero si en el futuro se migra a un framework, usá el Rich Results Test.
- **Ordená por fase.** Fase 1 completa y verificada antes de arrancar la 2.
