# KAM HUB · ASPIRIA — Plan de sprints

Sprints de 2 semanas. Cada sprint cierra con una demo funcional sobre `index.html`.

| Sprint | Objetivo | Estado |
|---|---|---|
| 1 | Identidad ASPIRIA y sistema de diseño | ✅ Hecho |
| 2 | CRM ASPIRIA (solo admins): cuentas grandes, cotizaciones, reuniones y negociaciones | Siguiente |
| 3 | Backend real: autenticación, roles y persistencia | Pendiente |
| 4 | Afiliaciones y base de clientes (ficha 360) | Pendiente |
| 5 | Campañas en tiempo real | Pendiente |
| 6 | Gestión de convenios | Pendiente |
| 7 | Dashboards ejecutivos con datos reales y reportes | Pendiente |
| 8 | IA, notificaciones y salida a producción | Pendiente |

---

## Diagnóstico del prototipo actual (`ACTIVOS/KAM HUB ASPIRIA.html`)

**Qué ya resuelve:** login con dos roles (KAM / Admin), cartera filtrada por KAM, alta de comercios con puntos de venta, contacto y convenio, campañas e incentivos con T&C y puntos de venta, funnel de 6 etapas con historial de acciones, biblioteca de convenios y assets, dashboard ejecutivo, exportables CSV y reinicio de datos.

**Qué falta para ser un CRM real:**

1. **Sin persistencia.** Todo vive en memoria y se pierde al recargar la página.
2. **Autenticación simulada.** Cualquier correo entra. Si el correo contiene "admin", obtiene el rol de administrador. El filtro por KAM solo existe en el navegador.
3. **KPIs fabricados.** `PROGRAM_DATA` está hardcodeado. "Contratos firmados" se calcula como `firmados × 1.093` y "Activas del mes" como `activas × 0.74`.
4. **Fecha fija.** `TODAY = 2026-06-18`, el calendario está anclado a junio y `inThisMonth` está fijo.
5. **El estado de las campañas es estático.** "Activa" o "Por vencer" se escribe a mano y no se calcula a partir de las fechas. Las campañas nuevas siempre quedan como "Borrador".
6. **Las cargas de archivos no funcionan.** Las zonas de "arrastra un archivo" no tienen un `<input type=file>`.
7. **Riesgo de XSS.** El texto ingresado por el usuario se inserta con `innerHTML` sin escapar.
8. **Código sin uso.** `CALLS`, `RESULTS`, `PORTFOLIO`, `GEO`, `calendar()`, `openContactDetail()` y `openInteractionModal()` no se llaman desde ninguna vista. Los contactos no tienen pantalla propia.
9. **IDs con `Date.now()` (+1, +2, …).** Pueden repetirse si se crean dos registros en el mismo milisegundo.

---

## Sprint 1 — Identidad ASPIRIA y sistema de diseño ✅

**Entregado en `index.html`.** El original en `ACTIVOS/` queda intacto como referencia.

- **Tokens de marca** en `:root`, tomados del Manual de Marca v3: navy `#0D1B4B` / `#091340` / `#162050`, teal `#00C4AA`, azul `#2B80D4`, gradiente de 135° y superficies `#F5F7FA` / `#E2E8F2`.
- **Tipografía:** Instrument Serif (400) para titulares, números KPI y el énfasis en cursiva teal. DM Sans para la interfaz.
- **Forma:** bordes de 3–4px, sin sombras pesadas, eyebrows de 10px en mayúsculas con espaciado `.2em`, botón primario teal con texto navy y botón secundario con borde navy.
- **Estructura:** sidebar navy-deep con logo en blanco y el ítem activo marcado con borde teal; login navy con card y franja de gradiente; logo y favicon en `assets/`.
- **Voz de marca:** saludo ejecutivo sin emojis ("Hola, *Valeria*") y titular "Cada cuenta, *bajo control*".

### Paleta de gráficos (validada para daltonismo)

