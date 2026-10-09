# Proyecto 01 — Guía de WhatsApp: primeros pasos y glosario
**Meridian World — Consultorio**
*Ubicación: desde el 9 oct 2026 vive en `01_Meridian/Guia Whatsapp/` porque la usan por igual el [[ToDo Gestor de citas]] y el [[Webhook ToDo]]. Las capturas están en la subcarpeta `capturas-whatsapp/`.*
*Documento nuevo — 5 oct 2026. Pensado para quien hace esto por primera vez. Se basa en la documentación oficial de Meta for Developers; los tiempos de revisión de plantillas no tienen cifra oficial.*

---

## 1. La idea en una analogía

WhatsApp funciona como un servicio de correo que opera Meta. Para mandar cartas oficiales a nombre de un negocio necesitas: un **apartado postal registrado al negocio** (la cuenta de WhatsApp Business y su número), **formatos de carta aprobados** para escribirle primero a alguien (las plantillas) y una **ruta de mensajería hacia tu software** (la API y el webhook).

## 2. Glosario

| Término | Qué es, en palabras simples |
|---|---|
| **Meta Business Portfolio** (antes "Business Manager") | La "carpeta de la empresa" dentro de Meta. Agrupa las cuentas, números y apps de un negocio. Cada consultorio tendrá la suya. |
| **Verificación de negocio** | Meta comprueba con documentos legales que el negocio existe. Es una de las rutas para subir los límites iniciales de envío. |
| **WABA** (WhatsApp Business Account) | La cuenta "madre" de WhatsApp Business: contiene los números y las plantillas. Vive dentro de un Business Portfolio. |
| **Número / Phone Number ID** | El teléfono registrado dentro de la WABA. El *Phone Number ID* es el código interno que tu programa usa para decir "envía desde este número". |
| **Cloud API** | La forma en que tu software le habla a WhatsApp. Meta la aloja; tú solo mandas peticiones por internet. |
| **App de Meta** | El registro de tu software ante Meta. Ahí viven las credenciales y los permisos. No es una app de celular. |
| **Token de acceso** | La contraseña de tu software ante la API. El *temporal* dura unas horas (sirve para probar); el *permanente* se crea con un **System User** (usuario de sistema) en el Business Portfolio. |
| **Webhook** | Una dirección de tu servidor a la que Meta avisa cuando pasa algo: llegó un mensaje, se entregó, se leyó. Es el timbre de tu casa: tú no estás revisando la puerta, el timbre te avisa. |
| **Plantilla (template)** | Mensaje con texto fijo y huecos variables ({{1}}, {{2}}), aprobado por Meta antes de usarse. Es obligatoria para escribirle primero a una persona o fuera de la ventana de 24 horas. |
| **Categorías de plantilla** | *Marketing* (promocional), *Utility* (confirmaciones y avisos que el usuario espera) y *Authentication* (códigos de un solo uso). Las nuestras son Utility. Si Meta cree que una Utility suena a promoción, la recategoriza a Marketing (se cobra más). |
| **Ventana de servicio de 24 horas** | Cuando el paciente te escribe, se abre una ventana de 24 horas en la que puedes contestarle con mensajes libres, sin plantilla. |
| **Nombre visible (display name)** | El nombre que ven los pacientes. Meta lo revisa (aprobado, pendiente, rechazado). |
| **Calidad y límite de mensajes** | Meta califica la calidad de tu número. Los límites de envío arrancan en 250 destinatarios únicos por 24 horas y suben con buena calidad. |
| **Coexistence** | Permite que el mismo número funcione a la vez en la app WhatsApp Business del celular y en la API. Se sincronizan los últimos 6 meses de chats. |
| **Embedded Signup** | La ventana de Meta donde el dueño de un número conecta su cuenta a tu app. Para Coexistence, el dueño escanea un código QR desde su app. |
| **Tech Provider / Solution Partner / BSP** | Quienes tienen autorización de Meta para conectar negocios a la API. Ver sección 1.4 bis de [[proyecto-01-costos-y-cotizacion]]. |

## 3. Qué hay que hacer, por fases

### Fase 0 — Preparar tu terreno (tú sola, sin cliente)
1. Una cuenta de Facebook/Meta y el registro como desarrolladora en developers.facebook.com.
2. Crear el **Business Portfolio de Meridian World**.
3. Crear una **App de Meta** con el caso de uso "Connect with customers through WhatsApp". Se crea una WABA de prueba con un **número de prueba de Meta**.
4. Enviar un mensaje de prueba con el token temporal.
5. Crear el token permanente (System User con los permisos `business_management`, `whatsapp_business_messaging` y `whatsapp_business_management`).
6. Montar el **webhook** en el backend, para recibir estados de entrega y mensajes entrantes.

### Fase 1 — Tu número dedicado
Requisitos del número según Meta:
- No puede estar registrado en WhatsApp ni en WhatsApp Business. Si lo está, hay que eliminar esa cuenta primero.
- Debe poder recibir un código por SMS o llamada de voz. El móvil es lo recomendado; un fijo solo sirve para llamada.
- Hay que elegir un nombre visible y esperar su aprobación.
- Una vez registrado en la Cloud API, ya no se usa en la app normal de WhatsApp, salvo con Coexistence.

### Fase 2 — Plantillas
- Redactar los ~12 mensajes del catálogo, con ejemplos para cada variable, sin tono promocional.
- Enviarlos a revisión desde el WhatsApp Manager o por API. No hay cifra oficial de tiempo de revisión: conviene enviarlos pronto.
- **Las plantillas pertenecen a cada WABA.** Las creadas en tu cuenta de pruebas no pasan solas a la de la clienta: cada consultorio necesitará su propio juego. Conviene un script que las cree por API para cada cliente nuevo.

### Fase 3 — Producción con la clienta
- La clienta tiene su propio Business Portfolio, verificado como su negocio.
- Se conecta su número con **Embedded Signup** y Coexistence, a través del BSP o Tech Provider elegido. Requiere la app WhatsApp Business versión 2.24.17 o superior.
- Mantén la app abierta: si el celular principal pasa unos 14 días inactivo, Meta desconecta el número.
- Antes de esta fase hay que elegir proveedor (ver 1.4 ter de [[proyecto-01-costos-y-cotizacion]]).

