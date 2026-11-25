# TASKS.md

> Estado actual del trabajo. Los commits de Git guardan el detalle de las fases completadas.
> Plan v3. El crítico dio `VERDICT: OK` en la ronda 3. El usuario ordenó ejecutarlo con `/taskflow run F01`.

## 1. Objetivo

- Propósito: web de presentación del Taller Autovida (Orihuela, Alicante). Sustituye a la web WordPress actual y usa el mismo dominio.
- Público: conductores de la zona que llegan desde el móvil y quieren llamar, escribir por WhatsApp o saber cómo llegar.
- Segundo objetivo: pieza de portfolio público de un desarrollador junior. Alojamiento propio.
- El dueño del taller casi no usa el correo. El teléfono y WhatsApp son los canales principales.

## 2. Estado actual

- Qué funciona: solo `README.md` y `.gitignore` en `main`. No hay código.
- Pendiente: todo el desarrollo (fases F01 a F11).
- Rama Git: `main`. Remoto público en GitHub.
- Cambios locales existentes: ninguno. El plan y `design/logo/` ya están en Git.
- Capacidades de prueba necesarias:
  - Node y npm (local: Node 26.12, npm 11.20). Docker 29 local.
  - Navegadores de Playwright: sin comprobar. F01 debe instalarlos.
  - La máquina local es x86_64. La imagen ARM64 solo se prueba en la Raspberry Pi (F08).
  - Accesos humanos: Cloudflare (túnel), Namecheap (DNS), Raspberry Pi. Se necesitan en F08, F10 y F11.
  - La integración continua de GitHub queda sin verificar hasta el primer push que el usuario autorice.
- Límite de familias de modelos: el orquestador es Claude. Escritores, pruebas y crítico son modelos de OpenCode (una sola familia). El revisor es un subagente de Claude.

### Datos pendientes del taller (no se publican hasta tenerlos)

- Nombre completo, NIF y domicilio del titular (aviso legal). Preguntar a la gestoría qué domicilio debe figurar.
- Enlace de la ficha de Google (Cómo llegar y Ver reseñas).
- Plazo de la garantía. Si no se da, el texto dice "garantía en las reparaciones" sin plazo.
- Fotos nuevas. De momento se usan las 6 de la web actual.
- Condiciones de Kit Digital (logos obligatorios y mantenimiento). Preguntar al agente digitalizador.
- Confirmar con el taller el texto "Servicio multimarca" (viene del cartel del logo).

## 3. Arquitectura y tecnología

- Next.js 16 (App Router) con exportación estática: `output: 'export'`, `trailingSlash: true`, `images.unoptimized: true`.
- React 19, TypeScript, Tailwind CSS 4 (`@tailwindcss/postcss`, tokens con `@theme`).
- Gestor de paquetes: npm. Node 24 en CI y Docker (comprobar disponibilidad en F01).
- TypeScript: la versión que instale `create-next-app`. No migrar a TypeScript 7 ahora: `eslint-config-next` aún necesita la API del compilador TS 6.
- Lint: ESLint CLI con configuración plana y `eslint-config-next`. `next lint` no existe en Next 16.
- Tests: Vitest (lógica pura), Playwright más `@axe-core/playwright` (navegador y accesibilidad), Lighthouse (calidad, solo en F07).
- Contenido: un archivo tipado en `content/` y funciones puras en `lib/`. Sin base de datos, sin login, sin formularios, sin citas.
- Despliegue: Docker multietapa (build con Node, servir con `nginx:alpine`) y `cloudflared` en Docker Compose, en una Raspberry Pi 5 (ARM64).
- La exportación estática no soporta redirecciones, cabeceras ni optimización de imágenes. Esas funciones van en nginx.
- Estructura prevista: `app/`, `components/`, `content/`, `lib/`, `public/`, `tests/`, `docker/`, `scripts/`, `design/`.

### Decisiones de producto y diseño (confirmadas por el usuario)