| Uso | Colores | Regla |
|---|---|---|
| Categórica (identidad de series) | `#2B80D4` `#E08A1E` `#00A68F` `#7A5BD0` `#D6608F` `#3A4A8C` | Orden fijo, nunca se cicla. Validada: separación CVD mínima ΔE 12.2 y visión normal ΔE 19.8 |
| Ordinal (etapas del funnel) | `#86B6EF` → `#5598E7` → `#2B80D4` → `#1C5CAB` → `#0D366B` | Un solo tono (azul de marca) que va de claro a navy y avanza con la etapa |
| Estados | Bien `#00A68F` · Alerta `#E08A1E` · Crítico `#D94F4F` | Siempre con etiqueta o texto, nunca solo color |
| Magnitud de una serie | `#2B80D4` | Las barras por categoría o distrito usan un solo color |

**Decisión de marca:** el manual restringe el rojo `#D94F4F` a "Sin ASPIRIA". En el CRM lo usamos solo para lo crítico (gestión *Perdida*, vencimiento ≤7 días), que es su equivalente semántico. Ningún otro elemento lo usa.

**Cambios en los gráficos:**

- El donut de 11 categorías se reemplazó por barras horizontales.
- La línea de tendencia ya no se deforma.
- Gantt, barras, etapas y tendencia tienen tooltip al pasar el mouse.
- Los números van en color de texto, no en el color de la serie.
- Los subtítulos de los KPI ya no salen en verde como si fueran variaciones.

---

### Ajustes al módulo Comercial (adelantados del Sprint 4) ✅

- Selector **Tarjetas / Tabla**. La tabla muestra nombre comercial con la razón social al lado, RUC, categoría, KAM, puntos de venta, modalidad, estado y vigencia del convenio, si el archivo del convenio fue subido, y las acciones «Ver ficha» y «Editar».
- **Estado del convenio:** Vigente / Por vencer (≤60 días) / Vencido / Sin convenio. El sistema detecta si el PDF fue subido; si falta, se puede subir desde la ficha.
- **Categorías nuevas:** Restaurantes, Experiencias (con los subtipos Viajes, Terrazas y Eventos) y Educación. Cada filtro muestra su conteo.
- **Filtros combinables:** KAM (solo admin), estado del convenio (Vigente / Por vencer / Vencido / Por vencer o vencido / Sin vigencia / Sin convenio) y archivo del convenio (Subido / Pendiente). Cada opción muestra su conteo. Los avisos «sin archivo» y «por vencer o vencidos» aplican el filtro con un clic, y hay un botón «Limpiar filtros».
- **Buscador** junto a los botones de acción. Busca por nombre, razón social, RUC, código, KAM o modalidad.
- **«Editar»** abre el formulario de registro con los datos cargados. Si cambia el nombre, las referencias en otros módulos se actualizan.
- La carga del convenio es real (PDF, máx. 10 MB, con arrastrar y soltar), también desde Biblioteca. Mientras no exista el backend del Sprint 3, el archivo se pierde al recargar la página.
- **Importación desde Excel con plantilla oficial:**
  - *En el formulario del comercio:* «Descargar plantilla» / «Importar Excel» para sus puntos de venta (Vía, Dirección, Distrito, Código(s)).
  - *Carga masiva desde Comercial:* plantilla de dos hojas (Comercios + Puntos de venta, enlazadas por el nombre comercial). Antes de importar muestra una vista previa con los conteos.
  - *Plantillas con validación nativa de Excel* (generadas con ExcelJS): desplegables en Vía, Distrito, Categoría, Tipo de experiencia, KAM y Modalidad. Un valor escrito fuera de la lista se bloquea; un valor pegado que no es válido y los obligatorios vacíos se pintan de rojo. También validan RUC de 11 dígitos, fecha del convenio y Tipo de experiencia solo para Experiencias. La hoja «Listas» está protegida.
  - *Validación por fila:* listas válidas (acepta texto sin tildes y alias como «Av», «Surco» o «SJL»), RUC de 11 dígitos, fechas, duplicados, marcas existentes (solo se agregan sus PDV nuevos) y permisos por KAM. Las filas con error se omiten y el reporte se puede descargar en CSV.
  - Cobertura geográfica: Lima, Callao y 8 ciudades de provincia (Arequipa, Trujillo, Chiclayo, Piura, Cusco, Huancayo, Ica, Iquitos), cada una con sus distritos. En la plantilla Excel, «Ciudad» es una columna con desplegable y la celda se pinta de rojo si el distrito no pertenece a la ciudad.
  - El lector de Excel (SheetJS) se carga desde cdnjs, así que importar requiere conexión a internet.

