# Plan de Pruebas para la Integración de WhatsApp Business API

## 1. Pruebas Estructurales: Configuración y Conectividad

Esta sección se enfoca en verificar la correcta configuración del entorno y la capacidad de la aplicación para establecer y mantener una conexión con la API de WhatsApp Business de Meta.

### 1.1. Verificación de Variables de Entorno

**Objetivo**: Asegurar que todas las variables de entorno críticas para la operación de la aplicación estén definidas y sean accesibles.

| ID de Prueba | Descripción del Caso de Prueba | Pasos de Ejecución | Resultado Esperado |
|---|---|---|---|
| TP-ENV-001 | Verificar `ACCESS_TOKEN` | 1. Asegurarse de que `ACCESS_TOKEN` esté presente en el archivo `.env` y sea un token válido de Meta. | La aplicación debe iniciar sin advertencias de token faltante y el token debe ser reconocido por la API de Meta. |
| TP-ENV-002 | Verificar `APP_SECRET` | 1. Asegurarse de que `APP_SECRET` esté presente en el archivo `.env` y coincida con el secreto de la aplicación en el panel de desarrolladores de Meta. | La aplicación debe iniciar sin advertencias de secreto faltante. |
| TP-ENV-003 | Verificar `VERIFY_TOKEN` | 1. Asegurarse de que `VERIFY_TOKEN` esté presente en el archivo `.env` y coincida con el token configurado en el webhook de Meta. | La validación del webhook debe ser exitosa. |
| TP-ENV-004 | Verificar `REDIS_HOST` y `REDIS_PORT` | 1. Asegurarse de que `REDIS_HOST` y `REDIS_PORT` estén presentes en el archivo `.env`. | La aplicación debe intentar conectarse a Redis usando estos valores. Si Redis está disponible, la conexión debe ser exitosa. Si no, debe manejar el error sin detener la aplicación. |

### 1.2. Conexión a la API de WhatsApp Business (Meta)

**Objetivo**: Confirmar que la aplicación puede comunicarse exitosamente con los endpoints de la API de WhatsApp Business.

| ID de Prueba | Descripción del Caso de Prueba | Pasos de Ejecución | Resultado Esperado | 
|---|---|---|---|
| TP-API-001 | Envío de mensaje de prueba (inicialización) | 1. Iniciar la aplicación con credenciales válidas. 2. Enviar un mensaje de prueba a un número de WhatsApp registrado. | El mensaje debe ser enviado exitosamente y aparecer en el chat de WhatsApp del destinatario. |
| TP-API-002 | Recepción de estado de mensaje | 1. Enviar un mensaje desde la aplicación. 2. Esperar la notificación de estado de entrega (`delivered`) y lectura (`read`) a través del webhook. | La aplicación debe registrar los estados de `delivered` y `read` correctamente. |

### 1.3. Configuración y Validación del Webhook

**Objetivo**: Verificar que el webhook esté correctamente configurado en la plataforma de Meta y que la aplicación pueda recibir y procesar eventos entrantes.

| ID de Prueba | Descripción del Caso de Prueba | Pasos de Ejecución | Resultado Esperado |
|---|---|---|---|
| TP-WEB-001 | Validación del webhook | 1. Configurar la URL del webhook en el panel de desarrolladores de Meta. 2. Proporcionar el `VERIFY_TOKEN` configurado en `.env`. | Meta debe validar la URL del webhook y mostrar un estado de 
activo. |
| TP-WEB-002 | Recepción de mensajes entrantes | 1. Enviar un mensaje de texto desde un número de WhatsApp de prueba al número de WhatsApp Business configurado. 2. Observar los logs de la aplicación. | La aplicación debe recibir el mensaje a través del webhook y procesarlo según la lógica definida (ej. responder con un mensaje predeterminado). |
| TP-WEB-003 | Recepción de estados de mensajes | 1. Enviar un mensaje desde la aplicación. 2. El destinatario debe recibir y leer el mensaje. 3. Observar los logs de la aplicación. | La aplicación debe recibir y registrar los eventos de estado de entrega y lectura del mensaje. |

## 2. Pruebas Funcionales: Flujos de Mensajería

Esta sección valida la funcionalidad de los diferentes tipos de mensajes y flujos de conversación que la aplicación debe soportar.

### 2.1. Envío de Mensajes de Texto

**Objetivo**: Verificar que la aplicación puede enviar y recibir mensajes de texto estándar correctamente.

| ID de Prueba | Descripción del Caso de Prueba | Pasos de Ejecución | Resultado Esperado |
|---|---|---|---|
| TP-MSG-001 | Envío de mensaje de bienvenida | 1. Un usuario nuevo envía un mensaje de "Hola" a la aplicación. | La aplicación debe responder con el mensaje de bienvenida configurado (`APP_DEFAULT_MESSAGE`). |
| TP-MSG-002 | Envío de respuesta a mensaje genérico | 1. Un usuario envía un mensaje de texto no reconocido por la lógica de la aplicación. | La aplicación debe responder con el mensaje predeterminado o una indicación de que no entiende el mensaje. |

### 2.2. Envío de Mensajes Interactivos (Botones y Listas)

**Objetivo**: Asegurar que la aplicación puede enviar mensajes con botones de respuesta rápida y listas interactivas, y procesar las selecciones del usuario.