- Páginas: Inicio, Servicios, Galería, Contacto, Aviso legal, Privacidad. Solo español.
- Contacto: botón de llamada `tel:+34…` (un solo teléfono), WhatsApp `wa.me/34…`, `mailto:`, Cómo llegar (enlace a Google Maps, sin mapa incrustado), horario, Ver reseñas.
- Horario: lunes a viernes, 9:00-14:00 y 16:00-20:00. Sábado y domingo cerrado.
- Portada: héroe con botones, servicios, ventajas, galería destacada, reseñas de Google, horario y mapa, contacto.
- Ventajas (solo estas 4): presupuesto sin compromiso, piezas originales o equivalentes, garantía en las reparaciones, precios claros.
- Servicios: los 6 de la web actual (mantenimiento preventivo, frenos, transmisión, motor, escape, neumáticos y ruedas) más aire acondicionado, diagnóstico electrónico, suspensión y dirección, batería y sistema eléctrico.
- Móvil: barra fija abajo con Llamar, WhatsApp y Cómo llegar.
- Galería: rejilla. Un clic o toque abre la foto ampliada en un `<dialog>` nativo. Otro clic o toque la cierra.
- Estilo: claro y limpio. Tema claro y oscuro automático según el sistema. El usuario eligió esto en lugar de solo claro, con el coste extra de contraste y pruebas.
- Colores del cartel: rojo en los botones de llamada, amarillo en detalles, azul marino en el texto. Tokens de partida, tomados del SVG: rojo `#C83850`, amarillo `#D8D848`, azul marino `#304058`, negro `#15191F`. Contraste calculado a mano: blanco sobre rojo 5,1:1, marino sobre blanco 10,5:1, marino sobre amarillo 6,9:1. La prueba de F03 lo confirma y define los tonos del tema oscuro.
- Logo: entregado el 25 de noviembre de 2026 en `design/logo/` (`autovida-logo.svg`, `.png` de 2725x2176 y `.pdf`). El SVG pesa 22 KB, usa solo polígonos y trazos, sin scripts ni imágenes incrustadas, y mide 681,2 x 544. Los originales quedan en `design/logo/`. La web usa una copia del SVG. El logo trae fondo blanco propio, así que se ve igual en tema oscuro.
- Tipografía: sans sobria y legible, autohospedada con `next/font/local`.
- Analítica: Umami autohospedado, sin cookies. Se configura por variables de entorno. Sin variables no se carga el script.
- Pie de página: logos de Kit Digital obligatorios (2 PNG de la web actual).
- Se mantienen las URLs `/servicios/`, `/galeria/` y `/contacto/`. Las URLs antiguas que cambien llevan redirección 301 en nginx.

## 4. Reglas obligatorias

- Seguir las instrucciones del repositorio y las reglas globales del usuario.
- Solo el orquestador edita este archivo. Los escritores informan mediante archivos de informe.
- Los modos de ejecución crean un commit por fase verificada. No hay atribución de IA en los commits.
- Todo texto marcado `PENDIENTE:` bloquea el build de producción. Las fases F01 a F09 verifican con `ALLOW_PENDING=1`. Solo la prueba del guardián (F02) y la fase F11 construyen sin esa variable.
- La variable `SITE_ENV` (`staging` o `production`) decide `noindex`, `robots.txt`, la URL base del sitemap y el sitio de Umami. El sitio de pruebas usa `staging`. Solo producción se indexa.
- No publicar datos de clientes. No copiar precios, cifras ni textos de la plantilla anterior.
- No hacer push, PR, publicación ni despliegue sin petición explícita del usuario.
- No hacer commit con pruebas obligatorias fallando. No forzar push sin autorización.

## 5. Comandos

```sh
# instalar
npm ci
# desarrollo
npm run dev
# puerta común de fase (orquestador, antes de cada commit): estático + build + navegador
ALLOW_PENDING=1 npm run check   # eslint . && tsc --noEmit && vitest run && next build && npm run budgets
ALLOW_PENDING=1 npm run e2e     # Playwright contra out/ servido en local
# puerta de entrega: puerta común + build de producción sin pendientes + Docker + Lighthouse
npm run build                   # debe fallar si queda algún PENDIENTE:
npm run docker:verify           # construye la imagen, la arranca y comprueba cabeceras y redirecciones con curl
npm run lighthouse
```

Los nombres exactos de los scripts se fijan en F01. Medir el tiempo de la puerta común en F01 y registrarlo aquí. Playwright necesita navegadores instalados.
Docker local es x86_64. La imagen ARM64 se valida en la Raspberry Pi en F08.

## 6. Debate del plan