## 4. Decisión (5 oct 2026): Meridian World será el "cliente de práctica"
Nydia usará su startup, con su número dedicado, como si fuera un negocio cliente: su propio Business Portfolio, su propia WABA, sus plantillas y su número. Así se ensaya todo el proceso antes de tocar la cuenta de la clienta. Esto resuelve la duda sobre el número dedicado: es de práctica y desarrollo, no el de producción de la clienta, que sigue siendo el de la administradora con Coexistence.

## 5. Plan de práctica con Meridian

**Qué buscamos aprender y medir:**
1. La cadena completa, de la cuenta de desarrolladora al mensaje entregado.
2. Cuánto tarda cada paso (verificación, aprobación del nombre visible, aprobación de plantillas), para darle plazos reales a la clienta.
3. El costo real por mensaje, leído del panel de facturación de Meta. Esto ayuda a cerrar el pendiente de la tarifa formal.
4. Un script que cree las plantillas por API en cualquier WABA. Meridian sería el primer "tenant" de la plataforma multi-cliente.
5. Una lista de verificación de onboarding para cada consultorio nuevo.

**Pasos:**
1. **Datos del negocio:** nombre legal, domicilio, teléfono, sitio web con HTTPS y correo del dominio. Deben coincidir con la entidad legal de Meridian.
2. **Business Portfolio de Meridian**, con verificación en dos pasos en la cuenta personal de Facebook que lo administra.
3. **App de Meta, WABA de prueba y número de prueba:** primer mensaje a tu propio celular.
4. **Registrar el número dedicado** como número normal de la Cloud API (sin app), con nombre visible "Meridian World". Como el número ya tiene una cuenta de WhatsApp recién creada, primero hay que **eliminarla** desde la app (Ajustes > Cuenta > Eliminar cuenta). Hazlo justo antes de registrar el número en el WhatsApp Manager. Si Meta lo rechaza de inmediato, espera un poco y reintenta (esto último es experiencia común, no está documentado).
5. **Webhook** en el backend para recibir estados y mensajes.
6. **Plantillas:** las mismas estructuras del catálogo (variables, categoría Utility), con textos que tengan sentido para Meridian. Cada plantilla debe tener sentido para el negocio que la envía.
7. **Script de plantillas por API.**
8. **Verificación de negocio:** se pospone hasta que Meridian World se dé de alta ante el SAT (decidido el 5 oct 2026: se hará más adelante). Es opcional para empezar; ver abajo.
9. **Después:** probar Coexistence con la prueba gratuita del proveedor elegido. Requiere un número dentro de la app WhatsApp Business, así que no puede ser el mismo número que ya está en la API.

**Sobre la verificación de negocio:** según una guía de terceros (no oficial), no es obligatoria para empezar. Sin ella, el límite es de 250 destinatarios únicos por 24 horas y 2 números por cuenta; con ella suben a 2,000 y 20. Meta no publica una lista de documentos por país; pide los datos del negocio y, si no los encuentra en un registro público, documentos como constitución, comprobante fiscal o extracto bancario, nunca autoemitidos. Puede tardar hasta 14 días hábiles. Un sitio web que no cargue con HTTPS es causa común de rechazo.

**Por qué el alta ante el SAT importa más adelante:** para ser **Tech Provider** (conectar clientes directo con Meta, sin intermediario) la verificación de negocio es obligatoria, y después hay que pasar el App Review con dos videos de demostración (uno de mensajería y otro de creación de plantillas). Tu script de plantillas de la práctica sirve para ese video. Mientras Meridian no esté verificada, el camino al lanzamiento es un proveedor (BSP) que ya sea Tech Provider o Solution Partner. Aparte, para cobrarle a la clienta con factura necesitarás el alta; consúltalo con un contador, porque no soy asesor fiscal.

**Precauciones:** el portfolio queda ligado a una cuenta personal de Facebook (activa la verificación en dos pasos). **Decisión del 5 oct 2026:** Nydia usará su cuenta personal vieja, que no usa para nada social, solo como llave de acceso; no se crea una cuenta nueva porque Facebook permite una sola cuenta personal por persona y duplicarla pone en riesgo el acceso al portfolio. Más adelante conviene agregar un segundo administrador al portfolio como respaldo. Envía solo a tus propios números mientras practiques. Para mensajes de negocio reales, Meta pedirá un método de pago.

## 6. Fase 0 paso a paso, con capturas (5 oct 2026)

Esta sección documenta lo que Nydia hizo realmente en pantalla, en orden. Las capturas están en `capturas-whatsapp/`. Si Meta cambia la interfaz, las capturas muestran cómo se veía hoy.

> **Cuenta usada:** la cuenta personal vieja de Facebook de Nydia, con verificación en dos pasos ya activada. **Dirección:** developers.facebook.com.

### Paso 6.1 — Entrar a "Mis apps" y pulsar "Crear app"
Al ser la primera vez, la pantalla dice "Aún no hay apps". Pulsa el botón verde **Crear app** (arriba a la derecha, o el del centro). El filtro "Portfolio comercial" aparece vacío porque todavía no existe ninguno.

![[fase0-01-apps-vacio.png]]

### Paso 6.2 — Detalles de la app
El asistente tiene **5 etapas**: Detalles de la app → Casos de uso → Negocio → Requisitos → Resumen.

- **Nombre de la app:** `Meridian Mensajeria` (19 de 30 caracteres). Es solo un nombre interno que se ve en "Mis apps"; se puede cambiar después en la configuración. *Mejora pendiente:* si quieres acentos o un nombre más claro (por ejemplo "Meridian Mensajería"), se puede corregir más tarde sin perder nada.
- **Correo de contacto de la app:** `nydia.hernandez@meridianworld.io`. Meta lo usa para avisos de políticas, restricciones o recuperación de la app. Debe ser un correo que revises con frecuencia.

![[fase0-02-detalles-app.png]]

### Paso 6.3 — Casos de uso
Se eligió **"Conectarte con los clientes a través de WhatsApp"** (aparece en la lista "Destacados"). Dice que "se requiere un portfolio comercial", por eso la etapa siguiente lo pide. El contador abajo marca "1 caso de uso agregado".

Aviso de Meta: algunos casos de uso no se pueden combinar en la misma app; si algún día necesitas otro (por ejemplo, anuncios), se crea otra app.

![[fase0-03-casos-de-uso.png]]