| ID de Prueba | Descripción del Caso de Prueba | Pasos de Ejecución | Resultado Esperado |
|---|---|---|---|
| TP-INT-001 | Envío de mensaje con botones de respuesta rápida | 1. La aplicación envía un mensaje con botones de respuesta rápida (ej. "Probar Demo"). 2. El usuario selecciona una opción. | La aplicación debe recibir la selección del botón y ejecutar la acción asociada (ej. enviar un mensaje interactivo con medios). |
| TP-INT-002 | Procesamiento de selección de botón | 1. Un usuario selecciona el botón `REPLY_INTERACTIVE_MEDIA_ID`. | La aplicación debe responder con el mensaje interactivo de medios (`sendInteractiveMediaMessage`). |
| TP-INT-003 | Procesamiento de selección de carrusel de medios | 1. Un usuario selecciona el botón `REPLY_MEDIA_CAROUSEL_ID`. | La aplicación debe responder con el carrusel de medios (`sendMediaCarouselMessage`). |
| TP-INT-004 | Procesamiento de selección de oferta limitada | 1. Un usuario selecciona el botón `REPLY_OFFER_ID`. | La aplicación debe responder con el mensaje de oferta limitada (`sendLimitedTimeOfferMessage`). |

### 2.3. Envío de Mensajes Basados en Plantillas

**Objetivo**: Validar que la aplicación puede enviar mensajes utilizando plantillas pre-aprobadas por WhatsApp Business.

| ID de Prueba | Descripción del Caso de Prueba | Pasos de Ejecución | Resultado Esperado |
|---|---|---|---|
| TP-TMP-001 | Envío de plantilla de utilidad | 1. La aplicación intenta enviar un mensaje utilizando la plantilla `grocery_delivery_utility`. | El mensaje de plantilla debe ser enviado exitosamente con la imagen y el texto correctos. |
| TP-TMP-002 | Envío de plantilla de oferta limitada | 1. La aplicación intenta enviar un mensaje utilizando la plantilla `strawberries_limited_offer`. | El mensaje de plantilla debe ser enviado exitosamente con la imagen, el código de oferta y la fecha de expiración correctos. |
| TP-TMP-003 | Envío de plantilla de carrusel de medios | 1. La aplicación intenta enviar un mensaje utilizando la plantilla `recipe_media_carousel`. | El mensaje de plantilla debe ser enviado exitosamente con el carrusel de imágenes correcto. |

## 3. Pruebas de Manejo de Errores y Casos de Borde

Esta sección se enfoca en verificar cómo la aplicación reacciona ante situaciones inesperadas, errores de la API o entradas de usuario no válidas.

### 3.1. Errores de Conectividad y Autenticación

**Objetivo**: Evaluar la robustez de la aplicación frente a problemas de conexión o credenciales inválidas.

| ID de Prueba | Descripción del Caso de Prueba | Pasos de Ejecución | Resultado Esperado |
|---|---|---|---|
| TP-ERR-001 | `ACCESS_TOKEN` inválido/expirado | 1. Configurar un `ACCESS_TOKEN` inválido o expirado en `.env`. 2. Intentar enviar un mensaje. | La aplicación debe registrar un error de autenticación y no debe enviar el mensaje. Debe manejar el error de forma elegante sin crashear. |
| TP-ERR-002 | `APP_SECRET` incorrecto | 1. Configurar un `APP_SECRET` incorrecto en `.env`. 2. Intentar validar el webhook. | La validación del webhook debe fallar con un error de autenticación. |
| TP-ERR-003 | Fallo en la conexión a Redis | 1. Detener el servicio de Redis (si está en ejecución). 2. Iniciar la aplicación. 3. Intentar enviar un mensaje que use la caché. | La aplicación debe registrar un error de conexión a Redis, pero debe continuar funcionando sin crashear (gracias a la mejora implementada). Los mensajes que no dependan de Redis deben enviarse. |

### 3.2. Entradas de Usuario Inválidas o Inesperadas

**Objetivo**: Verificar cómo la aplicación maneja mensajes de usuario que no se ajustan a los flujos esperados.

| ID de Prueba | Descripción del Caso de Prueba | Pasos de Ejecución | Resultado Esperado |
|---|---|---|---|
| TP-EDGE-001 | Mensaje de texto muy largo | 1. Enviar un mensaje de texto con una longitud superior al límite de WhatsApp (4096 caracteres). | La aplicación debe manejar el mensaje (posiblemente truncándolo o respondiendo con un mensaje de error) sin crashear. |
| TP-EDGE-002 | Mensaje con caracteres especiales/emojis | 1. Enviar un mensaje que contenga una variedad de caracteres especiales y emojis. | La aplicación debe procesar y responder al mensaje correctamente, mostrando los caracteres y emojis sin corrupción. |
| TP-EDGE-003 | Mensaje de tipo no soportado | 1. Enviar un mensaje de WhatsApp de un tipo no soportado por la aplicación (ej. ubicación, contacto). | La aplicación debe ignorar el mensaje o responder con un mensaje genérico indicando que no puede procesar ese tipo de contenido. |

### 3.3. Comportamiento de la API de WhatsApp

**Objetivo**: Probar la resiliencia de la aplicación ante respuestas inesperadas o errores de la API de WhatsApp.

| ID de Prueba | Descripción del Caso de Prueba | Pasos de Ejecución | Resultado Esperado |
|---|---|---|---|
| TP-API-ERR-001 | Respuesta de error de la API | 1. Simular una respuesta de error de la API de WhatsApp (ej. código de estado 4xx o 5xx) al intentar enviar un mensaje. | La aplicación debe registrar el error de la API y manejarlo sin crashear, posiblemente reintentando o notificando un fallo. |
| TP-API-ERR-002 | Retraso en la respuesta de la API | 1. Simular un retraso significativo en la respuesta de la API de WhatsApp. | La aplicación debe manejar el retraso sin bloquearse indefinidamente, posiblemente con un tiempo de espera (timeout) configurado. |