- Rondas: boceto ciego (7 fases, 8 riesgos) y 3 rondas sobre el plan. Ronda 1: 12 hallazgos (2 bloqueantes, 6 mayores, 4 menores). Ronda 2: 10 corregidos, 1 parcial, 1 excepción aceptada (dos tamaños de WebP) y 3 hallazgos mayores nuevos. Ronda 3: los 4 corregidos y ningún hallazgo bloqueante o mayor. Parada por falta de hallazgos nuevos. Crítico: OpenCode `critica`, sesión única.
- Cambios adoptados del boceto: protección de datos pendientes que rompe el build de producción; hueco fijo para el logo; manejador de clic en el visor (`closedby="any"` solo funciona en Chrome 135+, Firefox 144+ y Safari 26.2+); CSP mínima documentada; plan de vuelta atrás del DNS.
- Cambios adoptados en la ronda 1:
  - C01: `error_page 404 /404.html` en nginx.
  - C02 y C06: el DNS se divide en dos fases. F10 mueve el DNS a Cloudflare sin cambiar nada. F11 cambia la web con un registro y TTL bajo. La vuelta atrás es un registro, no un cambio de servidores de nombres.
  - C03 y C07: `ALLOW_PENDING=1` hasta F09 y `SITE_ENV` para `noindex`, `robots`, sitemap y Umami.
  - C04: F06 depende de F05.
  - C05: presupuestos de imagen fijados en F01. Sin excepciones silenciosas en Lighthouse.
  - C08: prueba de contraste de tokens en Vitest.
  - C09: Node 24 fijado en todo. Se quita la exigencia de `actionlint`.
  - C10: `frame-ancestors 'none'` y el token de Cloudflare nunca se imprime.
  - C11: decisión de Cookies y un solo teléfono comprobados.
  - C12: se quita el agente de pruebas de F02, las capturas de referencia y Uptime Kuma de F08.
- Rechazado con evidencia: C12, un solo tamaño de WebP. La rejilla necesita miniaturas para cumplir el presupuesto de imagen. El crítico lo aceptó en la ronda 2.
- Cambios adoptados en la ronda 2:
  - N01: contrato de variables `SITE_ENV`, `SITE_URL` y compañía en F01 con prueba de Vitest.
  - N02: orden de F09 frente a F11 y comprobación en producción si el logo llega tarde.
  - N03 y C06: F10 deja la web en solo DNS. F11 usa un CNAME con proxy al túnel y la forma exacta se anota en F10.
- Decisiones abiertas para el usuario: ninguna por ahora.

## 7. Plan de fases

| Fase | Título | Estado |
|------|--------|--------|
| F01 | Base del proyecto y puerta de calidad | Pendiente |
| F02 | Contenido tipado, enlaces y guardia de pendientes | Pendiente |
| F03 | Sistema de diseño y estructura común | Pendiente |
| F04 | Páginas: Inicio, Servicios, Contacto y legales | Pendiente |
| F05 | Galería y visor ampliado | Pendiente |
| F06 | Imagen Docker y nginx | Pendiente |
| F07 | SEO, analítica y calidad | Pendiente |
| F08 | Sitio de pruebas en la Raspberry Pi | Pendiente (necesita acciones humanas) |
| F09 | Favicon, imagen social y revisión del logo | Pendiente |
| F10 | DNS a Cloudflare sin cambios | Pendiente (acciones humanas, sin código) |
| F11 | Cambio de la web y vuelta atrás | Pendiente (necesita datos legales y acciones humanas) |

### F01: Base del proyecto y puerta de calidad

- Objetivo: proyecto Next.js 16 que compila a `out/` y tiene la puerta de calidad y la integración continua.
- Tareas:
  - [ ] Crear el proyecto en la raíz con `create-next-app` (TypeScript, Tailwind, ESLint, App Router, npm). Conservar `README.md`, `.gitignore` y `TASKS.md`.
  - [ ] Configurar `output: 'export'`, `trailingSlash: true`, `images.unoptimized: true`.
  - [ ] Añadir scripts `check`, `e2e`, `docker:verify`, `lighthouse` (los dos últimos pueden ser stubs documentados hasta F06 y F07).
  - [ ] Añadir Vitest, Playwright con Chromium y `@axe-core/playwright`. Una prueba de humo por herramienta.
  - [ ] Fijar Node 24 en `.nvmrc`, `engines` (`>=24`), CI y, más tarde, Docker. El Node 26 local cumple `engines`.
  - [ ] `scripts/check-budgets.mjs` (`npm run budgets`): falla si una miniatura de `out/` pesa más de 200 KB o una imagen ampliada más de 600 KB.
  - [ ] Crear `.github/workflows/ci.yml` con lint, tipos, Vitest, build, presupuestos y e2e. Sin despliegue.
  - [ ] Actualizar `README.md` con instalación, comandos y estructura reales, y quitar la hoja de ruta duplicada de este archivo.
  - [ ] Actualizar `.gitignore` (`out/`, `.next/`, `playwright-report/`, `test-results/`, `.env*`, sin excluir `.env.example`).
  - [ ] `lib/env.ts` y `.env.example` con el contrato de variables: `SITE_ENV` (`staging` o `production`; por defecto `staging`, que es el valor seguro porque lleva `noindex`), `SITE_URL` (obligatoria si `SITE_ENV=production`; por defecto `http://localhost:3000` en `staging`), `ALLOW_PENDING`, `NEXT_PUBLIC_UMAMI_URL`, `NEXT_PUBLIC_UMAMI_WEBSITE_ID`. Un valor no válido de `SITE_ENV` o la falta de `SITE_URL` en producción detiene el build con un error claro.
