# apm-innova

Web de **APM Innova**, peluquería unisex en Toledo (C/ Río Alberche 25): sitio estático con [Hugo](https://gohugo.io) y el tema [Localcraft](https://github.com/maciejkosiarski/localcraft), publicado en GitHub Pages.

## Qué hay

| Página | Ruta | De dónde salen los datos |
|---|---|---|
| Inicio | `/` | `content/_index.md` (secciones: portada, productos en un párrafo, Instagram y llamada) |
| Servicios | `/servicios/` | `data/es/pricing.yaml` (aún no existe: sin precios, la página invita a pedirlos por teléfono) |
| Dónde estamos | `/donde-estamos/` | `params.business` en `hugo.yaml` (el mapa sale solo si hay `lat` y `lng`) |
| Contacto | `/contacto/` | `params.business`, `params.instagram` y `params.whatsapp` |

Es una web de **presentación para salir en Google**: no vende ni cobra (alcance decidido el 2026-10-23).

SEO: `sitemap.xml` y `robots.txt` los genera Hugo; el JSON-LD `HairSalon` sale en la portada desde `params.business`.

**WhatsApp**: está preparado pero apagado. Al descomentar `params.whatsapp` en `hugo.yaml` aparecen el botón flotante y el WhatsApp en contacto y en dónde estamos, con el mensaje ya escrito (`wa.me`). Se enciende cuando el salón confirme el número.

## Trabajar en local

Hace falta **Hugo extended 0.168.0** (la misma versión que usa el flujo de GitHub Actions).

```bash
hugo server                           # http://localhost:1313/apm-innova/
hugo --gc --minify --panicOnWarning   # lo mismo que compila la CI
```

## Añadir datos

**Publicaciones de Instagram en la portada**: en `data/es/instagram.yaml`, la URL de cada una (`https://www.instagram.com/p/…/`). Se incrustan con el embed oficial y el script de Instagram solo se carga si hay alguna. Si en la foto se reconoce a una clienta, hace falta su permiso.

**Foto de portada**: dejar la foto en `assets/images/` y poner `image: "/images/<archivo>.jpg"` en la sección `hero` de `content/_index.md`. Sin foto, el hueco no se enseña.

**Servicios y precios**: crear `data/es/pricing.yaml`:

```yaml
categories:
  - name: "Mujer"
    services:
      - name: "Corte"
        description: "…"
        price: "00 €"
```

**Horario**: crear `data/es/opening_hours.yaml` con la clave raíz `days:` y los siete días en orden de lunes a domingo (`day`, `open`, `close` o `closed: true`). Sale en «Dónde estamos» y en el JSON-LD.

```yaml
days:
  - day: "Lunes"
    open: "10:00"
    close: "14:00"
  # … hasta el domingo
  - day: "Domingo"
    closed: true
```

**Precios**: escríbelos como se leen («12,50 €»); la coma se convierte en punto para los datos de Google.

**Fuentes**: el tema carga Google Fonts, que recibe la IP de quien visita. Hay que recogerlo en la política de privacidad o alojar las fuentes en el repo antes de publicar.

Nada de esto se inventa: si un dato no lo han dado las titulares, no se pone.

## Publicar

El flujo `.github/workflows/web.yml` compila en cada PR y comprueba que existen las cinco páginas, el sitemap, el robots y el JSON-LD, y que no se ve la palabra «pendiente» en ninguna página.

**Solo publica** desde `main` y si la variable del repositorio `PUBLICAR` vale `true`. Para activarlo:

1. Settings → Pages → Source: **GitHub Actions**.
2. Settings → Secrets and variables → Actions → Variables → `PUBLICAR` = `true`.

No se activa hasta tener el aviso legal y la política de privacidad (faltan los NIF y el domicilio fiscal de las titulares) y el número de WhatsApp confirmado.

## El tema

`themes/localcraft/` es una copia del tema Localcraft (licencia MIT, `themes/localcraft/LICENSE`), commit `9c96051` del 2026-07-23 (`themes/localcraft/ORIGEN-COMMIT`). Se copió en vez de usarlo como submódulo para no depender de un autor único. Cambios hechos a la copia para que compile con Hugo 0.168 y `--panicOnWarning`:

- `site.Data` / `.Site.Data` → `hugo.Data`
- `.Language.LanguageCode` → `.Language.Locale`
- `.Site.Languages` → `hugo.Sites` (selector de idioma)

Lo propio de esta web está fuera del tema: `layouts/` (sobrescribe al tema), `i18n/es.yaml` (el tema no trae español) y `assets/css/custom.css`.
