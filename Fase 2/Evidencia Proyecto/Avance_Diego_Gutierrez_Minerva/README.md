# Ficha de avance – Fase 2: contribución de Diego Gutiérrez

**Proyecto:** Minerva E-Commerce (Grupo 8, CAPSTONE_003D)
**Integrante:** Diego Gutiérrez (GitHub: `Gutixd`)
**Repositorio del código (equipo):** https://github.com/minerva-capstone-team/minerva-capstone
**Período cubierto:** 30 de septiembre al 7 de octubre de 2026
**Estado del documento:** redactado con asistencia de IA (ver sección 6); el autor debe revisarlo antes de entregarlo.

---

## 1. Resumen
Sobre la base de la tienda ya construida por el equipo (catálogo, carrito, checkout, panel de administración, correos con Resend y chatbot con Gemini), se agregaron las funcionalidades que se detallan abajo: seguimiento de pedidos, stock confiable, cupones, envío por región, boleta/factura, avisos de estado, notificaciones al administrador, política de privacidad y un bot que responde mensajes directos de Instagram.

## 2. Alcance de esta contribución (PRs #5 a #9)
Todos fueron integrados a `main` mediante Pull Request.

| PR | Fusionado | Qué entrega | Tamaño |
|---|---|---|---|
| [#5](https://github.com/minerva-capstone-team/minerva-capstone/pull/5) | 30-09-2026 | Página pública de **seguimiento de pedidos** con código de Paket; **panel de administración instalable como app** (PWA) | +551 / −6, 18 archivos |
| [#6](https://github.com/minerva-capstone-team/minerva-capstone/pull/6) | 30-09-2026 | **Stock confiable**, **cupones**, **tarifa de envío por región** y envío gratis, **boleta/factura**, **avisos de estado** al cliente, **notificaciones push** al administrador | +2.235 / −60, 29 archivos |
| [#7](https://github.com/minerva-capstone-team/minerva-capstone/pull/7) | 07-10-2026 | **Bot de Instagram** que responde mensajes directos (webhook firmado, reutiliza el Gemini de la tienda) | +230 / −6, 5 archivos |
| [#8](https://github.com/minerva-capstone-team/minerva-capstone/pull/8) | 07-10-2026 | **Política de privacidad** (`/privacidad`) con instrucciones para eliminar datos | +231 / −2, 4 archivos |
| [#9](https://github.com/minerva-capstone-team/minerva-capstone/pull/9) | 07-10-2026 | **Corrección de tiempos de espera** del bot para que siempre responda a tiempo | +53 / −23, 3 archivos |

### Trabajo previo de otros integrantes (no incluido en esta contribución)
Los PRs [#1](https://github.com/minerva-capstone-team/minerva-capstone/pull/1) a [#4](https://github.com/minerva-capstone-team/minerva-capstone/pull/4) son de **Diego Román** (`DiegoRoman-code`): correos de pedido con Resend, chatbot de ayuda con Gemini y ajustes. Esta contribución se apoya en esa base, pero no la reclama.

## 3. Detalle de lo construido

**Seguimiento de pedidos (PR #5).** La clienta ingresa número de pedido y el email de la compra y ve una línea de tiempo (recibido → entregado). Cuando el despacho está "Enviado" aparece el código de Paket con un botón a `track.paket.cl`. Se exige el email porque los números de pedido son correlativos; la función de base de datos solo devuelve estado y productos, sin dirección ni teléfono.

**Panel de administración como app (PR #5).** Manifiesto, íconos y service worker acotados a `/admin`, botón "Instalar app" y barra de pestañas inferior para el teléfono.

**Stock confiable (PR #6).** Las reservas de pedidos sin pago vencen a las 48 horas y devuelven el stock; cancelar un pedido repone stock y cupón; las variantes descuentan su propio stock; tope de pedidos pendientes por email.

**Cupones y envío (PR #6).** Cupones de porcentaje o monto fijo con compra mínima, usos máximos y vencimiento, validados y canjeados en el servidor. Tarifa de envío editable por región y envío gratis desde un monto.

**Boleta o factura (PR #6).** Elección en el checkout; para factura se pide RUT (validado con dígito verificador en navegador y base de datos), razón social, giro y dirección. El administrador registra número y enlace del documento emitido por el proveedor autorizado por el SII (la emisión ante el SII no se realiza desde el sitio).

**Avisos y notificaciones (PR #6).** Correo al cliente al cambiar el estado (requiere dominio verificado en Resend); alternativa por WhatsApp con el mensaje ya escrito; notificaciones push al administrador por cada pedido nuevo.

**Bot de Instagram (PR #7 y #9).** Recibe el webhook de Meta, verifica la firma, responde de inmediato y procesa después; ignora ecos, duplicados y adjuntos; limita 20 respuestas por hora por persona; si la IA tarda o falla, responde un mensaje de respaldo con el WhatsApp de la tienda.

**Política de privacidad (PR #8).** Página pública redactada según los datos que el sitio realmente recoge, con la sección "Cómo eliminar tus datos".

## 4. Evidencia

### 4.1 Capturas de pantalla
> **Importante:** las capturas corresponden a un **entorno local de pruebas con datos ficticios** (cliente "Ana Pérez", `ana@test.cl`), generado con un servidor de base de datos simulado. No son de producción ni muestran datos reales.

| Archivo | Muestra |
|---|---|
| `capturas/01_checkout_cupon_envio_factura.png` | Checkout con cupón aplicado, descuento, envío, total y datos de factura |
| `capturas/02_seguimiento_pedido_paket.png` | Seguimiento del pedido con línea de tiempo y código de Paket |
| `capturas/03_admin_pedido_detalle.png` | Detalle del pedido en el admin: desglose, factura, código de Paket y botones de aviso |
| `capturas/04_admin_cupones.png` | Gestión de cupones |
| `capturas/05_admin_envios.png` | Tarifas de envío por región, envío gratis y reserva de stock |
| `capturas/06_admin_movil_barra_inferior.png` | Panel admin en teléfono con barra inferior |
| `capturas/07_politica_de_privacidad.png` | Política de privacidad |
| `capturas/08_carrito_envio_gratis_progreso.png` | Carrito con progreso hacia envío gratis |

### 4.2 Pruebas automatizadas
| Archivo | Resultado |
|---|---|
| `pruebas/pruebas_base_de_datos.txt` | **64 de 64** pruebas pasan: migraciones 0001–0005 aplicadas en un PostgreSQL local (stock de producto y variante, cancelar/reabrir, cupones, envío gratis, RUT, factura, reservas vencidas, permisos, avisos de un solo uso) |
| `pruebas/pruebas_bot_instagram.txt` | **21 de 21** pruebas pasan: webhook con servidores simulados de Meta y Gemini (firma, duplicados, memoria, ecos, adjuntos, fallos y tiempos de espera) |

Además, cada PR pasó `tsc --noEmit` y `next build` sin errores, y `npm audit` sin vulnerabilidades.

## 5. Estado y limitaciones (a la fecha de esta ficha)
- Las migraciones 0004 y 0005 fueron ejecutadas en Supabase y las variables de entorno cargadas en Vercel.
- **Bot de Instagram:** el webhook queda verificado y recibe correctamente las pruebas enviadas por Meta, pero la entrega de **mensajes reales** desde Instagram aún no está resuelta (en revisión: roles de la aplicación y permisos de mensajes). El acceso avanzado a mensajes de Meta, que permite responder a cualquier persona, aún no se solicita.
- **Correos a clientes:** requieren un dominio verificado en Resend; mientras tanto solo se envía el aviso interno y se ofrece el aviso por WhatsApp.
- **Pago en línea (Webpay / Mercado Pago):** pendiente; hoy el pago se coordina por WhatsApp.
- **No verificado aún:** activación de notificaciones push en un teléfono real y emisión de boletas/facturas con un proveedor.

## 6. Herramientas y apoyo utilizado (declaración de uso de IA)
- **Asistente de programación con IA:** *Claude Code* (Anthropic). Se utilizó para escribir el código, las pruebas automatizadas, las migraciones de base de datos y la documentación de las funcionalidades de las secciones 2 y 3 (incluido este documento), a partir de las instrucciones dadas por Diego Gutiérrez durante sesiones de trabajo.
- **Rol de Diego Gutiérrez:** definió los requerimientos y prioridades, tomó las decisiones de producto y de negocio, aprobó cada cambio antes de integrarlo, ejecutó las migraciones en Supabase, configuró los servicios externos (Vercel, Resend, Meta/Instagram) y realizó las pruebas funcionales en esos entornos.
- **Trazabilidad:** los commits y descripciones de PR incluyen la mención de la herramienta (`Co-Authored-By` y "Generated with Claude Code").
- **Otras herramientas:** Next.js, React, Tailwind CSS, Supabase, Vercel, Resend, Gemini (Google) y la API de Instagram de Meta.

> La cantidad de código escrito con asistencia de IA es alta; esta ficha no pretende presentarla como programación manual. Si la pauta del curso exige otro formato de declaración, este apartado puede ajustarse.