### Paso 6.4 — Negocio: no había portfolio
Meta pregunta "¿Qué portfolio comercial quieres conectar a esta app?" y avisa **"No hay negocios disponibles"**, porque la cuenta nunca había creado uno. El botón Siguiente queda deshabilitado hasta tener uno. Se pulsó el enlace **"crea uno nuevo"**.

Dato útil del texto de Meta: se puede conectar un portfolio **no verificado**, pero para acceder a datos de terceros y publicar la app habrá que completar la verificación después. Eso coincide con nuestra decisión de posponer la verificación hasta el alta ante el SAT.

![[fase0-04-negocio-sin-portfolio.png]]

### Paso 6.5 — Crear el portfolio comercial
Se llenó la ventana "Crea un portfolio comercial":

| Campo | Valor | Nota |
|---|---|---|
| Nombre del portfolio comercial | `Meridian World` | Debe coincidir con el nombre público del negocio y no puede llevar caracteres especiales. Lo verán otras empresas en Meta. |
| Nombre y apellido | Nydia Hernandez Gonzalez | Visibles para otras personas que se agreguen al portfolio. |
| Correo electrónico del negocio | `nydia.hernandez@meridianworld.io` | Meta lo usa para contactarte; tus clientes no lo ven. |

Al pulsar **Crear portfolio** aceptas la Política de privacidad de Meta (lo hizo Nydia personalmente).

![[fase0-05-crear-portfolio.png]]

### Paso 6.6 — Portfolio creado: elegir "Verificar más tarde"
Meta muestra "**Se creó Meridian World**" y ofrece dos botones: **Verificar más tarde** o **Iniciar verificación**. La verificación es la del negocio (ver sección 5): se necesita para enviar la app a revisión y para acceder a datos de personas ajenas al negocio. Para la práctica **no se necesita**, y Meridian aún no está dada de alta ante el SAT. **Decisión:** pulsar **Verificar más tarde**.

![[fase0-06-portfolio-creado.png]]

> Después de elegir "Verificar más tarde", el asistente volvió a la etapa Negocio (paso 6.7).

### Paso 6.7 — Negocio: elegir el portfolio y pulsar "Siguiente"
De regreso en la etapa Negocio, ahora aparece **Meridian World** con el círculo ya seleccionado (por eso "Siguiente" se habilitó). Meta repite el aviso: se puede conectar un portfolio no verificado, pero para acceder a datos de terceros habrá que verificarlo después.

![[fase0-07-negocio-portfolio-seleccionado.png]]

### Paso 6.8 — Requisitos de publicación
Meta dice: "No se identificaron requisitos". Esto puede cambiar si más adelante se agregan otros casos de uso. No hay nada que hacer aquí; pulsa **Siguiente**.

![[fase0-08-requisitos.png]]

### Paso 6.9 — Resumen y "Crear app"
Última revisión antes de crear: nombre de la app (Meridian Mensajeria), correo, caso de uso de WhatsApp, negocio **Meridian World — "Negocio no verificado"** y requisitos vacíos. Al pulsar **Crear app** se aceptan las Condiciones de la plataforma de Meta y las Políticas para desarrolladores (lo hizo Nydia personalmente).

![[fase0-09-resumen.png]]

### Paso 6.10 — La app quedó creada: el "Panel"
Meta abre el panel de la app con una ventana de bienvenida ("Te damos la bienvenida al panel de apps"). Se puede cerrar con la X o hacer el recorrido guiado (opcional).

Cosas que se ven en el panel:
- Arriba a la izquierda aparece el nombre de la app, **Meridian Mensajeria**.
- Menú lateral: Panel, Acciones requeridas, Casos de uso, Inicio de sesión con Facebook, Pruebas, **Publicar** (con la etiqueta **"Sin publicar"**), Configuración de la app, Roles de la app y Bandeja de entrada de alertas.
- En "Personalización y requisitos de la app" aparece la tarea **Personalizar el caso de uso "Conectarte con los clientes a través de WhatsApp"**. Ahí está el camino hacia la API de WhatsApp.

**"Sin publicar" es lo normal y lo que queremos.** La app está en modo desarrollo: solo tú, y las personas que agregues como roles, pueden usarla. Para practicar con tus propios números no hace falta publicarla ni pasar App Review.

![[fase0-10-panel-app-creada.png]]

### Paso 6.11 — Panel con la lista de tareas
Con la ventana de bienvenida cerrada, el panel muestra tres tareas en "Personalización y requisitos de la app":
1. **Personalizar el caso de uso "Conectarte con los clientes a través de WhatsApp"** (es la que usamos ahora).
2. Probar casos de uso.
3. Asegúrate de que se cumplan todos los requisitos y luego publica la app.

Abajo hay un aviso, "Conviértete en proveedor de tecnología" (Tech Provider), que pide completar la verificación de acceso. Es el camino que dejamos para más adelante, ver sección 5.

![[fase0-11-panel-tareas.png]]

### Paso 6.12 — Personalizar el caso de uso: aviso de Meta
Al abrir la primera tarea aparece una ventana ("Aprende a personalizar los casos de uso"). Opciones: **Hacer el recorrido** (opcional) u **OK**. Nydia cerró el aviso.

![[fase0-12-personalizar-aviso.png]]

### Paso 6.13 — Plataforma de WhatsApp Business: elegir portfolio y aceptar condiciones
La pantalla pide **seleccionar un portfolio comercial** (aparece Meridian World) y explica tres cosas:
- Recibirás un **número de teléfono de prueba** de WhatsApp para enviar mensajes a **un máximo de 5 números de teléfono**.
- Al pulsar **Continuar** se aceptan las *Condiciones de Facebook para WhatsApp Business* y las *Condiciones de hospedaje de Meta para la API de la nube* (lo hizo Nydia personalmente).
- Meta pide revisar las restricciones de uso para organizaciones políticas y gubernamentales.

![[fase0-13-plataforma-whatsapp-business.png]]

### Paso 6.14 — "Información general": el mapa de la integración
Después de Continuar, Meta muestra una guía de "30 minutos" con la pestaña **Integrar con la API** (la que nos sirve: para negocios con desarrolladores propios) frente a **Convertirte en socio**. Tres pasos:

| Paso de Meta | Tiempo que estima Meta | Qué es |
|---|---|---|
| **1. Pruébala** | 5 min | Enviar un mensaje desde el número de prueba para entender la API. |
| **2. Configuración de producción** | 20 min | Configurar tu número real de WhatsApp y empezar a enviar. Aquí se crean el número, las plantillas, el método de pago, el usuario del sistema y los webhooks. |
| **3. Verificación del negocio** | 5 min | Subir documentos para que Meta los revise. |

La sección "Activos que necesitarás o crearás" confirma la cadena que explicamos en el glosario: cuenta de Facebook → App de Meta (con Webhooks, paso 2) → Portfolio comercial → cuenta de WhatsApp Business **de prueba** (paso 1) y **tu WABA** (paso 2), que contiene número, plantillas y **método de pago** (paso 2), además del **usuario del sistema** (paso 2) y la verificación del negocio (paso 3).

> **Discrepancia por confirmar:** el asistente de Meta lista la verificación como paso 3, después de la configuración de producción. Esto encaja con la fuente de terceros (se puede empezar sin verificar, con límite de 250 destinatarios únicos por 24 horas), pero Meta no lo dice explícitamente en esta pantalla. Lo confirmaremos al llegar al paso 2, cuando veamos si deja registrar el número sin verificar. Además, "método de pago" aparece en el paso 2: habrá que ver si Meta lo exige para registrar el número o solo para enviar mensajes de pago.

![[fase0-14-informacion-general-api.png]]

### Paso 6.15 — Paso 1 de Meta, "Pruébalo": Meta ya creó el número de prueba
Meta entregó automáticamente una cuenta de WhatsApp Business **de prueba** (WABA de prueba) con un número de prueba. La primera casilla aparece como **Completado**: "Solicita un número de prueba de WhatsApp".

| Dato | Valor (de prueba) | Para qué sirve |
|---|---|---|
| Número de prueba | +1 (555) 640-2656 | Es el *remitente* de los mensajes de práctica. No es el número dedicado de Nydia. |
| Phone Number ID | 1343137562213970 | Identificador interno del número. La API lo usa en la URL para enviar (no es el teléfono). |
| ID de la cuenta de WhatsApp Business (WABA) | 3148175478714513 | Identificador de la cuenta que contiene el número y, más adelante, las plantillas. |
| Token de acceso | "Not generated yet" (botón **Generar token**) | La "llave" que prueba que quien llama a la API tiene permiso. |

> **Seguridad:** el Phone Number ID y el ID de la WABA son identificadores, no contraseñas. El **token sí es secreto**: quien lo tenga puede enviar mensajes como tu negocio. No se pega en el chat ni se guarda en este manual; irá en un archivo de variables de entorno (`.env`) fuera del repositorio. El token que genera este botón es **temporal**; el permanente se crea después con un Usuario del sistema.

**Segunda casilla (pendiente): "Envía un mensaje desde tu número de prueba"** (Meta estima unos 2 minutos). Tiene tres campos:
- **Tu número de prueba:** ya viene elegido.
- **Destinatario:** "Selecciona un número de destinatario". Aquí se agrega el celular de Nydia (la cuenta de prueba solo puede escribir a un máximo de 5 números, y cada uno debe confirmarse).
- **Mensaje:** viene "Confirmación del pedido", una plantilla de ejemplo de Meta.

A la derecha, Meta muestra el **mismo envío como código `curl`**: un POST a `https://graph.facebook.com/v25.0/<Phone Number ID>/messages`, con el token en el encabezado `Authorization: Bearer`, y un cuerpo JSON con `messaging_product: whatsapp`, el destinatario (`to`), `type: template` y el nombre de la plantilla. **Esto es exactamente lo que hará nuestro backend**: el botón "Enviar mensaje" solo hace esa llamada por ti. Dato útil: la versión de la API hoy es v25.0.

Tercera sección, **"Revisa los webhooks de prueba"**: dice "Esperando a que los webhooks empiecen a mostrar los mensajes enviados…". Cuando enviemos el mensaje debería mostrarse ahí cómo avisa Meta del estado (enviado, entregado, leído).

Abajo hay un botón **Continuar** hacia el **Paso 2: Configuración de producción**; todavía no se pulsa.

![[fase0-15-paso1-pruebalo.png]]

### Paso 6.16 — Agregar el celular como destinatario (ventana de Facebook)
Al elegir "Destinatario" y agregar un número, Meta abre una ventana emergente de facebook.com que conecta la app con tu cuenta. Son cuatro pantallas, en este orden:

1. **¿Continuar como Nydia Hernandez?** Meridian Mensajeria recibirá tu nombre y foto de perfil. Se pulsa **Continuar**.
   ![[fase0-16-continuar-como.png]]
2. **Elige las cuentas de WhatsApp a las que quieres que acceda Meridian Mensajeria.** Dos opciones: "todas las actuales y futuras" o **"solo las actuales"**. Se dejó **solo las actuales**, con la única cuenta marcada: *Test WhatsApp Business Account* (la WABA de prueba). Es la opción más segura: la app no recibe acceso automático a cuentas futuras.
   ![[fase0-17-elegir-cuenta-whatsapp.png]]
3. **Revisa la solicitud de acceso:** la app podrá *administrar tus cuentas de WhatsApp* y *administrar y acceder a conversaciones en WhatsApp*, ambas limitadas a 1 cuenta. Se aceptan las condiciones de Meta Business Tools y se pulsa **Guardar**.
   ![[fase0-18-solicitud-acceso.png]]
4. **"Nydia Hernandez se conectó a Meridian Mensajeria".** Se pulsa **De acuerdo**. Esa conexión se puede administrar luego en "integraciones comerciales".
   ![[fase0-19-conectado.png]]

Resultado: en "Destinatario" aparece el celular personal de Nydia (terminación 1295), lista para recibir mensajes de prueba.

![[fase0-20-destinatario-seleccionado.png]]

### Paso 6.17 — Token temporal generado y listo para enviar
Al pulsar **Generar token**, el campo muestra el token (empieza con `EAA…`) y el botón cambia a "Generar nuevo token". Meta avisa que en apps nuevas el número de prueba puede tardar un minuto en registrarse. Con el token, el botón **Enviar mensaje** se activa y el código `curl` ya lleva el token en `Authorization: Bearer …`.

**Estructura exacta del envío de la plantilla de ejemplo** (sin token), que nuestro backend va a reproducir:

```
POST https://graph.facebook.com/v25.0/<PHONE_NUMBER_ID>/messages
Authorization: Bearer <TOKEN>
Content-Type: application/json

{ "messaging_product": "whatsapp",
  "to": "<celular con código de país, sin + ni espacios>",
  "type": "template",
  "template": {
    "name": "jaspers_market_order_confirmation_v1",
    "language": { "code": "en_US" },
    "components": [{ "type": "body", "parameters": [
        { "type": "text", "text": "John Doe" },
        { "type": "text", "text": "123456" },
        { "type": "text", "text": "Oct 5, 2026" } ] }] } }
```

Lo que enseña este ejemplo para nuestras plantillas: una plantilla se identifica por **nombre + idioma**, y sus variables ({{1}}, {{2}}, {{3}}…) se llenan con una lista ordenada de `parameters` dentro del componente `body`. Nuestras plantillas del catálogo (cita confirmada, recordatorio, etc.) usarán esta misma forma, con idioma `es_MX`.

![[fase0-21-token-generado.png]]
> La captura se guardó con el token tapado a propósito.

> **Incidente de seguridad de práctica (5 oct 2026):** el token temporal se pegó completo en el chat. Riesgo bajo: es temporal, corresponde a un número de prueba que solo puede escribir a números verificados, y el chat es privado. Aun así, queda como regla del proyecto: **los tokens no se pegan en el chat, ni en capturas, ni en este manual; viven solo en un archivo `.env` fuera del repositorio.** El token pegado no se guardó en ningún documento.

### Paso 6.18 — Primer mensaje enviado y recibido (5 oct 2026, 12:45 a.m.)
Nydia pulsó **Enviar mensaje** y el mensaje de prueba llegó a su WhatsApp. En el celular aparece como una **cuenta de negocio** con el número de prueba +1 (555) 640-2656 y el texto "Order confirmed" con los datos del ejemplo (John Doe, pedido 123456, 5 de octubre de 2026) y un botón "Visit order details".

![[fase0-22-mensaje-recibido.png]]

**Esto confirma que la cadena completa funciona:** cuenta de desarrolladora → app → portfolio → WABA de prueba → número de prueba → API → celular.

### Paso 6.19 — El webhook de prueba: lo que Meta avisó
Casi al mismo tiempo, Meta mandó un aviso (webhook) con el estado del mensaje. Esta es su estructura, con los datos personales enmascarados:

```json
{
  "object": "whatsapp_business_account",
  "entry": [{
    "id": "3148175478714513",                 // ID de la WABA de prueba
    "changes": [{
      "field": "messages",
      "value": {
        "messaging_product": "whatsapp",
        "metadata": { "display_phone_number": "15556402656",
                      "phone_number_id": "1343137562213970" },
        "contacts": [{ "wa_id": "52181XXXXX1295",
                       "user_id": "MX.2030…7166" }],
        "statuses": [{
          "id": "wamid.HBgN…(identificador del mensaje)…",
          "status": "delivered",
          "timestamp": "1791182714",          // 5 oct 2026, 06:45:14 UTC = 00:45 hora de México
          "recipient_id": "52181XXXXX1295",
          "recipient_user_id": "MX.2030…7166",
          "conversation": { "id": "…", "origin": { "type": "utility" } },
          "pricing": { "billable": true, "pricing_model": "PMP",
                       "category": "utility", "type": "regular" }
        }]
      }
    }]
  }]
}
```

**Cómo leerlo (para el backend):**
- `statuses` (y no `messages`) significa que es un **aviso de estado** de un mensaje que nosotros enviamos. Los mensajes que escribe el paciente llegan en un arreglo `messages`. Los estados esperables son `sent`, `delivered`, `read` y `failed`; aquí llegó `delivered`.
- `id` (el `wamid`) es el **identificador del mensaje**. Hay que guardarlo al enviar para poder ligar cada aviso de estado con el mensaje correcto (esto alimenta nuestra detección de fallas de entrega, ver `diagrama-falla-entrega-notificacion`).
- `phone_number_id` indica a **qué número del negocio** pertenece el aviso: con varios consultorios (multi-cliente) es la llave para saber a qué *tenant* corresponde.
- `pricing`: Meta ya clasificó el mensaje como **`utility`** y lo marca **`billable: true`**. `pricing_model: PMP` parece indicar cobro por mensaje; no está verificado con esta pantalla. Es el primer dato real de costo; hay que contrastarlo con el panel de facturación de Meta.

#### Hallazgo 1 — El teléfono no es una llave de identidad confiable por sí sola
Dos cosas en este aviso chocan con lo que hoy dice `proyecto-01-modelo-de-datos-reconciliado` ("telefono: E.164, llave para WhatsApp"):

1. **El número que enviamos no es el que Meta devuelve.** Enviamos a `52 81…` y Meta respondió con `521 81…` (con un "1" después del código de país) en `wa_id` y `recipient_id`. Es una particularidad conocida de México. Un artículo de un proveedor (Gupshup, de terceros y anterior) describe el caso inverso, así que **no hay que asumir el formato**: se debe guardar el `wa_id` tal como Meta lo devuelve y comparar números normalizados, o el mismo paciente se vería como dos personas distintas.
2. **Aparece `user_id` ("MX.2030…").** Es un **BSUID** (*Business-Scoped User ID*): un identificador del usuario ligado a tu portfolio, con formato `PAÍS.código`. Según la documentación de Meta, empezó a aparecer en los webhooks en abril de 2026, y está llegando la función de **nombres de usuario** de WhatsApp. Si una persona adopta un nombre de usuario, **Meta puede omitir su teléfono (`wa_id`) en los avisos** cuando el negocio no le ha escrito en los últimos 30 días ni la tiene en su lista de contactos. Meta pide guardar el BSUID junto al teléfono y permite enviar mensajes por BSUID (soporte de envío desde julio de 2026).

**Qué significa para nuestro diseño (propuesta, pendiente de que Nydia la apruebe):**
- En `clientes`, además de `telefono`, guardar **`wa_id`** (como lo devuelve Meta) y **`bsuid`** (cuando llegue).
- Identificar al paciente que escribe por **BSUID primero**, luego por `wa_id`/teléfono normalizado.
- Esto afecta REQ-NOT-03 (mensaje de un número no reconocido), REQ-ACC-04 (cambio de teléfono) y SEG-06 (límite por número). Un paciente que ya escribió o recibió mensajes de nosotros en los últimos 30 días conserva su teléfono visible, así que el riesgo real aparece sobre todo con **mensajes entrantes de personas que nunca hemos contactado**.

