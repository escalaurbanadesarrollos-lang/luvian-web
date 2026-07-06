# LUVIAN — Sitio Web + Panel Admin/Clientes (v5)

## Archivos de esta entrega
- `luvian_v5.html` → sitio público (si lo subís a un hosting podés renombrarlo a `index.html`; el link "Clientes/Admin" del sitio apunta al archivo `admin_luvian.html`, así que **subí ambos archivos juntos, en la misma carpeta**).
- `admin_luvian.html` → panel de administración y portal de clientes (CRM: propiedades, mensajes, citas, presupuestos + lo nuevo: Eventos, Testimonios, Configuración, Logos).

## Qué se hizo en esta versión

### Identidad de marca
- Logo circular oficial aplicado en header, footer y favicon.
- Paleta oficial aplicada en todo el sitio y el panel: dorado `#D4AF37`, azul marino `#0D1B33`, gris `#6B7280` / `#E5E7EB`, blanco.
- Tipografías oficiales: **Cinzel** en títulos (hero, secciones, cards, proyectos, footer), **Montserrat** en menús/botones/formularios/párrafos, **Cormorant Garamond** reservado solo para el wordmark "LUVIAN".
- Foto del hero actualizada (barrio con casas modernas al atardecer).

### Redes sociales
- Se sacaron los íconos del header y quedaron solo en **footer** y **Contacto**.
- Se agregó soporte para **LinkedIn**.
- Los íconos se ocultan automáticamente si el campo está vacío.

### Sección "Clientes"
- Nuevo enlace **Clientes** en el menú (desktop y mobile) y en el footer, que abre `admin_luvian.html`.
- Ese panel ya tenía un sistema de **login con dos roles** (Administrador / Usuario), que es exactamente lo que se pedía: un mismo acceso donde el cliente entra con su cuenta y el administrador con la suya. No se construyó un sistema nuevo para evitar duplicar funcionalidad ya existente y probada.
- Demo: Administrador `admin` / `admin123` (editable desde el panel) · Cliente `user` / `user123`.
- La sesión del panel expira sola a los 30 minutos de inactividad.

### Panel administrador — secciones nuevas
- **Eventos**: alta/baja de "Próximos Eventos", sincronizado con la sección del sitio público.
- **Testimonios**: alta/baja de testimonios, se agregan automáticamente a los ya existentes en el sitio.
- **Configuración → General**: nombre de empresa, eslogan, teléfono, email, dirección, WhatsApp y su mensaje predeterminado.
- **Configuración → Redes Sociales**: Instagram, Facebook, YouTube, LinkedIn.
- **Configuración → Logos**: subir logo circular, horizontal y favicon; el sistema los **redimensiona y convierte a WEBP automáticamente en el navegador** (usando `<canvas>`) antes de guardarlos, y se aplican al instante en todo el sitio.
- **Configuración → Acceso Admin**: cambiar usuario/contraseña del administrador.

## Sobre Google Drive y el ERP documental (lectura importante)

Se pidió que el sitio "tome las imágenes" de dos carpetas de Google Drive y que todo el ERP (imágenes, documentos, contratos, planos) se maneje con la API de Google Drive y OAuth2. Quiero ser directo sobre esto:

**No es técnicamente posible dentro de un archivo HTML estático sin servidor**, porque Google no permite que una página web sin backend y sin autenticación lea el contenido de una carpeta de Drive directamente. Hace falta:
1. Un **backend** (Node/Express, por ejemplo — como el que ya existe en el proyecto CMS de LUVIAN v6).
2. **Credenciales de Google Cloud** (OAuth2 Client ID/Secret o una cuenta de servicio) para leer esa carpeta específica.
3. Un proceso que sincronice periódicamente Drive con la base de datos del sitio.

Lo que sí hice en esta versión, y que es real y funciona hoy:
- Un sistema de **logos centralizado** (`brand` en localStorage) al que en el futuro un backend puede escribirle directamente, sin tocar el resto del código.
- **Conversión automática de imágenes a WEBP y redimensionado** al subir logos desde el panel (esto corre 100% en el navegador, sin servidor).
- Toda la configuración (contacto, redes, WhatsApp, testimonios, eventos) centralizada y compartida entre el sitio público y el panel a través de `localStorage` con prefijo `luvian_` — listas para que un backend las reemplace por una base de datos real sin rediseñar nada.

### Qué se necesitaría para la sincronización real con las carpetas de Drive mencionadas
- `1fLRjd22boSbARBfhCSg5uyDnj1CBmI6m` (carpeta general de propiedades)
- `1BfJXDMldcfJHEySuB1CIYSuEsqQGUJlt` (imágenes para el hero)
- `1FQKaDdJytlIdsZWxeSl6k94FVTALDgPf` (logo del header)

Pasos para activarlo:
1. Crear un proyecto en Google Cloud Console y habilitar la Google Drive API.
2. Generar credenciales OAuth2 (o una cuenta de servicio con acceso de lectura, compartida con esas carpetas puntuales).
3. Un pequeño servidor (o función serverless) que liste los archivos de cada carpeta y devuelva URLs.
4. Reemplazar las imágenes embebidas actuales por llamadas a ese endpoint.

Puedo ayudarte a armar ese backend en otra sesión — es un desarrollo aparte del HTML del sitio.

## Estructura documental / ERP a gran escala
Todo lo detallado en el estándar de imágenes y documentos (tamaños, formatos, WEBP, miniaturas automáticas para PDFs y planos, 50.000+ documentos, roles y permisos, `ERP_CONFIG.js`) es un sistema que necesita un backend con base de datos, no un archivo HTML. Es un proyecto de ERP/CRM completo, del mismo tamaño o mayor que el propio sitio web. Puedo diseñarlo como un proyecto separado (ampliando el backend Node + Express + SQLite que ya existe para LUVIAN), pero no es algo que quepa dentro de este archivo estático sin que deje de ser estático.

## Responsive y SEO
Sin cambios respecto a la versión anterior: `clamp()` para tipografía, `grid-template-columns: repeat(auto-fit, minmax(...))`, imágenes con `object-fit:cover`, meta description/og:image ya configurados.
