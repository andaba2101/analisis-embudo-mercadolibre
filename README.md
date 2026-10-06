# analisis-embudo-mercadolibre

Sprint 4 | Proyecto 4: Análisis de embudo y retención para MercadoLibre - Resumen ejecutivo

## 🛒 Introducción

En este proyecto asumí el rol de analista de producto dentro del equipo de Crecimiento y Retención de MercadoLibre.

Mi objetivo fue analizar el recorrido de los usuarios desde su primera visita hasta la compra, identificar los principales puntos de fuga dentro del embudo de conversión y evaluar cómo cambia la retención de usuarios a lo largo del tiempo.

Para desarrollar el análisis, utilicé SQL con el fin de:

- Construir un embudo de conversión multietapa.
- Calcular tasas de conversión entre eventos.
- Identificar caídas de usuarios entre etapas.
- Comparar el comportamiento por país, dispositivo y fuente de tráfico.
- Analizar la retención de usuarios por cohortes.
- Proponer oportunidades de mejora basadas en la evidencia disponible.

## 🎯 Objetivos del proyecto

Con este proyecto busqué demostrar mi capacidad para:

- Navegar tablas de eventos y retención.
- Construir embudos multietapa mediante CTEs en SQL.
- Calcular conversiones y drop-off entre etapas.
- Analizar la retención de usuarios por cohortes.
- Comparar métricas por país, dispositivo y fuente de referencia.
- Simular posibles mejoras de conversión o retención.
- Validar resultados antes de comunicar conclusiones.
- Redactar un informe ejecutivo mediante el modelo Contexto → Hallazgo → Implicación.

## 🗃️ Datasets utilizados

Trabajé con dos fuentes principales: una tabla de eventos del embudo y una tabla de actividad de usuarios para el análisis de retención.

| Tabla | Propósito |
| --- | --- |
| `mercadolibre_funnel` | Registra eventos de usuarios durante el proceso de compra. |
| `mercadolibre_retention` | Registra actividad recurrente de usuarios después de su registro. |

## 📚 Diccionario de datos

### Tabla `mercadolibre_funnel`

Esta tabla contiene los eventos generados por los usuarios durante su recorrido dentro de la plataforma.

| Columna | Tipo de dato | Ejemplo | Descripción |
| --- | --- | --- | --- |
| `user_id` | STRING | `c786f5e6-a5b8-45fc-94ee-efeba1a209ce` | Identificador único del usuario. |
| `session_id` | STRING | `sid_1129` | Identificador único de la sesión. |
| `event_name` | STRING | `add_to_cart` | Evento realizado por el usuario dentro de la plataforma. |
| `event_times` | FLOAT / INTEGER | `1.76001e+15` | Marca temporal del evento en formato Unix timestamp. |
| `country` | STRING | `Argentina` | País desde donde se genera el evento. |
| `device_category` | STRING | `mobile` | Tipo de dispositivo utilizado por el usuario. |
| `platform` | STRING | `android` | Plataforma o sistema operativo de origen. |
| `product_cat` | STRING | `Electrónica` | Categoría del producto relacionada con el evento, cuando aplica. |
| `price` | FLOAT | `1250.00` | Precio del producto asociado al evento, cuando aplica. |
| `currency` | STRING | `USD` | Moneda del precio registrado. |
| `referral_source` | STRING | `organic` | Fuente de tráfico de la sesión. |
| `event_date` | DATETIME | `2025-08-09 11:03:54` | Fecha y hora legible del evento. |
| `year` | INTEGER | `2025` | Año en que ocurrió el evento. |

### Tabla `mercadolibre_retention`

Esta tabla contiene información sobre registros y actividad posterior de los usuarios.

| Columna | Tipo de dato | Ejemplo | Descripción |
| --- | --- | --- | --- |
| `user_id` | STRING | `00198c1f-bc1e-403e-b46a-67b0b3ac7657` | Identificador único del usuario. |
| `signup_date` | DATE | `2025-05-01` | Fecha de registro del usuario. |
| `signup_datetime` | DATETIME | `2025-05-01 18:02:25` | Fecha y hora exactas del registro. |
| `country` | STRING | `Brazil` | País del usuario al momento de registrarse. |
| `device_category` | STRING | `mobile` | Dispositivo utilizado por el usuario. |
| `platform` | STRING | `android` | Plataforma desde la que accedió el usuario. |
| `day_after_signup` | INTEGER | `8` | Días transcurridos desde el registro hasta la actividad. |
| `activity_date` | DATE | `2025-05-09` | Fecha en la que el usuario presentó actividad. |
| `active` | INTEGER | `1` | Indicador de actividad: `1` para activo y `0` para inactivo. |
| `prob_active` | FLOAT | `0.1697` | Probabilidad estimada de actividad del usuario. |