### Pagos / meses sin intereses (MSI) y cobertura de programas en PDV ✅

- **Marca:** «Afiliada a pagos / meses sin intereses», fecha de afiliación y plazos (3, 6, 9, 12, 18 y 24 meses).
- **PDV:** al afiliar la marca, todos sus PDV quedan con MSI activo. Cada PDV se puede excluir manualmente desde el formulario del comercio o con el interruptor en Puntos de venta.
- **PDV con promoción activa:** el PDV está incluido en una campaña o incentivo vigente hoy (si la promo no tiene PDV seleccionados, aplica a todos).
- **Indicadores:**
  - Bloque «Cobertura de programas en puntos de venta» en Dashboard y Dashboard Ejecutivo: con promo, con MSI, con ambos y sin ningún programa. Cada bloque lleva al detalle filtrado.
  - Columnas de PDV con promo y PDV con MSI en Desempeño por KAM.
- **Filtros y columnas:** en Comercial (MSI por marca) y en Puntos de venta (promo y MSI).
- **Excel:** columnas «Meses sin intereses», «Plazos MSI» y «MSI activo en el PDV» con desplegable Sí/No y validación.
- Si se edita la dirección de un PDV, las campañas e incentivos que lo seleccionaron se actualizan.

### Datos demo ampliados y Dashboard con datos reales ✅

- **Generador con semilla fija** (`genDemo`): produce siempre los mismos datos al recargar o al «Restaurar datos demo». Conserva los 12 comercios ingresados a mano y completa la demo hasta:
  - **75 marcas** con nombres reales y KAM asignado al azar.
  - **64 afiliadas a MSI (85%)**.
  - **100 promociones**: 70 campañas y 30 incentivos, solo en marcas con convenio firmado. Su estado (Activa, Por vencer, Programada, Vencida) sale de las fechas.
  - **223 PDV**: 89 (40%) en los C.C. principales de Lima, 40 (18%) en provincias y 94 en calles de Lima.
  - **Pipeline**: 50 firmados, 18 en negociación, 4 prospectos y 3 perdidos, con acciones vencidas e inactivas.
  - Las marcas sin convenio muestran «Sin convenio».
- Se eliminó `PROGRAM_DATA`. Los KPI, el funnel, los resultados por KAM y la composición por categoría del Dashboard principal se calculan con los datos del sistema y coinciden con el resto de módulos.
- El cronograma muestra las 15 promociones más próximas a vencer.

### Módulo Puntos de venta (antes «Base de comercios») ✅

**Comercial** gestiona marcas, contactos, convenios y campañas. **Puntos de venta** es la vista operativa de las sedes.

- **Indicadores clicables (filtran):** total de PDV, Lima y Callao, Provincias, distritos cubiertos, PDV en centros comerciales y PDV por completar (sin código o sin ejecutivo).
- **Tres gráficos clicables:** cobertura por distrito (o por ciudad en Provincias), tipo de ubicación (C.C. vs calle) con el ranking de centros comerciales, y carga por ejecutivo de capacitación.
- **Tabla:**
  - Pestañas Todos · Lima y Callao · Provincias.
  - Filtros: KAM, ejecutivo, ciudad, distrito, ubicación, C.C., marca y categoría.
  - **Agrupar por** marca, distrito, C.C., ejecutivo o ciudad, con grupos que se despliegan.