- Dependencias: ninguna.
- Criterios de aceptación:
  - `npm ci` termina con código 0 en un clon limpio.
  - `ALLOW_PENDING=1 npm run check` termina con código 0 y genera `out/index.html`.
  - `npm run e2e` pasa la prueba de humo con Chromium contra `out/`.
  - Un cambio de tipo erróneo o un error de lint hace fallar `check` (probado una vez y revertido).
  - `npm run budgets` falla con una imagen de prueba que supera el límite (probado una vez y revertido).
  - Prueba de Vitest de `lib/env.ts`: `SITE_ENV` ausente da `staging`; `SITE_ENV=production` sin `SITE_URL` lanza error; un valor desconocido lanza error.
  - El archivo del workflow existe. La ejecución real en GitHub queda sin verificar hasta el primer push autorizado.
  - El tiempo de la puerta común queda medido y escrito en la sección 5.
- Pruebas requeridas: humo de Vitest, humo de Playwright, humo de axe en la portada.
- Evidencia de pruebas: pendiente.
- Veredicto de revisión: pendiente.
- Archivos cambiados: pendiente.
- Riesgos: conflicto de `create-next-app` con archivos existentes; TypeScript 7 frente a `eslint-config-next`; Node 24 no disponible en la imagen elegida.
- Estado: Pendiente.

### F02: Contenido tipado, enlaces y guardia de pendientes

- Objetivo: una única fuente de datos del taller y funciones puras de enlaces probadas. El build de producción falla si queda un dato pendiente.
- Tareas:
  - [ ] `content/site.ts` con tipos: nombre, dirección, teléfono, WhatsApp, correo, horario, servicios, ventajas, enlaces de Google, textos de legales con `PENDIENTE:`.
  - [ ] `lib/links.ts`: `telHref`, `whatsappHref`, `mailtoHref`, `mapsHref` (con el esquema de URLs de Google Maps por búsqueda de dirección si falta el enlace de la ficha).
  - [ ] `lib/hours.ts`: formato del horario y texto accesible.
  - [ ] `scripts/check-pending.mjs`: recorre el contenido y falla con código distinto de 0 si aparece `PENDIENTE:` y `ALLOW_PENDING` no vale `1`. Se enlaza en `npm run build`.
- Dependencias: F01.
- Criterios de aceptación:
  - Prueba de Vitest: `telHref` produce `tel:+34XXXXXXXXX`; `whatsappHref` produce `https://wa.me/34XXXXXXXXX` sin `+`, espacios ni guiones, con texto precodificado opcional.
  - Valores dorados fijados aquí, no salidos del código: la entrada `625 133 827` produce `tel:+34625133827` y `https://wa.me/34625133827`.
  - Prueba de Vitest: número con espacios o `+34` de entrada produce el mismo resultado; entrada inválida lanza un error claro.
  - Con un `PENDIENTE:` en el contenido, `npm run build` sale con código distinto de 0 y `ALLOW_PENDING=1 npm run build` sale con 0.
  - El contenido no incluye precios, cifras ni textos de la plantilla anterior.
- Pruebas requeridas: Vitest de enlaces y horario; prueba del guardián con y sin variable.
- Evidencia de pruebas: pendiente.
- Veredicto de revisión: pendiente.
- Archivos cambiados: pendiente.
- Riesgos: un enlace de teléfono o WhatsApp erróneo deja al cliente sin contacto. Por eso los criterios fijan valores dorados y F04 comprueba los enlaces en el navegador.
- Estado: Pendiente.

### F03: Sistema de diseño y estructura común

- Objetivo: tokens, fuentes, tema claro y oscuro, cabecera, pie y barra fija móvil.
- Tareas:
  - [ ] Tokens con `@theme`: rojo, amarillo, azul marino, neutros, espaciado, radios. Variantes para modo oscuro con `prefers-color-scheme`.
  - [ ] Fuente sans autohospedada con `next/font/local` (licencia libre, `.woff2` en el repo). El escritor propone una y registra su licencia.
  - [ ] `components/`: `Header` (logo SVG de `design/logo/` copiado a `public/`, en hueco de tamaño fijo con proporción 681,2 x 544 y `alt="Autovida, servicio multimarca"`), `Footer` (horario, enlaces legales, dos logos de Kit Digital con `alt`), `MobileBar` (Llamar, WhatsApp, Cómo llegar; solo en pantallas pequeñas), `Button`, `Section`.
  - [ ] Enlace "Saltar al contenido", estilos de foco visibles, tamaño táctil mínimo de 44 px.
  - [ ] La barra móvil no tapa contenido (margen inferior en el cuerpo).
