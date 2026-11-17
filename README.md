# KAM-HUB-ASPIRIA
Este es un sistema que permite a un KAM o Account Manager gestionar todo el proceso comercial, desde la planificación de prospectos y cuentas, gestión de datos de clientes, como los status de sus cuentas y proceso de ventas y comercial hasta el cierre, gestión de convenios, seguimiento de campañas en tiempo real vigentes y vencidas, etc.

Sistema cerrado: una instancia por cliente, sin indexación en buscadores y acceso solo con usuarios autorizados.

## Estructura

- `index.html` — aplicación KAM HUB con la identidad ASPIRIA (abrir directamente en el navegador).
- `assets/` — logo y favicon ASPIRIA.
- `ACTIVOS/` — material fuente: prototipo original y Manual de Marca v3 (solo referencia, no se publica).
- `docs/SPRINTS.md` — plan de sprints y diagnóstico del prototipo.
- `netlify.toml` — despliegue: publica solo `index.html` + `assets/` en `dist/`, con `robots.txt` y cabeceras `noindex`.

## Accesos

El login tiene dos pestañas: **Cliente** y **Operador** (KAM y administradores). Los usuarios viven en la constante `USERS` de `index.html` (provisional hasta el backend del Sprint 3; la clave se valida en el navegador).

| Usuario | Pestaña | Rol | Qué ve |
|---|---|---|---|
| `luis.saenz@aspiriateam.com` · `stefano.agurto@aspiriateam.com` | Operador | Admin | Todo, consolidado, y gestiona todo (panel Admin incluido) |
| `valentina.herrera@aspiriateam.com` · `diego.salazar@aspiriateam.com` | Operador | KAM | Su vista de trabajo (Mi día, CRM, campañas…) sobre su cartera + dashboard general |
| `gestioncliente@aspiriateam.com` | Cliente | Cliente | Solo lectura, consolidado: Dashboard, Comercial, Puntos de venta, Dashboard Ejecutivo (reportes) y Biblioteca (descargables) |

Las vistas por rol están en `NAV_ROLES`; las acciones de edición bloqueadas para el cliente, en `RO_FNS`.

Clave inicial de todos: `123456789`. Cámbiala en `USERS` antes de entregar la instancia.

## Datos

Todos los comercios, convenios, campañas y contactos son datos de demostración inventados. Para regenerarlos en un navegador que ya los tenía cargados: **Admin → Reiniciar información**.