## 🧭 Macro Journey: embudo general

Definí el análisis alrededor del recorrido principal de compra de MercadoLibre.

| Etapa | Evento | Descripción | Métrica principal |
| --- | --- | --- | --- |
| 🟢 Descubrimiento | `first_visit` | El usuario ingresa por primera vez al sitio o aplicación. | Usuarios nuevos o sesiones nuevas. |
| 🟡 Interés / Consideración | `select_item` / `select_promotion` | El usuario selecciona un producto o promoción. | Tasa de clics e intención inicial. |
| 🟠 Intención de compra | `add_to_cart` | El usuario añade un producto al carrito. | Proporción de usuarios que agregan al carrito. |
| 🔵 Inicio de compra | `begin_checkout` | El usuario inicia el proceso de checkout. | Proporción de carritos que inician la compra. |
| 🟣 Información de envío | `add_shipping_info` | El usuario incorpora información de envío. | Proporción de checkouts con envío completado. |
| 🟤 Información de pago | `add_payment_info` | El usuario agrega o selecciona un método de pago. | Proporción de checkouts con pago iniciado. |
| 🔴 Conversión / Compra | `purchase` | El usuario completa la compra. | Conversión final del proceso. |

## 💼 Preguntas de negocio

Durante el proyecto busqué responder dos grupos de preguntas.

### Embudo de conversión

- ¿En qué etapa del proceso se pierden más usuarios?
- Entre el 1 de enero y el 31 de agosto de 2025, ¿cuál es la tasa de conversión entre cada etapa clave?
- ¿Cuál es el paso con mayor caída porcentual?
- ¿Cómo cambia la pérdida de usuarios según el país?
- ¿Cómo se comportan las etapas por dispositivo y fuente de referencia?

### Retención por cohortes

- ¿Qué tan bien se retiene a los usuarios después de registrarse?
- Para usuarios registrados entre el 1 de enero y el 1 de junio de 2025, ¿cuál es la retención en D7, D14, D21 y D28?
- ¿Cómo cambia la retención entre países?
- ¿Qué cohortes muestran mejores o peores resultados a lo largo del tiempo?

## 📏 Métricas principales

| Métrica | Fórmula conceptual | Propósito |
| --- | --- | --- |
| Conversión entre etapas | Usuarios de la etapa siguiente / usuarios de la etapa anterior | Medir el avance relativo entre dos eventos consecutivos. |
| Drop-off | 1 − tasa de conversión entre etapas | Identificar la proporción de usuarios que no continúa al siguiente paso. |
| Conversión final | Usuarios con `purchase` / usuarios con `first_visit` | Medir la proporción agregada entre descubrimiento y compra. |
| Retención D7 | Usuarios activos entre D1 y D7 / usuarios iniciales de la cohorte | Medir actividad posterior al registro durante la primera semana. |
| Retención D14 | Usuarios activos entre D8 y D14 / usuarios iniciales | Evaluar actividad en la segunda semana. |
| Retención D21 | Usuarios activos entre D15 y D21 / usuarios iniciales | Evaluar actividad en la tercera semana. |
| Retención D28 | Usuarios activos entre D22 y D28 / usuarios iniciales | Evaluar actividad en la cuarta semana. |

## 🔄 Proceso de análisis

Organicé el proyecto en cinco etapas principales.

| Etapa | Qué hice | Resultado |
| :---: | --- | --- |
| 1 | Exploré el esquema y las tablas base. | Comprendí el nivel de detalle de eventos y actividad. |
| 2 | Construí el embudo de conversión. | Organicé los eventos en una secuencia de compra. |
| 3 | Calculé conversiones y caídas. | Identifiqué los principales puntos de fuga. |
| 4 | Analicé la retención por cohortes. | Comparé actividad en D7, D14, D21 y D28. |
| 5 | Preparé el informe ejecutivo. | Tradují las métricas en prioridades de producto y retención. |

## 🔍 Paso 1: Exploración del esquema

Antes de calcular las métricas, revisé las tablas disponibles y validé su nivel de detalle.