- Dependencias: F02.
- Criterios de aceptación:
  - Cero peticiones a dominios externos para fuentes (comprobado en Playwright).
  - axe sin violaciones graves en claro y en oscuro (`colorScheme` emulado) en la plantilla base.
  - Una prueba de Vitest calcula el contraste de cada par de tokens (texto sobre fondo, texto sobre botón rojo, texto sobre amarillo) en claro y en oscuro. Exige 4,5:1 en texto normal y 3:1 en texto grande. Si un par no alcanza el mínimo, se ajusta el token. axe lo confirma en el navegador.
  - A 360 px de ancho: la barra aparece con 3 botones, cada uno enlaza a su `href` esperado, no hay desbordamiento horizontal. A 1024 px la barra no se muestra.
  - El logo SVG ocupa siempre el mismo espacio (sin salto de maquetación al cargar).
- Pruebas requeridas: Vitest de contraste de tokens; Playwright (viewport 360 y 1024, claro y oscuro); axe.
- Evidencia de pruebas: pendiente.
- Veredicto de revisión: pendiente.
- Archivos cambiados: pendiente.
- Riesgos: coste de contraste doble; la barra fija tapa el pie; el logo pierde legibilidad en tamaños pequeños de cabecera móvil (probar a 360 px).
- Estado: Pendiente.

### F04: Páginas: Inicio, Servicios, Contacto y legales

- Objetivo: todas las páginas de texto, con metadatos y contenido del archivo tipado.
- Tareas:
  - [ ] Inicio: héroe con "Llamar" y "WhatsApp", servicios, ventajas, horario y Cómo llegar, Ver reseñas, contacto. La galería destacada llega en F05.
  - [ ] Servicios: 10 servicios con descripciones propias. Sin los textos copiados mal de la plantilla.
  - [ ] Contacto: dirección, teléfono, WhatsApp, correo, horario, Cómo llegar.
  - [ ] Aviso legal y Privacidad con datos del titular marcados `PENDIENTE:`. La privacidad describe Umami sin cookies y el tratamiento de datos.
  - [ ] `metadata` por página (título, descripción, canonical, `lang="es"`).
  - [ ] Página 404 propia (`app/not-found.tsx`, que la exportación emite como `404.html`).
  - [ ] Decisión de Cookies: no hay página de Cookies si F07 demuestra que no se escriben cookies ni `localStorage`. Privacidad lo indica.
- Dependencias: F03.
- Criterios de aceptación:
  - Playwright: `/`, `/servicios/`, `/contacto/`, `/aviso-legal/`, `/privacidad/` responden con contenido y un solo `h1`.
  - Los enlaces `tel:`, `wa.me`, `mailto:` y Maps de cada página coinciden con los de `lib/links.ts`. Todos los `tel:` y `wa.me` de una página usan el mismo número.
  - axe sin violaciones graves en cada página, claro y oscuro.
  - Ninguna página contiene los textos "Transmission", "Diagnostics", "$", ni cifras de la plantilla anterior (prueba de cadenas prohibidas).
  - Con `ALLOW_PENDING` sin definir, el build falla por los datos legales (comportamiento esperado hasta F11).
- Pruebas requeridas: Playwright por página; axe; prueba de cadenas prohibidas.
- Evidencia de pruebas: pendiente.
- Veredicto de revisión: pendiente.
- Archivos cambiados: pendiente.
- Riesgos: afirmaciones sin confirmar (por ejemplo "multimarca"); textos legales incorrectos.
- Estado: Pendiente.

### F05: Galería y visor ampliado

- Objetivo: galería con visor ampliado accesible y galería destacada en la portada.
- Tareas:
  - [ ] Script `scripts/optimize-images.mjs` (manual) que convierte las 6 fotos a WebP en dos tamaños: miniatura para la rejilla y ampliada para el visor. Con un solo tamaño no se cumple el presupuesto. Se confirma el permiso de uso con el taller.
  - [ ] `Gallery` en cliente: rejilla de botones; un clic abre un `<dialog>` con `showModal()`; un clic en cualquier punto del visor lo cierra. Escape también cierra.
  - [ ] Devolver el foco al botón que abrió el visor.
  - [ ] Galería destacada con 3 o 4 fotos en la portada y enlace a `/galeria/`.
  - [ ] Cada foto lleva `alt` descriptivo, ancho y alto (sin salto de maquetación).
