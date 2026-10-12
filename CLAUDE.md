# Portal Vilela 1931

> **Fuente de este archivo:** `CONTEXTO.md` que Mariano subió en la conversación del 2026-10-11/12.
> Este repo todavía **no tiene código**: el código vive en la Mac de Mariano (git local, sin remoto).
> Nada de lo de abajo está verificado contra código. Lo marcado **(suposición)** no figura en el CONTEXTO.md.

## Estado del proyecto

### Qué es y para qué sirve
Portal web de vecinos del Consorcio de Propietarios Vilela 1931 (CABA, 17 UF, sin encargado).
Sirve para ver liquidaciones de expensas, deuda por UF, informar pagos, chatear, votar y controlar a la administración (Adm. Sanjuan).

### Stack y despliegue
- Next.js 15.5.27 (App Router, Server Actions), React 19, `@supabase/ssr`, chart.js / react-chartjs-2. CSS propio en `src/app/globals.css`, sin Tailwind.
- **Producción:** https://vilela-1931.vercel.app. Es pública; la app tiene su propio login. Vercel Authentication solo protege las previews.
- **Vercel:** team `agostomariano-9165s-projects`, proyecto `vilela-1931` (`prj_3CaFfVPVHWi1VWaNNBy9drnJkoiV`).
- **Supabase:** proyecto `vilela-1931`, ref `cnxheflolqscvpvpzlkg`, org dockgroup, región sa-east-1, US$10/mes.
- **Código:** `~/Library/Mobile Documents/com~apple~CloudDocs/vilela-1931/app` (iCloud, en la Mac de Mariano). Migraciones en `supabase/migrations/0001…0005`.
- **Deploy:** con la Vercel CLI desde la carpeta vinculada: `npx vercel@latest deploy --prod --yes --scope agostomariano-9165s-projects`.

### Decisiones tomadas y por qué
| Decisión | Por qué |
|---|---|
| Primero un prototipo estático y después Next.js + Supabase + Vercel | Validar pantallas antes de construir **(suposición)** |
| Un usuario por vecino, con email y contraseña, dado de alta por el admin (mínimo 8 caracteres al cambiar la clave) | Controlar quién entra; consorcio chico **(suposición)** |
| Votos con doble cómputo (por unidades y por porcentual) | Criterio habitual de las asambleas de propiedad horizontal **(suposición)** |
| El vecino informa el pago con comprobante y el admin lo confirma o rechaza | Ningún pago cuenta sin validación |
| RLS: todo vecino activo lee todo el consorcio y escribe solo lo suyo; el consejo crea votaciones; el admin carga datos y configura | Transparencia entre vecinos **(suposición)** |
| Deploy por la CLI, no por el conector MCP de Vercel | El MCP devuelve 403 al crear o modificar proyectos |
| Compilar fuera de iCloud (rsync a una carpeta temporal sin `node_modules`/`.next`) | iCloud interfiere con `node_modules` y `.next` **(suposición)** |
| "Consultar saldo" de servicios copia la cuenta y abre el sitio del proveedor | No hay API de saldo |

### Estado actual y próximo paso
- **Estado:** desplegado en producción. 15 liquidaciones reales cargadas (2023-10 y de 2025-07 a 2026-09; falta 2026-05) y cuadran al centavo. Los advisors de seguridad de Supabase no muestran alertas. **Todavía no se probó ninguna pantalla con un usuario logueado.**
- **Próximo paso:** Mariano carga `SUPABASE_SERVICE_ROLE_KEY` en Vercel y crea su usuario admin:
  - `npx vercel@latest env add SUPABASE_SERVICE_ROLE_KEY production`
  - `SUPABASE_SERVICE_ROLE_KEY=… npm run crear-admin -- <email> "Mariano" 06 "<clave>"`
  - Claude **no** crea cuentas, no pone contraseñas y no manipula la service role key.

### Turno: de quién es la pelota
**Mariano.** Todo está frenado hasta que cargue la service role key y cree su usuario admin. Después tiene que definir el consejo y dar de alta a los vecinos.
Euge, Nacho y los clientes no participan en este proyecto **(suposición)**.

### Pendientes técnicos conocidos
1. Cargar `SUPABASE_SERVICE_ROLE_KEY` en Vercel y crear el admin (Mariano).
2. Volver a desplegar después de cargar la clave: la pantalla de alta de vecinos la necesita.
3. Revisar todas las pantallas con sesión iniciada.
4. Definir el consejo y dar de alta a los vecinos.
5. Subir el código a este repo (`agostomariano-cmd/vilela1931`) y conectarlo a Vercel. Hoy depende de la Mac de Mariano.
6. Cargar la liquidación de 2026-05 y las viejas (2022–2023, administraciones anteriores).
7. Opcionales: subir el PDF de la liquidación desde la app; avisos por mail o WhatsApp (hay una línea de Kapso).

> **Repo público:** no commitear datos bancarios, números de cuenta de servicios ni los hallazgos sobre la administración (nombres y montos). Esos datos quedan en el `CONTEXTO.md` local.

---

## Referencia técnica

### Base de datos
- `unidades` (17, con su porcentual). `perfiles`: 1 usuario → 1 UF, con flags `es_consejo`, `es_admin` y `activo`; un trigger lo crea desde `auth.users` (`raw_user_meta_data.nombre/unidad_id`).
- `liquidaciones`, `liquidacion_unidades`, `gastos` (categorías SP/AB/MA/RU/LI/AD/SI/BA/SE/OT).
- `pagos_informados` + bucket privado `comprobantes/<UF>/…`.
- `conversaciones`, `mensajes` (chat en realtime).
- `votaciones`, `votos`: PK votación+UF, un voto por UF, se puede cambiar hasta el cierre; un trigger valida la opción y el cierre.
- `servicios` (electricidad, gas, agua, ABL), `avisos` (realtime), `administraciones`, `eventos`.
- Vistas: `unidades_con_deuda`, `resultado_votaciones`, `indicadores`, `posibles_duplicados`, `proveedores_obras`, `servicios_pagos`.
- Helpers RLS: `private.es_vecino`, `private.es_admin`, `private.es_consejo`, `private.mi_unidad` (security definer).

### Secciones de la app
Mi unidad · UF con deuda + Informar pago · Liquidación de expensas (mes a mes + Análisis anual) · Gastos del edificio · Servicios públicos · Indicadores (10) · Chat · Votaciones (rueda por UF, doble cómputo) · Configuración (solo admin) · Botón rojo **Aviso general**.

### Convenciones
- Fuentes Archivo / Public Sans / IBM Plex Mono. Tokens de color claro/oscuro en `globals.css`.
- `.vercel/` no se versiona.