- **Edición en línea:** vía, dirección, ciudad, distrito, ejecutivo y códigos de comercio como etiquetas editables.
  - Un código no puede estar en dos PDV.
  - Los cambios se sincronizan con Comercial: los PDV del registro y el campo «Código(s) de comercio» de la marca.
- **Nuevos campos por PDV:** *ciudad* y *ejecutivo de capacitación*. Se usan también en el formulario del comercio, en la plantilla Excel y en las exportaciones.
- **Ejecutivos de capacitación:** se administran en Panel Admin. Si se quita uno, sus PDV quedan «sin asignar».

### Modelo de alianzas sin montos ✅

En la gestión de alianzas comerciales no hay montos ni presupuestos. El sistema mide **cantidades**: convenios firmados, negociaciones en curso (contactado, propuesta y negociación), promociones activas (campañas e incentivos vigentes hoy), firmados sin promo, perdidos, acciones vencidas y gestiones inactivas.

- Se eliminó todo monto en soles: valor de las gestiones, pipeline ponderado, presupuesto de campañas, tendencia de presupuesto e inversión por categoría.
- El Dashboard Ejecutivo se rehízo con datos reales:
  - KPIs de alianzas.
  - Avisos de atención con un clic al pipeline.
  - Gestiones por etapa.
  - Promociones activas por categoría.
  - Desempeño por KAM (firmados, en negociación, promos, firmados sin promo, perdidos y tasa de firma).

### CRM · Pipeline en tabla (rediseño) ✅

Referencias: *table view* de HubSpot Deals, *list view* de Pipedrive y vistas guardadas de Salesforce.

- **Indicadores de la selección:** pipeline abierto, ponderado (valor × % de avance), ganadas con win rate, acciones vencidas y gestiones sin actividad por más de 14 días.
- **Barra de pipeline:** cada etapa muestra su conteo y valor, y al hacer clic filtra la tabla.
- **Vistas guardadas con conteo:** Todas · Abiertas · Acción vencida · Esta semana · Sin próxima acción · Sin actividad +14d · Ganadas · Perdidas.
- **Filtros combinables** en una fila: KAM, Etapa, Próxima acción, Promo, Salud y Categoría, más un buscador. Los filtros activos se resaltan.
- **Tabla ordenable:** marca, etapa, KAM, valor, última actividad, próxima acción y salud.
  - La etapa se cambia en la misma fila y el cambio queda en el historial.
  - Las acciones vencidas se marcan en rojo.
  - Una fila con totales al pie.
- «Exportar vista» descarga en CSV exactamente lo que está filtrado. Toda marca de la cartera aparece en el pipeline: si no tiene gestión, entra como Prospecto.

### Modelo de negocio, CRM y experiencia de usuario ✅

**Reglas de negocio aplicadas**

- **CRM = marcas no afiliadas.** Una afiliación avanza de Prospecto a Convenio firmado. Al firmar se abre el registro del comercio. Si se cancela sin guardar, la gestión vuelve a su etapa anterior con una acción pendiente para hoy.
- **Acciones de cartera:** el CRM tiene las pestañas Afiliaciones, Renovaciones y Campañas estacionales. Al cerrar una renovación se registra el nuevo convenio; al cerrar una adenda se crea la campaña.
- **Convenios:** duran como máximo 1 año (lo común es 3 meses). Las campañas estacionales se registran con una adenda simple por su vigencia. No existen promociones permanentes: la promoción del convenio debe estar dentro de su vigencia.
- **Códigos de comercio = POS.** Niubiz tiene 9 dígitos y Mastercard 6. Un código repetido se avisa pero no se bloquea, porque el POS puede haber rotado. Los códigos de la marca se toman de sus PDV.
- **Estado de campañas e incentivos:** se calcula con las fechas (Programada, Activa, Por vencer, Vencida o Cancelada).
- **T&C estándar:** se edita en Panel Admin. Va sin fechas y con campos entre [corchetes]; no se puede crear la campaña con corchetes pendientes. Los incentivos tienen su propia plantilla.
- **Calendario comercial Perú 2026:** 14 temporadas, entre ellas Cyber Wow (abril, julio y noviembre), Cyber Days, Día de la Madre, Día del Padre, Fiestas Patrias, Día del Niño, Halloween, Black Friday y Navidad. Se administran en Panel Admin. El calendario estacional del Dashboard va encima de la línea de tiempo de promociones activas.