- Dependencias: F04.
- Criterios de aceptación:
  - Playwright: clic en una foto abre el visor; un clic en la imagen, en el fondo o en cualquier zona lo cierra; Escape lo cierra; el foco vuelve al botón de origen.
  - Sin dependencia de `closedby="any"`: la prueba pasa también con ese atributo ignorado (clic por manejador).
  - axe sin violaciones con el visor abierto, claro y oscuro.
  - `npm run budgets` pasa: miniaturas ≤ 200 KB, imágenes ampliadas ≤ 600 KB. Las ampliadas se cargan al abrir el visor, no en la carga inicial.
  - La galería sin JavaScript sigue mostrando las fotos en la rejilla.
- Pruebas requeridas: Playwright de interacción con ratón y teclado; axe con visor abierto.
- Evidencia de pruebas: pendiente.
- Veredicto de revisión: pendiente.
- Archivos cambiados: pendiente.
- Riesgos: fotos sin permiso o de mala calidad; foco atrapado; el visor no cierra en iOS antiguo.
- Estado: Pendiente.

### F06: Imagen Docker y nginx

- Objetivo: imagen que sirve `out/` con cabeceras de seguridad y redirecciones, probada con `curl`.
- Tareas:
  - [ ] `Dockerfile` multietapa: Node 24 compila, `nginx:alpine` sirve. Soporte `linux/arm64`. Argumentos de build `ALLOW_PENDING` y `SITE_ENV`.
  - [ ] `docker/nginx.conf`: `try_files $uri $uri/ =404`, `error_page 404 /404.html`, caché inmutable en `/_next/static`, compresión, redirecciones 301 (lista en `docker/redirects.conf`).
  - [ ] Cabeceras: CSP mínima documentada línea a línea (con `frame-ancestors 'none'`), `X-Content-Type-Options`, `Referrer-Policy`, `Permissions-Policy`.
  - [ ] Cabecera `X-Robots-Tag: noindex` activada en tiempo de ejecución por `SITE_ENV=staging`.
  - [ ] `compose.yml` con el servicio `web` y el servicio `cloudflared` en un perfil aparte (token por variable de entorno, sin valores en el repositorio).
  - [ ] `npm run docker:verify`: construye, arranca, ejecuta `curl` y detiene el contenedor.
  - [ ] `.dockerignore` y `.env.example`.
- Dependencias: F05 (la imagen sirve el `out/` completo).
- Criterios de aceptación:
  - `curl -I` a `/`, `/servicios/`, `/galeria/` y `/contacto/` da 200 y las cabeceras esperadas. Una ruta inexistente da 404 y su cuerpo es el de `404.html`.
  - `/_next/static/...` devuelve `Cache-Control` con `immutable`.
  - Cada redirección de `docker/redirects.conf` da 301 al destino esperado.
  - La CSP permite solo `self`, el servidor de Umami configurado y los logos locales. Una prueba de Playwright contra el contenedor no registra errores de CSP en la consola.
  - La imagen se construye con `--platform linux/arm64` (con QEMU o `buildx`) o el límite queda anotado si no es posible en esta máquina.
  - Con `SITE_ENV=staging`, la respuesta lleva `X-Robots-Tag: noindex`; con `production`, no.
  - `docker:verify` no usa ni imprime el token de Cloudflare.
- Pruebas requeridas: script `curl` con comprobaciones; Playwright contra el contenedor; agente de pruebas para cabeceras y redirecciones.
- Evidencia de pruebas: pendiente.
- Veredicto de revisión: pendiente.
- Archivos cambiados: pendiente.
- Riesgos: CSP demasiado estricta bloquea Umami o estilos; redirecciones que forman bucle; no se puede ejecutar ARM64 localmente.
- Estado: Pendiente.

### F07: SEO, analítica y calidad

- Objetivo: metadatos SEO locales, analítica sin cookies y umbrales de calidad medidos.
- Tareas:
  - [ ] `app/sitemap.ts` y `app/robots.ts` estáticos. La URL base viene de `SITE_URL`. Con `SITE_ENV=staging`, `robots.txt` prohíbe todo y el sitemap queda vacío. Con `production`, el sitemap lista todas las páginas, legales incluidas.
  - [ ] Datos estructurados JSON-LD `AutoRepair` o `LocalBusiness` generados desde `content/site.ts`.
  - [ ] Imagen Open Graph.
  - [ ] Script de Umami cargado solo si existen las variables `NEXT_PUBLIC_UMAMI_URL` y `NEXT_PUBLIC_UMAMI_WEBSITE_ID`. Sin cookies ni `localStorage`. Pruebas y producción usan sitios de Umami distintos.
  - [ ] Lighthouse móvil sobre `out/` servido en local para las 4 páginas principales.
  - [ ] Prueba de enlaces rotos.