En `mercadolibre_funnel`, revisé:

- Eventos disponibles.
- Usuarios únicos.
- Sesiones.
- Fechas de los eventos.
- Países.
- Dispositivos.
- Plataformas.
- Fuentes de referencia.
- Categorías de producto.

En `mercadolibre_retention`, revisé:

- Usuarios registrados.
- Fecha de registro.
- Fechas de actividad.
- Días transcurridos desde el registro.
- Indicador binario de actividad.
- País, dispositivo y plataforma.

Esta exploración fue importante para diferenciar:

- Un evento de compra.
- Una sesión.
- Un usuario.
- Una actividad posterior al registro.
- Una cohorte semanal o mensual.

## 🧮 Paso 2: Construcción del embudo

Construí un embudo multietapa con consultas SQL y CTEs.

El objetivo fue organizar los eventos de compra en una secuencia comparable.

### Etapas consideradas

```text
first_visit
→ select_item / select_promotion
→ add_to_cart
→ begin_checkout
→ add_shipping_info
→ add_payment_info
→ purchase
```

Para cada evento:

- Conté usuarios únicos.
- Organicé los resultados por etapa.
- Calculé conversiones entre pasos.
- Calculé el drop-off correspondiente.
- Comparé las métricas entre países.
- Consideré segmentaciones por dispositivo y fuente de tráfico.

### Lógica del embudo

La tasa de conversión entre etapas se calculó con la siguiente lógica:

```text
Conversión entre etapas =
Usuarios de la etapa siguiente / Usuarios de la etapa anterior
```

La caída se interpretó como:

```text
Drop-off =
1 − Conversión entre etapas
```

## 📉 Paso 3: Análisis de conversiones y fugas

Una vez construido el embudo, evalué dónde se concentraban las principales pérdidas de usuarios.

Para cada transición, comparé:

- Usuarios de la etapa inicial.
- Usuarios de la etapa siguiente.
- Tasa de conversión.
- Tasa de caída.
- Diferencias por país.
- Diferencias por dispositivo.
- Diferencias por fuente de referencia.

### Ejemplo de estructura de salida

| Transición | Usuarios etapa inicial | Usuarios etapa siguiente | Conversión | Drop-off |
| --- | ---: | ---: | ---: | ---: |
| `first_visit` → `select_item` | Usuarios | Usuarios | Porcentaje | Porcentaje |
| `select_item` → `add_to_cart` | Usuarios | Usuarios | Porcentaje | Porcentaje |
| `add_to_cart` → `begin_checkout` | Usuarios | Usuarios | Porcentaje | Porcentaje |
| `begin_checkout` → `add_shipping_info` | Usuarios | Usuarios | Porcentaje | Porcentaje |
| `add_shipping_info` → `add_payment_info` | Usuarios | Usuarios | Porcentaje | Porcentaje |
| `add_payment_info` → `purchase` | Usuarios | Usuarios | Porcentaje | Porcentaje |

## 👥 Paso 4: Retención por cohortes

Para analizar si los usuarios regresaban después de registrarse, construí cohortes basadas en la fecha de registro.

Cada usuario fue asignado a una cohorte según su semana o periodo de registro.

Después, medí la actividad en ventanas de tiempo posteriores.

| Periodo | Ventana analizada |
| --- | --- |
| D7 | Días 1 a 7 posteriores al registro. |
| D14 | Días 8 a 14 posteriores al registro. |
| D21 | Días 15 a 21 posteriores al registro. |
| D28 | Días 22 a 28 posteriores al registro. |

### Estructura de la tabla de cohortes

| Cohorte | Usuarios iniciales | Retención D7 | Retención D14 | Retención D21 | Retención D28 |
| --- | ---: | ---: | ---: | ---: | ---: |
| Semana de registro | Usuarios de la cohorte | Porcentaje | Porcentaje | Porcentaje | Porcentaje |

El análisis de cohortes permitió comparar:

- Cohortes con mayor actividad posterior.
- Cohortes con menor retención.
- Diferencias de actividad entre países.
- Posibles oportunidades de onboarding, notificaciones o recompensas.

## 📊 Paso 5: Informe ejecutivo C → F → I

Organicé los resultados mediante el enfoque:

> Contexto → Hallazgo → Implicación

### Contexto

Expliqué qué recorrido de producto analicé, el periodo cubierto y las fuentes utilizadas.