**Experiencia de usuario (auditoría con los flujos de punta a punta)**

- **Protección de datos:** al cerrar con datos sin guardar (Cancelar, ✕, clic fuera o Esc) se pide confirmación. El alta de comercio guarda un borrador recuperable. Cambiar de pestaña, de tipo o de comercio no borra lo escrito.
- **Registro del comercio en 4 pasos:** Marca y contacto → Convenio y MSI → Puntos de venta → Revisión, con validación por paso.
- **Errores en el campo:** borde rojo, mensaje debajo y salto al campo, por ejemplo «PDV 4: código 12 no válido».
- **Pipeline:**
  - Motivo obligatorio al cerrar en negativo, con reporte de motivos de pérdida en el Dashboard Ejecutivo.
  - Reabrir una gestión exige justificación.
  - Una gestión cerrada con su registro no retrocede.
  - Toda gestión abierta tiene próxima acción, y las fechas deben ser coherentes.
- **«Registrar acción»:** desde una fila la gestión viene fija; desde la barra superior se elige con un buscador.
- **Avisos:** los cambios de etapa y las cancelaciones se pueden deshacer. Al guardar se vuelve a la pantalla de origen, con acceso directo a la ficha.
- **Campañas e incentivos:** se pueden editar, duplicar y cancelar. Cancelar pide motivo y se puede deshacer.
- **«Mi día»** es la pantalla de inicio del KAM:
  - Acciones vencidas, de hoy y de la semana.
  - Registros pendientes y convenios por vencer.
  - Campañas de la semana.
  - Datos incompletos (convenio sin PDF, adenda sin archivo, PDV sin código y POS repetido). Se calculan en vivo y desaparecen al completarlos.
- **PDV activo/inactivo:** un PDV se desactiva con fecha y motivo en lugar de borrarse. Sale de la cobertura y de las campañas, y el CSV lo incluye (base para homologar con la app de campo).
- **Fecha real:** los datos demo se mueven solos a la fecha de hoy; los datos reales (después de «Empezar desde 0») no se mueven.
- **Persistencia:** los datos se guardan en el navegador (`localStorage`) y los PDF en IndexedDB. Es la capa `store` que se reemplazará en el Sprint 3.

## Sprint 2 — CRM ASPIRIA (solo administradores)

**Objetivo:** que los socios de ASPIRIA gestionen su propio pipeline B2B con cuentas grandes (bancos, fintech, telecom, consumo masivo).

| Historia | Criterio de aceptación |
|---|---|
| Como admin, veo una sección "ASPIRIA · Cuentas" que no existe para los KAM | No aparece en el menú, la ruta `go('aspiria')` está bloqueada para KAM y (desde el Sprint 3) los datos no se envían a ese rol |
| Registro de **cuentas** con razón social, RUC, sector, tamaño, decisores y owner (socio) | CRUD completo con búsqueda y filtro por sector y owner |
| **Oportunidades** por cuenta con servicio (KAM / Medios de pago / Trade), valor mensual y anual, probabilidad y fecha estimada de cierre | Pipeline en **kanban** por etapa: Prospecto → Diagnóstico → Propuesta → Negociación → Ganado / Perdido. Se mueven arrastrando o con una acción |
| Subir **cotizaciones** (PDF / Excel) con versión, monto, fecha y estado (enviada / en revisión / aceptada / rechazada) | El archivo se carga, se previsualiza y se descarga. Se conserva el historial de versiones por oportunidad |
| Registrar **reuniones** con fecha, asistentes, puntos tratados, acuerdos y próximos pasos con responsable y fecha | La ficha de la oportunidad muestra la última reunión arriba. Los próximos pasos entran a una agenda |
| Ver **dónde se quedó cada negociación** | Cada oportunidad muestra etapa, días en la etapa, última interacción, próximo paso y alerta si pasan más de 14 días sin movimiento |
| Dashboard de socios | KPIs de pipeline ponderado, ganado YTD, win rate y ciclo promedio; embudo por etapa (rampa ordinal); forecast por mes; pipeline por socio |