- Dependencias: F05 y F06.
- Criterios de aceptación:
  - Lighthouse móvil en las 4 páginas: rendimiento ≥ 90, accesibilidad ≥ 95, buenas prácticas ≥ 95, SEO ≥ 95. Si una página no llega, se corrige. El orquestador solo cambia este criterio con aprobación del usuario.
  - JSON-LD válido y coherente con los datos visibles. La prueba compara teléfono, dirección y horario con el contenido.
  - Prueba de Playwright: la página no escribe cookies ni `localStorage`.
  - Sin enlaces internos rotos.
  - Pruebas de Vitest de las funciones que generan `robots` y el sitemap para `staging` y `production`.
  - Con las variables de Umami vacías, no se solicita ningún script de analítica.
- Pruebas requeridas: Lighthouse, Playwright, enlaces rotos, validación de JSON-LD.
- Evidencia de pruebas: pendiente.
- Veredicto de revisión: pendiente.
- Archivos cambiados: pendiente.
- Riesgos: umbrales de Lighthouse inestables en el equipo local; el visor o las fotos bajan el rendimiento.
- Estado: Pendiente.

### F08: Sitio de pruebas en la Raspberry Pi

- Objetivo: la web visible en `autovida.nastymur.com`, con `noindex`, validada desde el móvil real.
- Tareas:
  - [ ] Usuario: crear el túnel en Cloudflare y dar el token. Mapear `autovida.nastymur.com` a `http://web:80`.
  - [ ] Usuario: clonar el repositorio en la Raspberry Pi y arrancar `docker compose --profile tunnel up -d --build` con `ALLOW_PENDING=1` y `SITE_ENV=staging`.
  - [ ] Crear un sitio de Umami solo para pruebas y añadir sus variables. No mezclar con el de producción.
  - [ ] Revisión visual en un móvil real, en claro y en oscuro, con la barra fija y el visor.
- Dependencias: F07. Acciones humanas.
- Criterios de aceptación:
  - `curl -I https://autovida.nastymur.com/` devuelve 200 con `X-Robots-Tag: noindex`.
  - Los botones Llamar, WhatsApp y Cómo llegar funcionan en un móvil real.
  - La imagen ARM64 arranca en la Raspberry Pi y `docker compose ps` muestra los servicios sanos.
  - El usuario y el dueño del taller revisan la web y dan su visto bueno o una lista de cambios.
- Pruebas requeridas: `curl` remoto; revisión manual en móvil; lista de cambios.
- Evidencia de pruebas: pendiente.
- Veredicto de revisión: no aplica.
- Archivos cambiados: pendiente.
- Riesgos: la Raspberry Pi o la red doméstica cae; el túnel apunta mal; Umami mal configurado.
- Estado: Pendiente.

### F09: Favicon, imagen social y revisión del logo

- Objetivo: favicon, imagen Open Graph y comprobación del logo en todos los tamaños y temas. El SVG ya se usa desde F03.
- Tareas:
  - [ ] Generar el favicon (`favicon.ico` o SVG, y `apple-touch-icon`) desde `design/logo/autovida-logo.svg`.
  - [ ] Generar la imagen Open Graph de 1200 x 630 con el logo sobre fondo claro. Guardar el script en `scripts/`.
  - [ ] Revisar el logo en cabecera móvil (360 px), cabecera de escritorio y pie, en claro y oscuro.
- Dependencias: F03.
- Criterios de aceptación:
  - El favicon y la imagen Open Graph salen del SVG de `design/logo/`. Los metadatos de cada página los referencian.
  - La imagen Open Graph mide 1200 x 630 y pesa menos de 300 KB.
  - Playwright captura la cabecera a 360 px y a 1024 px en ambos temas. El logo no se recorta ni desborda.
  - Sin regresión de Lighthouse respecto a F07 en la portada.
- Pruebas requeridas: Playwright de capturas y de metadatos; Lighthouse de la portada.
- Evidencia de pruebas: pendiente.
- Veredicto de revisión: pendiente.
- Archivos cambiados: pendiente.
- Riesgos: el logo con detalles finos pierde legibilidad en la pestaña del navegador (favicon de 16 px); puede hacer falta una versión simplificada.
- Estado: Pendiente.

### F10: DNS a Cloudflare sin cambios

