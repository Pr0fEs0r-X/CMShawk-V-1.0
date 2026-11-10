# CMShawk V 1.0

> CMS & attack surface detector — GUI + CLI en Python. Integra Wappalyzer (1270+ tecnologías).

![Python](https://img.shields.io/badge/Python-3.8+-3776AB?style=flat-square&logo=python&logoColor=white)
![License](https://img.shields.io/badge/Licencia-MIT-22C55E?style=flat-square)
![Version](https://img.shields.io/badge/Versión-1.0-10B981?style=flat-square)
![Platform](https://img.shields.io/badge/Plataforma-Linux%20%7C%20macOS%20%7C%20Windows-6C757D?style=flat-square)

![GUI](https://img.shields.io/badge/GUI-tkinter-FF6F00?style=flat-square)
![CLI](https://img.shields.io/badge/CLI-colorama%20%2B%20pyfiglet-8B5CF6?style=flat-square)
![Wappalyzer](https://img.shields.io/badge/Wappalyzer-1270%2B%20techs-10B981?style=flat-square)
![CMS](https://img.shields.io/badge/CMS-20%2B%20detectables-8B5CF6?style=flat-square)

![Dependencies](https://img.shields.io/badge/Dependencias-3-22C55E?style=flat-square)
![Status](https://img.shields.io/badge/Status-Active-10B981?style=flat-square)
![Author](https://img.shields.io/badge/Author-Pr%40fEsOr%20X-f59e0b?style=flat-square)
![Year](https://img.shields.io/badge/©-2026-6C757D?style=flat-square)

**Programado por:** Rodolfo Hernandez Baz aka "Pr@fEsOr X" · © 2026

---

## Scripts

| Script | Descripción | Dependencias |
|--------|-------------|--------------|
| `cmshawk.py` | GUI con tkinter — title bar custom, botones macOS-style, About dialog, streaming progress | Solo Python 3.8+ (tkinter incluido) |
| `cmshawk_cli.py` | CLI con colores (colorama) + banner ASCII (pyfiglet) + menú interactivo numerado | `pip install colorama pyfiglet python-Wappalyzer` |

---

## Instalación

### GUI (sin dependencias externas)

```bash
# Debian/Ubuntu si falta tkinter:
sudo apt install python3-tk

# Ejecutar:
python3 cmshawk.py
```

### CLI (con colores + Wappalyzer)

```bash
pip install -r requirements.txt
python3 cmshawk_cli.py
```

O instalar manualmente:

```bash
pip install colorama pyfiglet python-Wappalyzer
python3 cmshawk_cli.py
```

> **Nota:** `python-Wappalyzer` es opcional. Si no está instalado, el script funciona normal con los 20 CMS internos y omite la capa de Wappalyzer.

---

## Detección de CMS (dos capas)

### Capa 1: Fingerprints internos (20 CMS)

WordPress, Drupal, Joomla, PrestaShop, Magento, Shopify, Wix, Squarespace, Webflow, Ghost, Blogger, Hugo, Jekyll, Gatsby, Next.js, Nuxt, Django, Laravel, Ruby on Rails, Express.

Cada CMS tiene:
- Meta generator regex
- Headers específicos
- Cookies características
- Fingerprints HTML (rutas, scripts, body classes)
- Probes de archivos característicos (`/readme.html`, `/core/CHANGELOG.txt`, etc.)

### Capa 2: Wappalyzer (1270+ tecnologías)

Si `python-Wappalyzer` está instalado, después de los fingerprints internos se corre Wappalyzer que detecta tecnologías adicionales usando su base de datos oficial de 1270+ tecnologías:

- **CMS adicionales:** TYPO3, Contao, Craft CMS, Concrete5, ProcessWire, October CMS, Bolt, SilverStripe
- **JS frameworks:** React, Vue, Angular, Svelte, SolidJS, Alpine.js, Qwik
- **Web servers:** nginx, Apache, Caddy, LiteSpeed, IIS
- **CDN:** Cloudflare, CloudFront, Fastly, Akamai, BunnyCDN
- **Analytics:** Google Analytics, Plausible, Matomo, Fathom
- **E-commerce:** WooCommerce, BigCommerce, Shopify, Magento
- **JS libraries:** jQuery, Lodash, Moment, Three.js
- **Advertising:** Google AdSense, DoubleClick
- **Tag managers:** Google Tag Manager, Adobe DTM
- **Y cientos más**

Ejemplo de output:
```
Tecnologias (Wappalyzer):
  3 tecnologias detectadas:
    Alpine.js  [JavaScript frameworks]
    Hugo  [Static site generator]
    Netlify  [PaaS, CDN]
```

---

## Características

### Detección
- 20+ CMS/frameworks con fingerprints internos
- 1270+ tecnologías con Wappalyzer
- Versión exacta vía `<meta name="generator">`
- Probe de archivos característicos cuando la versión está oculta
- Scoring de confianza 0–1 con evidencia categorizada

### Identificación de CDN/WAF/bloqueador
Cloudflare (CDN/challenge Turnstile 2023+/WAF block), Akamai, AWS CloudFront + WAF, Sucuri, Imperva/Incapsula, ModSecurity, Wordfence, nginx, Apache (con versión cuando es visible).

### Seguridad WordPress (10 checks)
- `xmlrpc.php` — activo / inactivo / protegido
- `/wp-json/` REST API — habilitado / deshabilitado
- `/readme.html` — versión expuesta
- `/wp-includes/`, `/wp-content/plugins/`, `/wp-content/uploads/` — directory listing
- Enumeración de usuarios vía REST API + `/?author=N` redirects
- `/wp-config.php.{bak,save,old,...}` — backups expuestos (CRÍTICO)
- `/wp-content/debug.log` — log expuesto

### REST API Enumeration
Si `/wp-json/` está habilitado: extrae namespaces (= enumerar plugins), últimos posts, páginas, y URLs de media.

### Headers de seguridad
Check de HSTS, CSP, X-Frame-Options, X-Content-Type-Options, Referrer-Policy, Permissions-Policy.

### HTTP/2 + HTTP/3 detection
Via header `Alt-Svc` + `curl --http2/--http3` (si curl está instalado).

### HTTP methods permitidos
OPTIONS request → parsea header `Allow` → marca PUT/DELETE/TRACE/CONNECT/PATCH como peligrosos.

### /server-status + /server-info (Apache)
Check de mod_status y mod_info expuestos.

### Admin/login paths
Prueba 10 paths: `/wp-admin/`, `/wp-login.php`, `/admin/`, `/administrator/`, `/login`, `/user/login`, `/manage/`, `/dashboard/`, `/control/`, `/cms/`.

### .htaccess y .git/ expuestos
- `/.htaccess` — si retorna 200 filtra config Apache
- `/.git/HEAD`, `/.git/config`, etc. — si retornan 200, repo descargable con git-dumper

### CVE lookup para plugins
Para cada plugin detectado, consulta `api.wordpress.org` para obtener la última versión, compara con la detectada, marca DESACTUALIZADO si es menor, y genera link a `wpscan.com/plugins/{slug}`.

### Control de delay + Cookie override
- **Delay (0-30s)** entre requests — evade rate-limiting y detección de bursts
- **Cookie override** — pega `cf_clearance=...` obtenida del navegador real para bypassar Cloudflare manualmente

### Export
- **HTML report** — reporte autocontenible con CSS dark theme
- **JSON** — para pipelines y automatización
- **SQLite history** — cada scan se guarda en `~/.cms_detector_history.db`

### Streaming progress
Cada probe se muestra en tiempo real mientras corre el scan (en GUI y CLI).

---

## Uso

### GUI

```bash
python3 cmshawk.py
```

- **Title bar custom** con botones macOS-style: 🔴 cerrar, 🟡 minimizar, 🟢 maximizar, 🔵 **?** (About)
- Ventana arrastrable desde el title bar
- Click en **?** abre el dialog About
- Streaming progress: cada probe se muestra en tiempo real

### CLI interactivo (menú numerado)

```bash
python3 cmshawk_cli.py
```

```
   ________  ________ __                   __
  / ____/  |/  / ___// /_  ____ __      __/ /__
 / /   / /|_/ /\__ \/ __ \/ __ `/ | /| / / //_/
/ /___/  /  /___/ / / / / /_/ /| |/ |/ / ,<
\____/_/  /_//____/_/ /_/\__,_/ |__/|__/_/|_|

  v1.0    BY Pr@fEsOr X
  ==================================================

  Menu principal:

  1) Escanear una URL
  2) Escanear batch (archivo de URLs)
  3) Salir

  Seleccione opcion [1-3]:
```

### CLI con argumentos

```bash
python3 cmshawk_cli.py -u techcrunch.com
python3 cmshawk_cli.py -u techcrunch.com --json
python3 cmshawk_cli.py -u techcrunch.com --html reporte.html
python3 cmshawk_cli.py -u URL -d 2 -c 'cf_clearance=abc123'
python3 cmshawk_cli.py -b urls.txt
python3 cmshawk_cli.py -b urls.txt --json > resultados.json
```

**Opciones:**
- `-u, --url URL` — URL a escanear
- `-b, --batch FILE` — archivo con URLs (batch)
- `-d, --delay N` — delay entre requests en segundos
- `-c, --cookie STR` — cookie header override
- `--json` — output JSON a stdout
- `--html FILE` — guardar reporte HTML
- `--no-banner` — omitir el banner ASCII

---

## Limitaciones

### Cloudflare Turnstile (2023+)
Sitios como `learningforward.org` o `medium.com` usan Cloudflare con Turnstile. La app detecta el bloqueo pero no puede bypassar el challenge desde Python — requiere navegador real + IP residencial. Solución: usa el campo **Cookie** para pegar `cf_clearance` obtenida de tu navegador.

---

## Uso ético

Detección pasiva: solo GET requests, sin autenticación, sin payloads de ataque. Úsala solo en sitios públicos donde tienes permiso o para auditoría legítima.

---

## Licencia

MIT © 2026 Rodolfo Hernandez Baz aka "Pr@fEsOr X"