**Técnico:** crear una capa `store` (get / list / save / remove) sobre `localStorage`, para que en el Sprint 3 solo cambie la implementación a backend.

## Sprint 3 — Backend real: autenticación, roles y persistencia

- Firebase Auth (o Supabase) con login por correo corporativo y SSO de Google Workspace.
- Roles `kam`, `admin` y `socio`. Reglas de seguridad del lado del servidor (cada KAM lee solo su cartera; ASPIRIA · Cuentas solo para `admin`/`socio`).
- Firestore/Postgres para todas las colecciones y Storage para convenios, cotizaciones y assets.
- Migrar todos los módulos a la capa `store`. Escapar todo HTML dinámico. Usar UUID como IDs.
- Bitácora de auditoría: quién creó o cambió qué y cuándo.
- **Aislamiento por usuario:**
  - Cada sesión es independiente. Los filtros, el orden, las vistas y los borradores de un usuario se guardan en *su* perfil y nunca se mezclan con los de otro.
  - La visibilidad por rol se aplica en el servidor: un KAM no puede recibir datos de otra cartera aunque manipule el navegador.
  - *Ya implementado en el prototipo:* reinicio del estado de vista al iniciar y cerrar sesión (`resetUserUI`).
  - **Regla de visibilidad:** el Dashboard general muestra el programa completo a todos los roles. En el resto de módulos (Comercial, Puntos de venta, Campañas, CRM · Pipeline, Biblioteca y Dashboard Ejecutivo) cada KAM ve solo su cartera, y solo los administradores ven el consolidado.

## Sprint 4 — Afiliaciones y base de clientes

- Ficha 360 del comercio: datos, contactos, PDV, convenios, campañas, funnel e historial.
- Vista de **Contactos** (hoy no existe) con la salud de la relación y registro de interacciones.
- Importación masiva por CSV o Excel con validación de RUC y deduplicación.
- Estado de afiliación (en trámite / activo / inactivo / suspendido) con fechas.

## Sprint 5 — Campañas en tiempo real

- Estado calculado a partir de las fechas: Programada / Activa / Por vencer (≤7 días) / Vencida. Las vencidas se archivan solas.
- Flujo de aprobación: Borrador → En revisión → Aprobada → Publicada.
- Calendario de campañas real (por mes y por semana) y Gantt filtrable.
- Evidencias de implementación en el PDV (fotos y checklist del material POP).
- Resultados por campaña: transacciones, ticket y conversión, cargados por CSV o API.

## Sprint 6 — Gestión de convenios

- Ciclo de vida: borrador → enviado → firmado → vigente → por renovar → vencido.
- Alertas de renovación a 60, 30 y 15 días. Versionado de adendas.
- Archivo real en Storage con vista previa y control de acceso por cartera.

## Sprint 7 — Dashboards ejecutivos con datos reales

- Reemplazar `PROGRAM_DATA` y los multiplicadores por agregaciones reales.
- Filtros globales (periodo, KAM, marca, categoría) en una sola fila.
- Exportación a PDF con diseño de marca y CSV/Excel de cada tabla.
- Vista de tabla alternativa para cada gráfico (accesibilidad).

## Sprint 8 — IA, notificaciones y producción

- IA Assistant conectado a Claude API sobre los datos del usuario (resúmenes, riesgos, próximas acciones).
- Notificaciones por correo y en la app: vencimientos, tareas y negociaciones estancadas.
- Modo oscuro (los tokens ya están preparados), pruebas E2E, despliegue (Vercel/Netlify) y dominio.
