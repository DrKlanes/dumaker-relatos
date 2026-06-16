# Relatos Aciagos

Micrositio estático de terror editorial-gótico — cuatro relatos ilustrados y
locutados, cada uno con su propia atmósfera de color. Segundo satélite
reconstruido desde el WordPress antiguo. Hermano técnico de
**[Horripilantes Rimas](https://rimas.dumaker.com)** (mismo esqueleto, otra piel).

🔗 Producción: **https://relatos.dumaker.com** (vía subdominio de `dumaker.com`)

---

## Stack

- **HTML + CSS + JS vanilla.** Sin frameworks, sin build step. Principio *poco humo*.
- **Una paleta de color por relato** — el cambio de atmósfera a pantalla completa
  es la firma visual del sitio (Aliento azul cobalto · El Peso crema/granate ·
  Vino Muerto negro/turquesa · El Aljibe negro/coral).
- **Fuentes autoalojadas** (woff2, subconjunto latino) → cero dependencias
  externas, Lighthouse alto. *New Rocker* (títulos) + *Old Standard TT* (cuerpo).
- **YouTube con carga diferida** (*facade*): el reproductor no se inserta hasta el
  clic; los 4 embeds pesan **cero** hasta que el usuario quiere verlos.
- **GitHub Pages** con despliegue *push-to-deploy*.

## Estructura

```
dumaker-relatos/
├── index.html          # toda la página (one-page)
├── styles.css          # estilos + @font-face + paletas por relato
├── app.js              # facade de YouTube + aparición al scroll
├── favicon .ico/.svg/-32.png · apple-touch-icon.png   # iconos (de la calavera)
├── CNAME               # relatos.dumaker.com (preparado, ver abajo)
├── .nojekyll           # sirve los archivos tal cual (sin Jekyll)
├── assets/
│   ├── ilu-aliento · ilu-el-peso · ilu-vino-muerto · ilu-aljibe .jpg   # ilustraciones
│   ├── poster-*.jpg    # carátulas de los 4 vídeos (miniatura de YouTube)
│   ├── calavera.png    # calavera con flecha (logo + favicon + pie)
│   ├── og.jpg          # tarjeta social (montaje de las 4 ilustraciones)
│   └── fonts/          # 4 woff2 (New Rocker, Old Standard TT 400/400i/700)
├── _originals/         # respaldo local de las imágenes sin optimizar (NO se sube)
└── _posters_raw/       # miniaturas crudas de YouTube (NO se sube)
```

> **Imágenes optimizadas:** las ilustraciones se reescalaron a ≤1280 px y se
> recomprimieron (JPEG q82; los PNG opacos se pasaron a JPEG). Los posters son las
> miniaturas oficiales de cada vídeo de YouTube, recortadas a 16:9. Los originales
> quedan en `_originals/` (ignorado por git). El script que lo hizo,
> `_build_assets.py`, también está fuera del repo.

## Probar en local

Cualquier servidor estático sirve:

```bash
python -m http.server 8080   # luego abre http://localhost:8080
```

(Abrir `index.html` con doble clic también funciona, pero un servidor local
reproduce mejor el comportamiento real.)

---

## Despliegue (ya configurado)

GitHub Pages publica **desde la rama `main`** (raíz). Cada `git push` a `main`
vuelve a publicar el sitio automáticamente — es *push-to-deploy* mediante el flujo
de Pages de GitHub (visible en la pestaña **Actions** como *pages-build-deployment*).
No hay paso de compilación.

- URL de validación (activa ya): **https://drklanes.github.io/dumaker-relatos/**
- Dominio final: **https://relatos.dumaker.com** (cuando el DNS apunte; ver abajo)

> **Sobre el `CNAME`:** el archivo `CNAME` está listo en el repo local pero **aún
> no se ha subido a propósito**. En cuanto GitHub detecta un `CNAME`, fija el
> dominio personalizado y *redirige* la URL `github.io` hacia `relatos.dumaker.com`
> — que no resolvería hasta tener el DNS, rompiendo la URL de validación. Por eso
> el orden correcto es: **(1)** validar en `github.io`, **(2)** poner el DNS en
> cdmon, **(3)** activar el `CNAME`. Avísame y lo hago yo en un paso.

### 👉 Lo único que te toca a ti: el DNS en cdmon

Añade **un solo registro** en el panel de cdmon para el dominio `dumaker.com`:

| Tipo  | Nombre / Host | Valor / Destino        | TTL          |
|-------|---------------|------------------------|--------------|
| CNAME | `relatos`     | `drklanes.github.io.`  | 3600 (o auto)|

Notas:
- Es un **subdominio**, por eso es un `CNAME` (no `A`). El destino es el host de
  GitHub Pages de la cuenta (`drklanes.github.io`), **no** el repositorio.
- Pon el nombre como `relatos` (si cdmon te obliga a escribir el FQDN, sería
  `relatos.dumaker.com`). El punto final del destino es opcional según el panel.
- La propagación puede tardar de minutos a unas horas.

Cuando el DNS resuelva, avísame y yo **(3)** activo el `CNAME` (quito su línea de
`.gitignore` y hago `git add CNAME && commit && push`). Después, en
**GitHub → repo → Settings → Pages**:
1. El *Custom domain* se pondrá en `relatos.dumaker.com` (lo fija el `CNAME`).
   Verás un check verde cuando valide el dominio.
2. Marca **Enforce HTTPS** (aparece cuando GitHub emite el certificado, unos
   minutos tras validar el DNS).

---

## Opcional — workflow de GitHub Actions explícito

El sitio ya despliega solo con cada push (flujo gestionado de Pages). Si además
quieres un **workflow de Actions versionado en el repo** (paridad exacta con el
stack de `dumaker.com`), hay que conceder el permiso `workflow` al token de `gh`
—el actual no lo tiene— ejecutando una vez:

```bash
gh auth refresh -h github.com -s workflow
```

Avísame después y subo `.github/workflows/deploy.yml`
(`actions/upload-pages-artifact` + `actions/deploy-pages`) y cambio el *source* de
Pages a *GitHub Actions*. No es necesario para que el sitio funcione.

---

## Créditos

Relatos e ilustraciones por **Dumaker** y **Alabamakiller**. Locución y
sonorización de **Jorge Gállego Loren**. — [dumaker.com](https://dumaker.com)