### Paso 6.20 — Segundo aviso: el mensaje fue leído (`read`)
Cuatro segundos después (12:45:19) llegó otro aviso con `"status": "read"` y marca de tiempo 1791182718. Trae el **mismo `id` de mensaje (`wamid`)** y la misma conversación que el aviso `delivered`. El panel también aclara: *"Si no se recibe, primero envía un mensaje de WhatsApp desde el teléfono del destinatario a tu número comercial para abrir un intervalo de conversación"* (la ventana de 24 horas del glosario).

![[fase0-23-webhook-read.png]]

**Qué enseña para el backend:**
- Un mismo mensaje genera **varios avisos** (`sent` → `delivered` → `read`, o `failed`), cada uno con el mismo `wamid`. Hay que ligarlos por `wamid`.
- El aviso `read` **también trae el bloque `pricing`** con `billable: true`. Eso no significa que se cobre dos veces: el cobro es por mensaje, no por aviso. Para medir el costo hay que contar **mensajes únicos por `wamid`**, nunca avisos.
- Los avisos pueden **repetirse o llegar desordenados**. El manejador debe ser *idempotente*: procesar dos veces el mismo aviso no debe duplicar nada (guardar el último estado y su hora, e ignorar un estado más viejo que el ya guardado).
- Meta espera una respuesta HTTP 200 rápida al webhook; la lógica pesada va después, en segundo plano. (Práctica habitual; confirmar en la documentación de Meta al implementarlo.)

### Paso 6.21 — Paso 2 "Configuración de producción": las cuatro tareas
Con la cuenta de WhatsApp del número dedicado ya eliminada, Nydia abrió el Paso 2. Meta lo divide en cuatro tareas, con sus tiempos estimados (los de Meta, no medidos por nosotros): **configurar webhooks** (~3 min), **registrar el número de teléfono** (~1 min), **agregar la información de pago para enviar mensajes iniciados por la empresa** (~1 min) y **enviar mensaje** (~2 min).

![[fase0-24-paso2-registrar-numero.png]]

**Hallazgos de esta pantalla:**
- **Aviso de webhooks:** *"Las apps solo podrán recibir webhooks de prueba enviados desde el panel de la app mientras esta esté sin publicar. No se entregarán datos de producción, incluidos los de los administradores, desarrolladores o evaluadores de la app, a menos que esta se haya publicado."* **Corrección a lo dicho antes:** "Sin publicar" basta para el número de prueba, pero para recibir mensajes reales en el webhook hay que **publicar la app**. Publicarla probablemente pida una URL de política de privacidad (por confirmar al llegar a "Publicar").
- **Límite de números:** *"Puedes agregar hasta 2 números de teléfono por empresa. Una vez que completes la verificación del negocio, podrás agregar hasta 20 números."* No estorba al modelo multicliente, porque cada cliente tendrá su propio portfolio y su propio WABA.
- **Pago:** aparece como una tarea aparte, *"para enviar mensajes iniciados por la empresa"*. Sugiere que no es requisito para registrar el número, pero falta confirmarlo al terminar el registro.
- Las dos primeras tareas (webhooks y número) no dependen del orden: se puede registrar el número sin haber puesto el webhook.

### Paso 6.22 — "Agregar número de teléfono": información de la empresa
Al pulsar "Agregar número nuevo" se abre un asistente de cuatro etapas: **Información de la empresa → Perfil de WhatsApp → Agregar número → Verificar número**. La primera pide el nombre de la empresa ("Meridian World"), un **sitio web o página de perfil** (si no hay sitio, sirve la URL de un perfil de redes sociales del negocio), el país (México) y una dirección opcional, además de un reCAPTCHA que Nydia resolvió ella misma. Meta aclara que el público no verá esta información en el perfil de WhatsApp.

![[fase0-25-agregar-numero-info-empresa.png]]

**Decisión (5 oct 2026):** el sitio web de Meridian ya está terminado pero sin publicar. No es obligatorio para avanzar hoy (se usa un perfil social), pero se recomienda publicarlo esta semana junto con una página de aviso de privacidad, porque probablemente lo pidan la verificación del negocio (Paso 3), la publicación de la app y la revisión del nombre para mostrar (los tres puntos por confirmar cuando se llegue a cada uno).

### Paso 6.23 — Página de Facebook de Meridian World y su nombre de usuario
Como el sitio web aún no está publicado, Nydia creó una **Página de Facebook** (gratuita) llamada "Meridian World", categoría "Empresa de tecnología de la información", con la descripción "Desarrollo web y digitalización para negocios". Se administra desde su cuenta personal (una Página no tiene usuario y contraseña propios). Los datos de contacto, ubicación y horario se dejaron **vacíos** a propósito: son públicos y no se quiere exponer teléfono, correo ni domicilio. Después, en *Configuración y privacidad → Configuración general de la página*, se cambió el **nombre de usuario** a `meridianworldio`; Facebook mostró "Nombre de usuario disponible", y la dirección pública quedó como `facebook.com/meridianworldio`.

![[fase0-26-facebook-nombre-de-usuario.png]]

### Paso 6.24 — Etapa 1 del asistente completada
La URL de la Página se pegó en el campo "Sitio web o página del perfil de la empresa". La etapa 1 quedó con palomita y se habilitó "Siguiente".

![[fase0-27-info-empresa-con-url.png]]

### Paso 6.25 — Etapa 2: "Perfil de WhatsApp"
La segunda etapa crea el **perfil de WhatsApp Business**, que sí verán las personas. Pide: *nombre visible* ("Meridian World"; Meta indica que debe coincidir con el nombre de la empresa y cumplir sus normas sobre nombres visibles), *zona horaria* (el menú propuso América/Ciudad Juárez; se debe confirmar la de la ciudad donde opera Meridian), *categoría* (obligatoria) y *descripción* (opcional, hasta 512 caracteres). Meta aclara que este perfil se puede editar después desde la configuración de **Activos comerciales** en Meta Business Suite.

![[fase0-28-perfil-de-whatsapp-business.png]]