El análisis incluyó eventos del embudo de compra y actividad posterior al registro de usuarios.

### Hallazgos

Presenté los principales resultados relacionados con:

- La etapa con mayor pérdida de usuarios.
- Las tasas de conversión entre eventos.
- La conversión final hacia compra.
- Las cohortes con mejor retención.
- Las cohortes con mayor pérdida de actividad.
- Las diferencias observadas por país, dispositivo o fuente de referencia.

### Implicaciones

Relacioné cada hallazgo con una acción potencial para el producto.

| Hallazgo posible | Implicación de negocio |
| --- | --- |
| Mayor caída durante checkout | Revisar fricciones de envío, pago, información o confianza. |
| Menor conversión en mobile | Analizar experiencia móvil, velocidad y formularios. |
| Baja retención en D7 | Mejorar onboarding, recordatorios y activación temprana. |
| Retención reducida en una cohorte o país | Revisar comunicaciones, producto y contexto local. |
| Diferencias por fuente de tráfico | Contrastar calidad de adquisición y comportamiento posterior. |

## 🧪 Simulación de mejoras

Como parte del análisis, consideré escenarios para evaluar cómo una mejora hipotética en conversión o retención podría afectar el resultado.

Estos escenarios permiten responder preguntas como:

- ¿Qué ocurriría si se reduce el drop-off de una etapa?
- ¿Cuántas compras adicionales podrían generarse?
- ¿Qué impacto tendría una mejora en D7 sobre la actividad posterior?
- ¿Qué etapa debe priorizarse primero para obtener mayor impacto potencial?

La simulación sirve para priorizar hipótesis, pero no reemplaza un experimento controlado.

## ✅ Validaciones de calidad

Documenté controles para asegurar que las métricas fueran interpretables y trazables.

| Validación | Pregunta de control |
| --- | --- |
| Identificadores de usuario | ¿Los usuarios se cuentan como únicos dentro de cada etapa? |
| Sesiones | ¿Se diferencia correctamente entre usuario y sesión? |
| Eventos | ¿Los nombres de eventos corresponden a etapas válidas del embudo? |
| Fechas | ¿Los eventos están dentro del periodo analizado? |
| Orden temporal | ¿Los eventos mantienen una secuencia lógica cuando se analiza el funnel? |
| Cohortes | ¿La cohorte se asigna con la fecha de registro correcta? |
| Actividad | ¿`active = 1` se utiliza correctamente para la retención? |
| Denominadores | ¿Las tasas utilizan usuarios iniciales consistentes? |
| País y dispositivo | ¿Las segmentaciones mantienen suficientes observaciones? |
| Interpretación | ¿Las recomendaciones distinguen correlación, comportamiento observado y causalidad? |

## 📦 Entregables

| Entregable | Contenido |
| --- | --- |
| Consultas SQL | Consultas con CTEs para embudo, conversiones, drop-off y cohortes. |
| Tabla de embudo | Usuarios, conversiones y pérdidas entre etapas. |
| Tabla de cohortes | Retención en D7, D14, D21 y D28. |
| Visualizaciones | Gráficos de embudo, conversiones y retención por cohortes. |
| Informe ejecutivo | Hallazgos e implicaciones mediante C → F → I. |
| Simulación | Escenarios de mejora de conversión o retención. |
| Validaciones QA | Controles sobre eventos, fechas, usuarios y denominadores. |

## 💭 Reflexión final

Este proyecto me permitió comprender que el análisis de producto no consiste únicamente en contar compras o visitas.

Durante el desarrollo:

- Organicé eventos de usuarios en un embudo de conversión.
- Identifiqué las transiciones donde se concentran las pérdidas.
- Medí el comportamiento posterior al registro mediante cohortes.
- Diferencié conversión, retención, actividad y compra.
- Comparé métricas entre segmentos.
- Organicé los hallazgos para facilitar decisiones de producto.

La principal enseñanza fue que una caída de usuarios no explica automáticamente su causa. Una fuga observada en checkout puede relacionarse con pagos, envíos, precio, experiencia de usuario, confianza o disponibilidad, pero requiere una investigación adicional para confirmarlo.

El análisis de cohortes también mostró la importancia de observar a los usuarios a lo largo del tiempo. La adquisición inicial es importante, pero la sostenibilidad del producto depende de que los usuarios regresen, encuentren valor y mantengan actividad después de registrarse.
