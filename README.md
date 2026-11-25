# AutoVida

Web de presentación del Taller Autovida (Orihuela, Alicante).
Sustituye a la web actual en WordPress y usa el mismo dominio, `tallerautovida.com`.

Es el negocio de mi padre. También es un proyecto de portfolio, por eso el repositorio es público.

## Estado

Fase de planificación. Todavía no hay código.

## Alcance

La web es simple. Un visitante debe llamar, escribir o llegar al taller en pocos toques desde el móvil.

Incluye:

- Inicio, Servicios, Galería y Contacto.
- Botón de llamada con enlace `tel:`.
- Enlace "Cómo llegar" a Google Maps.
- Enlace de correo con `mailto:`.
- Horario del taller.
- Páginas legales: aviso legal, privacidad y cookies.

No incluye, por decisión de alcance:

- Citas online.
- Base de datos.
- Login o panel de administración.
- Tienda o blog.
- Formulario con envío de datos.

## Stack

- Next.js 16 con App Router y generación estática.
- TypeScript en modo estricto.
- Tailwind CSS.
- Contenido en un archivo del repositorio, sin base de datos.
- Docker y nginx para servir los archivos estáticos.
- Despliegue propio en una Raspberry Pi con Cloudflare Tunnel.

## Reglas de contenido

- Usa solo datos reales confirmados por el taller.
- No copies precios ni estadísticas de la plantilla anterior.
- No guardes datos de clientes en el repositorio.
- Revisa los permisos de uso de cada foto antes de publicarla.

## Hoja de ruta

1. **Contenido.** Lista de servicios, horario, teléfono, correo y fotos reales.
2. **Maqueta.** Páginas con diseño móvil primero y contenido real.
3. **Calidad.** Accesibilidad, rendimiento, SEO local y páginas legales.
4. **Despliegue de prueba.** Publica en un subdominio y valida el acceso desde fuera.
5. **Cambio de dominio.** Conserva las URLs `/servicios/`, `/galeria/` y `/contacto/`, o añade redirecciones 301.

## Antes del cambio de dominio

La web actual vino de una ayuda Kit Digital. Comprueba las condiciones con el agente digitalizador antes de cambiar el DNS.
Haz una copia de la web WordPress actual. Verifica que el correo del dominio no dependa del hosting anterior.