**Lo aprendido:** el nombre visible coincide con el del portfolio y el de la Página (consistencia que ayuda a la aprobación, aunque Meta no detalla los criterios de rechazo en lo que revisamos). Un perfil social sirve de "sitio web" provisional; se reemplazará por `meridianworld.io` cuando el sitio esté publicado.

### Paso 6.26 — La Página de Facebook vista como visitante (ventana de incógnito)
Para comprobar lo que ve el público, Nydia abrió `facebook.com/meridianworldio` en una ventana de incógnito, sin sesión iniciada. La Página carga con el nombre "Meridian World", la descripción, la categoría "Information Technology Company" y 0 seguidores. **No aparece el nombre personal de Nydia** en la vista principal (queda por mirar la pestaña "About").

![[fase0-29-pagina-en-incognito.png]]

### Paso 6.27 — Número agregado, estado "No registrado"
Al terminar las etapas del asistente, el panel del Paso 2 muestra la cuenta de WhatsApp Business "Meridian World" (con su identificador) y debajo el número dedicado con la etiqueta **"No registrado"**, su identificador de número de teléfono y el botón **"Registrar"**. También hay un interruptor "Suscribir webhooks", que se deja apagado hasta tener URL de webhook. El número se tapó en la captura guardada.

![[fase0-30-numero-agregado-no-registrado.png]]

**Hallazgos:**
- **El panel hace el registro:** aunque la documentación de Meta dice que el registro (`POST /<PHONE_NUMBER_ID>/register`) es por API, en este asistente basta el botón "Registrar" (por confirmar al pulsarlo).
- **Formato del número:** el panel lo muestra como `+52 1 656…` (con un "1" después del código de país), igual que el "521" del webhook del Paso 1. No se sabe si Meta lo normalizó o se escribió así. Si la verificación por SMS falla, ese "1" es el primer sospechoso; ante un error, no reintentar varias veces (límite de 10 solicitudes de registro por número en 72 horas).
- La lada del número dedicado (656) es de Ciudad Juárez, lo que concuerda con la zona horaria propuesta por Meta.

### Paso 6.28 — Número registrado
Tras pulsar "Registrar" y fijar el PIN de verificación en dos pasos (que Nydia guardó por su cuenta y no aparece en ninguna captura), la etiqueta del número cambió de "No registrado" a **"Registrado"** (verde) y la tarea "Registra tu número de teléfono de WhatsApp" quedó con palomita. Las otras tres tareas del Paso 2 siguen pendientes: webhooks, información de pago y enviar mensaje. El interruptor "Suscribir webhooks" sigue apagado.

![[fase0-31-numero-registrado.png]]

**Hallazgos:**
- **El número se pudo registrar sin verificar el negocio y sin haber agregado un método de pago:** la tarea de pago aparece sin completar y el registro igualmente quedó en "Registrado". Esto responde la pregunta abierta de la sección 6.14. (No implica que el nombre para mostrar ya esté aprobado ni cuáles son los límites de envío: ambos por revisar en WhatsApp Manager.)
- El "1" extra del formato mexicano (`+52 1 656…`) no impidió el registro.
- Con el número en la API de la nube, ya no se puede usar la app de WhatsApp normal con ese número.
- Los tiempos reales de esta fase no se midieron.

### Paso 6.29 — Tarea "Enviar mensaje" con el número real
La última tarea del Paso 2 tiene dos partes. **Paso 1: "Genera un token"**: un botón "Generar token" que, según el texto de la pantalla, crea un *token de acceso permanente* para la cuenta de WhatsApp Business (es un secreto: va a un administrador de contraseñas y después a un `.env` fuera del repositorio; nunca al chat, capturas ni manual). **Paso 2: "Envía un mensaje desde tu número registrado"**: remitente (el número registrado), destinatario (el selector trae "US +1" por defecto; hay que cambiarlo a México +52), cuerpo ("Write message" o "Use template"), botones "Send message" y "Run in Postman", y un **código QR** para que el cliente escriba primero al número registrado. El aviso de la pantalla dice: *"Scan to reply. The customer sends the first message by scanning the QR code, which opens your 24-hour customer service window."* Debajo del botón, Meta advierte: *"By sending, you confirm this recipient has opted in to receive messages from your business."*

![[fase0-32-enviar-mensaje-panel.png]]

