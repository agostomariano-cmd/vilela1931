# Portal Vilela 1931 / Consorcios Online

> **Última actualización:** 2026-10-12.
> **Fuentes:** (1) el `CONTEXTO.md` que Mariano subió en otra conversación, volcado en la rama `claude/festive-ride-2kpoul`; (2) lo que verifiqué en vivo contra Supabase y Vercel el 2026-10-12, marcado **[VERIFICADO]**.
> Este repo **no tiene código**: el código vive en la Mac de Mariano (git local, sin remoto). Nada está verificado contra código.
> Lo marcado **(suposición)** no figura en ninguna de las dos fuentes.

## Estado del proyecto

### Qué es y para qué sirve
Empezó como portal de vecinos del Consorcio Vilela 1931 (CABA, 17 UF, sin encargado): liquidaciones de expensas, deuda por UF, pagos informados, chat, votaciones y control de la administración.
**[VERIFICADO]** Hoy la base y el proyecto de Vercel son **multi-consorcio con suscripción paga ("Consorcios Online")**. Vilela es el único consorcio cargado y está en plan `consorcio`, en modo `cortesia`.

### Stack y despliegue
- Next.js 15.5 (App Router, Server Actions), React 19, `@supabase/ssr`, chart.js. CSS propio, sin Tailwind. (Según el CONTEXTO.md; no está verificado contra código.)
- **Vercel [VERIFICADO]:** team `agostomariano-9165s-projects`, proyecto **`consorcios-online`** (antes `vilela-1931`), id `prj_3CaFfVPVHWi1VWaNNBy9drnJkoiV`, Node 24.
  - Dominios: `consorciosonline.com.ar`, `www.consorciosonline.com.ar`, `consorcios-online.vercel.app` y `vilela-1931.vercel.app`.
  - Las previews están protegidas por SSO; producción es pública y la app tiene su propio login.
  - El 2026-10-12 había un deploy en `INITIALIZING`. Desde este entorno no pude abrir la URL porque el proxy bloquea el acceso.
- **Supabase [VERIFICADO]:** proyecto `vilela-1931`, ref `cnxheflolqscvpvpzlkg`, sa-east-1, Postgres 17, `ACTIVE_HEALTHY`, creado el 2026-10-11.
- **Código:** carpeta `vilela-1931/app` en iCloud, en la Mac de Mariano.
- **Deploy:** con la Vercel CLI desde esa carpeta: `npx vercel@latest deploy --prod --yes --scope agostomariano-9165s-projects`.

### Decisiones tomadas y por qué
| Decisión | Por qué |
|---|---|
| Next.js + Supabase + Vercel, después de un prototipo estático | Validar pantallas antes de construir **(suposición)** |
| **[VERIFICADO]** Pasar de un solo edificio a multi-consorcio con planes, suscripción por Mercado Pago (`mp_preapproval_id`), cupones y referidos | Convertir el portal en producto vendible **(suposición)** |
| Un usuario por vecino, dado de alta por el admin | Controlar quién entra **(suposición)** |
| Votos con doble cómputo (por unidades y por porcentual) | Criterio de las asambleas de propiedad horizontal **(suposición)** |
| El vecino informa el pago con comprobante y el admin lo valida | Ningún pago cuenta sin validación |
| Deploy por la CLI, no por el conector MCP de Vercel | **[VERIFICADO]** El MCP también da 403 al leer las variables de entorno |
| Compilar fuera de iCloud | iCloud interfiere con `node_modules` y `.next` **(suposición)** |

### Estado actual y próximo paso [VERIFICADO en la base, 2026-10-12]
- 1 consorcio, 17 unidades, 15 liquidaciones, 19 migraciones (la última es `instagram_ritmo_y_datos_por_usuario`, del 2026-10-12).
- **El admin ya está creado:** 1 usuario en `auth.users` y 1 miembro activo que es admin y consejo. El paso que el CONTEXTO.md daba como bloqueante ya está hecho.
- **No hay vecinos dados de alta:** 1 miembro para 17 unidades.
- El esquema cambió respecto del CONTEXTO.md: los permisos ya no viven en `perfiles`, ahora están en `miembros` (`es_admin`, `es_consejo`, `activo`, con `consorcio_id`). Además hay tablas nuevas: `consorcios`, `membresias`, `plataforma_admins` (2), `cupones`, `referidos`, `publicaciones` (19, Instagram), `diagnosticos`, `ingresos_mail`, `secretos` y `accesos`.
- **Próximo paso:** probar las pantallas con sesión iniciada y dar de alta a los vecinos de Vilela.

### Turno: de quién es la pelota
**Mariano.** Tiene que probar el portal logueado, definir el consejo y cargar los vecinos. Además tiene que decidir si Consorcios Online se vende o sigue como herramienta propia. Euge, Nacho y los clientes no figuran en este proyecto **(suposición)**.

### Pendientes técnicos conocidos
1. **Subir el código a este repo y conectarlo a Vercel.** Hoy todo depende de una carpeta de iCloud en una sola Mac: si se pierde, se pierde el producto.
2. Revisar todas las pantallas con sesión iniciada (no consta que se haya hecho).
3. Dar de alta a los vecinos (16 unidades sin miembro).
4. Cargar la liquidación de 2026-05 y las viejas (2022–2023).
5. **(suposición)** Confirmar que el `SUPABASE_SERVICE_ROLE_KEY` esté cargado en Vercel. No lo pude verificar porque el MCP da 403.
6. Actualizar la "Referencia técnica" de abajo contra el código real: está desactualizada.
7. Opcionales: subir el PDF de la liquidación desde la app; avisos por mail o WhatsApp (hay una línea en Kapso).

> **Repo público:** no commitear datos bancarios, cuentas de servicios, datos de vecinos ni hallazgos sobre la administración.

---

## Referencia técnica (del CONTEXTO.md; puede estar desactualizada)

### Base de datos
- `unidades` (17, con porcentual). Los roles ahora están en `miembros` (ver arriba); `perfiles` queda con id, nombre y email.
- `liquidaciones`, `liquidacion_unidades`, `gastos` (categorías SP/AB/MA/RU/LI/AD/SI/BA/SE/OT).
- `pagos_informados` + bucket privado `comprobantes/<UF>/…`.
- `conversaciones`, `mensajes` (chat en realtime).
- `votaciones`, `votos`: un voto por UF, se puede cambiar hasta el cierre.
- `servicios`, `avisos` (realtime), `administraciones`, `eventos`.
- Vistas: `unidades_con_deuda`, `resultado_votaciones`, `indicadores`, `posibles_duplicados`, `proveedores_obras`, `servicios_pagos`.

### Secciones de la app
Mi unidad · UF con deuda + Informar pago · Liquidación de expensas (mes a mes + análisis anual) · Gastos · Servicios públicos · Indicadores · Chat · Votaciones · Configuración (solo admin) · Aviso general.

### Convenciones
- Fuentes Archivo / Public Sans / IBM Plex Mono. Tokens de color claro/oscuro en `globals.css`.
- `.vercel/` no se versiona.