- Objetivo: Cloudflare responde al DNS de `tallerautovida.com` con los mismos registros que hoy. La web actual y el correo siguen igual.
- Tareas:
  - [ ] Acceso a Namecheap y a Cloudflare. El hermano del usuario contrató el dominio.
  - [ ] Inventario del DNS actual: todos los registros (A, CNAME, MX, TXT de SPF, DKIM y DMARC si existen) y los servidores de nombres. Guardarlo fuera de Git.
  - [ ] Mirar "Email Forwarding" en Namecheap: lista de alias y destinos.
  - [ ] Añadir el dominio a Cloudflare. Comprobar a mano que cada registro importado coincide con el inventario. Los registros de la web quedan en modo solo DNS (nube gris) y apuntan al servidor actual, igual que hoy.
  - [ ] Leer la documentación de Cloudflare sobre túneles en el dominio raíz. Anotar aquí la fuente y la forma exacta del registro (se espera un CNAME con proxy a `<id-del-túnel>.cfargotunnel.com`, aplanado en la raíz) para usarla en F11.
  - [ ] Cambiar los servidores de nombres en Namecheap por los de Cloudflare.
  - [ ] Esperar la propagación, que puede tardar hasta 48 horas. Comprobar web y correo.
- Dependencias: ninguna de código. Acciones humanas. Terminar al menos 72 horas antes de F11.
- Criterios de aceptación:
  - La web WordPress responde igual que antes (mismo estado HTTP y mismo contenido en la portada).
  - Una consulta DNS pública muestra los servidores de Cloudflare y los registros del inventario sin diferencias.
  - Si existe correo del dominio, una prueba de envío y de recepción funciona.
  - El inventario y los servidores de nombres originales están guardados fuera del repositorio.
- Pruebas requeridas: consultas DNS, `curl`, prueba de correo.
- Evidencia de pruebas: pendiente.
- Veredicto de revisión: no aplica.
- Archivos cambiados: ninguno.
- Riesgos: registros MX o SPF olvidados; propagación lenta; falta de acceso al panel de Namecheap.
- Estado: Pendiente.

### F11: Cambio de la web y vuelta atrás

- Objetivo: `tallerautovida.com` sirve la web nueva. La vuelta atrás es un cambio de registro, tarda minutos y está ensayada.
- Tareas:
  - [ ] Rellenar los datos legales y sustituir todos los `PENDIENTE:`. Revisar los textos con la gestoría.
  - [ ] Comprobar con el agente digitalizador las condiciones y los logos de Kit Digital.
  - [ ] Copia de seguridad de WordPress (archivos y base de datos) desde el hosting actual.
  - [ ] Bajar el TTL de los registros de la web a 60-300 s, 24 horas antes del cambio.
  - [ ] Prueba previa: enlazar `nuevo.tallerautovida.com` al túnel y comprobar el recorrido completo en la zona real.
  - [ ] Ensayar la vuelta atrás con ese subdominio: cambiar el registro al valor anterior y medir el tiempo.
  - [ ] Construir producción con `SITE_ENV=production` y sin `ALLOW_PENDING`. Arrancar la imagen en la Raspberry Pi.
  - [ ] Cambio: sustituir los registros de la raíz y de `www` por el CNAME con proxy (nube naranja) al túnel, con la forma anotada en F10. Guardar el valor anterior (registro A y modo solo DNS) para volver.
  - [ ] Comprobar redirecciones, correo, enlaces y la web en un móvil real.
  - [ ] Enviar el sitemap a Search Console y actualizar la ficha de Google con la web.
  - [ ] Opcional: añadir el sitio a Uptime Kuma.
  - [ ] Mantener WordPress 7 días como respaldo.
  - [ ] Actualizar `README.md` con el estado final y la dirección pública.
- Dependencias: F08 aprobado, F10 estable y datos legales completos. F09 no bloquea esta fase.
- Criterios de aceptación:
  - `curl -I https://tallerautovida.com/` devuelve 200 sin `noindex`. Las URLs antiguas redirigen con 301.
  - El build de producción pasa sin `ALLOW_PENDING`. Ninguna página contiene `PENDIENTE:`.
  - En el ensayo, volver al registro anterior tarda menos de 10 minutos con el TTL bajo.
  - El correo del dominio, si existe, sigue llegando después del cambio.
  - Ficha de Google y Search Console actualizadas.
- Pruebas requeridas: `curl` de producción; prueba de correo; revisión manual en móvil.
- Evidencia de pruebas: pendiente.
- Veredicto de revisión: no aplica.
- Archivos cambiados: pendiente.
- Riesgos: ranking de Google; caída del servicio doméstico; túnel en el dominio raíz solo parcialmente verificado (se prueba antes en `nuevo.tallerautovida.com`).
- Estado: Pendiente.

## 8. Fases completadas

| Fase | Resultado | Verificación | Commit |
|------|-----------|--------------|--------|

## 9. Siguiente acción recomendada

- Siguiente fase: F01, tras la aprobación del usuario. F10 no depende del código y puede hacerse en paralelo.
- Comando de reanudación: `/taskflow run F01`.
- Esfuerzo recomendado: medio.