**Qué cuesta (según la [página de precios de Meta](https://developers.facebook.com/documentation/business-messaging/whatsapp/pricing), consultada el 5 oct 2026):**
- Desde el 1 de julio de 2025 Meta cobra **por plantilla entregada**, con tarifa según categoría y código de país del destinatario.
- Los mensajes que **no son plantilla** son gratis dentro de una ventana de atención al cliente de 24 horas (se abre cuando el usuario escribe primero).
- Las plantillas de utilidad son gratis si se entregan con la ventana abierta; fuera de ella se cobran. Las de marketing se cobran siempre.
- Las ventanas de "punto de entrada gratuito" (anuncios a WhatsApp o botones de Páginas de Facebook) dan 72 horas sin cobro.

**Plan de prueba sin gasto:** escanear el QR con el celular de Nydia (abre la ventana), generar el token, elegir destinatario México +52 con los 10 dígitos (sin el "1"), dejar "Write message" y enviar. No usar plantilla (el mensaje de prueba del Paso 1 fue plantilla fuera de ventana, por eso `billable: true`). Como todavía no hay webhook, el mensaje entrante del QR no se capturará; hay que repetir el escaneo con el webhook activo para guardar la primera carga útil entrante (probar 52 vs 521 y el BSUID).

**Implicación de diseño (REQ-ADM-08):** un recordatorio a un paciente sin ventana abierta es una plantilla de utilidad y se cobra; si el paciente responde, lo que sigue es gratis mientras dure la ventana. El costo por mensaje depende de la ventana, no solo del volumen.

### Paso 6.30 — Primer envío de punta a punta con el número real
Nydia escaneó el QR con su celular, que abrió un chat con el número dedicado con un texto ya escrito (*"Hello! I am interested in learning more about your business."*), y lo envió a las 5:15 p. m. Después, desde el panel de Meta (token generado, remitente = número registrado, destinatario = su celular en México +52 con los 10 dígitos, "Write message"), mandó el texto por defecto *"What can I help you with today?"*, que llegó a su teléfono a las 5:24 p. m. **El ciclo completo funcionó con el número real**: mensaje entrante (QR) → ventana de 24 horas abierta → respuesta de texto libre desde la API.

![[fase0-33-token-y-envio-redactado.png]]
![[fase0-34-chat-mensaje-recibido.png]]
![[fase0-35-chat-mensaje-enviado.png]]

**Qué se observó (en las capturas):**
- A las 5:15 el mensaje del cliente aparece con una palomita (enviado) y a las 5:24 con doble palomita (entregado). Las dos horas son las que muestra el teléfono.
- El texto libre se aceptó sin plantilla porque la ventana estaba abierta, y el panel no pidió método de pago.
- El teléfono muestra el chat con el número `+52 656…` y **no** con el nombre "Meridian World": el nombre para mostrar todavía no se ve (puede estar pendiente de aprobación; no verificado, revisar en WhatsApp Manager).
- El chat trae un aviso de WhatsApp: *"This business uses a secure service from Meta to manage this chat."* Es el aviso que se muestra a los clientes cuando el negocio usa la API en la nube.
- El formato del destinatario "MX +52 + 10 dígitos" funcionó sin el "1" extra.
- Como todavía no hay webhook, el mensaje entrante del QR no se capturó como carga útil; falta repetirlo con el webhook activo.

**Incidente de seguridad #2:** la captura original del panel mostraba el token permanente cortado por el borde del campo (Nydia indica que falta la otra mitad; riesgo bajo). Nydia decidió el 6 oct 2026 no revocarlo: el token de la captura está incompleto, así que no hay riesgo; el incidente se cerró. La copia guardada en el manual tiene el token, los números y el QR tapados (el QR contiene el número). Regla reforzada: antes de capturar un panel con un campo de token, taparlo o recortarlo; mejor aún, no capturar esa parte.

### Qué falta capturar
- Panel de facturación de Meta: confirmar que el texto libre de las 5:24 p. m. no se cobró y ver cuánto costó la plantilla del Paso 1.
- Paso 2 de Meta: tareas pendientes (webhooks e información de pago; "Enviar mensaje" ya se probó, falta ver si el panel la marca como completada). Además: etapas "Agregar número" y "Verificar número" del asistente y la pantalla del botón "Registrar", que no se capturaron.
- Estado del nombre para mostrar y límites de envío del número, en WhatsApp Manager.

### Lo aprendido en esta fase
- El orden real del asistente es: Detalles → Casos de uso → Negocio → Requisitos → Resumen. La creación del portfolio ocurre *dentro* del asistente, en la etapa Negocio, no antes.
- Un portfolio nuevo no está verificado; se puede avanzar sin verificar.
- El nombre del portfolio es público: conviene escribirlo tal como se llamará el negocio ante clientes.
- Tiempo aproximado de esta fase hasta la app creada: unos minutos, sin esperas de aprobación de Meta.
- El asistente de la API de Meta tiene tres pasos: Pruébala (5 min) → Configuración de producción (20 min) → Verificación del negocio (5 min). Los tiempos son los que estima Meta, no medidos por nosotros.
- El primer mensaje de prueba llegó en menos de un minuto y el webhook de estado (`delivered`) trajo categoría `utility` y `billable: true`.
- Meta devuelve el teléfono mexicano con un "1" extra (521) y agrega un BSUID (`user_id`): el teléfono solo no basta como llave de identidad.
- La app nace "Sin publicar" (modo desarrollo) y no necesita verificación de negocio para empezar a probar con el número de prueba. Para recibir webhooks de producción (mensajes reales) hay que publicarla (aviso de Meta en el Paso 2).
- Sin verificar el negocio, Meta permite 2 números por empresa; con verificación, hasta 20.
- Un mensaje entrante por QR abre la ventana de 24 horas y permite responder con texto libre desde la API, sin plantilla y sin método de pago (prueba del 5 oct 2026).
- El panel de Meta muestra el token permanente completo; es el punto más fácil para filtrarlo en una captura.
- Para pasar la app a modo Live, Meta pide nombre para mostrar, correo de contacto, URL de Términos de servicio, ícono, categoría y propósito de la app; la política de privacidad y la URL de eliminación de datos no figuran como obligatorias (recomendadas), y la verificación del negocio no es obligatoria para publicar ([Meta, ajustes básicos](https://developers.facebook.com/documentation/development/create-an-app/app-dashboard/basic-settings), 6 oct 2026).
- El webhook debe ser una URL pública HTTPS con certificado válido (no autofirmado), responder el saludo de verificación con `hub.challenge`, validar la firma `X-Hub-Signature-256` y deduplicar, porque Meta reintenta hasta 7 días ([Meta, webhooks](https://developers.facebook.com/documentation/business-messaging/whatsapp/webhooks/create-webhook-endpoint)).
- Todo portfolio nuevo empieza con 250 usuarios únicos cada 24 horas; la verificación del negocio es una vía para subir a 2,000, no la única ([Meta, límites](https://developers.facebook.com/documentation/business-messaging/whatsapp/messaging-limits)).

## Fuentes
- [Get started — Meta for Developers](https://developers.facebook.com/documentation/business-messaging/whatsapp/get-started)
- [Business phone numbers — Meta for Developers](https://developers.facebook.com/documentation/business-messaging/whatsapp/business-phone-numbers/phone-numbers)
- [Template categorization — Meta for Developers](https://developers.facebook.com/documentation/business-messaging/whatsapp/templates/template-categorization)
- [Onboard WhatsApp Business app users (Coexistence) — Meta for Developers](https://developers.facebook.com/documentation/business-messaging/whatsapp/embedded-signup/onboarding-business-app-users)
- [Messaging limits — Meta for Developers](https://developers.facebook.com/documentation/business-messaging/whatsapp/messaging-limits)
- [¿Es obligatorio verificar tu negocio en Meta? — Empleado.uno](https://www.empleado.uno/blog/es-obligatorio-verificar-tu-negocio-en-meta) (fuente de terceros)
- [Business-scoped user IDs — Meta for Developers](https://developers.facebook.com/documentation/business-messaging/whatsapp/business-scoped-user-ids)
- [Diferencias de formato en números de México — Gupshup](https://support.gupshup.io/hc/en-us/articles/4413210201625-Why-do-we-see-differences-between-the-actual-dial-numbers-and-the-one-registered-on-WhatsApp-for-region-Brazil-Mexico) (fuente de terceros)
