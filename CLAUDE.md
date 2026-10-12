# CLAUDE.md

## Estado del proyecto

> Última actualización: 2026-10-12. Fuente: el repo (vacío, sin commits previos) y la conversación con Mariano.
> Todo lo que no está confirmado aparece marcado **[SUPOSICIÓN]** o **[FALTA DATO]**.

### Qué es y para qué sirve
- **[SUPOSICIÓN]** Repo para centralizar la conexión de las ~20 líneas de WhatsApp de los distintos negocios de Mariano a la API oficial de WhatsApp (vía Kapso), para atenderlas e integrarlas sin depender de celulares físicos.
- **[FALTA DATO]** Confirmar si `vilela1931` es ese proyecto o un negocio/producto distinto (el nombre no lo aclara y no hay código).

### Stack y despliegue
| Pieza | Estado |
|---|---|
| Código | Ninguno todavía. El repo no tenía commits. |
| WhatsApp | **Confirmado:** WhatsApp Business Platform (Cloud API de Meta) gestionado con **Kapso**. |
| URL de producción | **[FALTA DATO]** |
| Proyecto de Vercel | **[FALTA DATO]** |
| Proyecto de Supabase | **[FALTA DATO]** |

Números en Kapso (consultados el 2026-10-11):
| Número | Nombre | Modo | Estado |
|---|---|---|---|
| +54 9 11 7260-6650 | Comercial Garden House | Coexistencia (app + API) | Conectado, calidad GREEN, límite TIER_2K, WABA aprobada |
| (sin número) | Sandbox WhatsApp | Sandbox | Solo pruebas |

### Decisiones tomadas y por qué
1. **No comprar un SIM bank ni un gateway GSM.** WhatsApp Cloud API solo necesita el chip para recibir el código de verificación una vez. Un gateway solo hace falta para SMS o voz, y además las operadoras bloquean los SIM box.
2. **No usar WhatsApp "no oficial"** (Evolution API, Baileys). Con 20 números el riesgo de ban es alto.
3. **Recomendación (pendiente de que Mariano la confirme número por número):** modo **solo API** para la mayoría de las líneas, atendidas desde el inbox de Kapso. **Coexistencia** solo donde alguien atiende hoy desde el celular, porque obliga a abrir la app al menos cada 14 días.
4. **Recomendación:** una cuenta de empresa de Meta (Business Portfolio) verificada por cada razón social o CUIT. La verificación sube el límite de números.

### Estado actual y próximo paso
- **Estado:** 1 de ~20 números migrado (Garden House, en coexistencia). Falta definir las otras ~19.
- **Próximo paso:** armar la tabla de las 20 líneas con número, negocio, modo (coexistencia o solo API) y responsable. Después la migración la ejecutan Nacho o Euge con una checklist: verificar el número, activar el PIN, cargar el nombre visible y probar enviando y recibiendo.

### Turno: de quién es la pelota
**De Mariano.** Tiene que pasar la lista de las ~20 líneas (número y negocio) y decidir el modo de cada una. Hasta que lo haga, Nacho y Euge no pueden arrancar.

### Pendientes técnicos conocidos
- [ ] **Garden House sin PIN de verificación en dos pasos** (`is_pin_enabled: false`): cualquiera que tenga el chip puede volver a registrar el número. Activarlo ya.
- [ ] **Garden House con nombre visible sin aprobar** (`name_status: NON_EXISTS`): cargarlo y mandarlo a aprobación.
- [ ] Mantener activos los chips prepagos (recarga o plan mínimo). Si la línea se da de baja, la operadora recicla el número.
- [ ] Verificar el Business Portfolio de cada razón social para no chocar con el límite de números.
- [ ] Definir las plantillas de mensajes salientes, que son las que cobra Meta.
- [ ] **[FALTA DATO]** Si va a haber código propio en este repo (webhooks, CRM, Supabase), definir el stack y el despliegue y completar esta sección.
