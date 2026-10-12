# TP 2: API de ensayos y labores (EEA Balcarce)

Un `openapi.yaml` que describe una API para coordinar los ensayos agronómicos de la EEA Balcarce y las solicitudes de labores que ejecuta la Cooperadora. Tiene ocho operaciones en cuatro paths y no hay nada implementado: el entregable es el contrato.

## Cómo se lee

Pegar el contenido de [openapi.yaml](openapi.yaml) en [editor.swagger.io](https://editor.swagger.io). Del lado derecho aparece la documentación navegable, con cada endpoint desplegable y sus ejemplos. Todos los ejemplos son ficticios: no hay datos personales ni información de ensayos reales.

## Qué me propuse construir

La EEA Balcarce es la Estación Experimental Agropecuaria del INTA (Instituto Nacional de Tecnología Agropecuaria) ubicada en Balcarce, provincia de Buenos Aires, donde se conducen ensayos agronómicos a campo. Las labores de campo de esos ensayos las realiza la Cooperadora de la Estación.

En ese circuito, el investigador pide una labor (un riego, una fertilización, una aplicación) y la Cooperadora designa a alguien para hacerla. Este contrato describe ese circuito: quién pide, quién ejecuta, quién carga el registro, qué se pidió y qué se hizo realmente.

El TP es el punto de partida de una aplicación real de manejo de ensayos para la Estación, que pienso completar aprovechando lo aprendido en la materia. Las etapas siguientes son un mapa de ensayos, la identificación por QR en el campo, el historial de manejo y el acceso a datos para análisis. Por eso el contrato cubre un recorte chico pero real, y deja afuera a propósito parcelas, tratamientos, evaluaciones, cálculos agronómicos, meteorología, alertas y permisos detallados.

## Cómo trabajé

Antes de empezar hice un brainstorming con ChatGPT para evaluar la viabilidad y el alcance de la idea, sin generar ningún archivo. De ahí salieron las reglas que llevé al primer prompt. El contrato se construyó completo en una sola conversación con Claude.

Dentro de esa conversación usé la skill Prompt Master para ordenar el prompt de arranque. Reorganizó el texto e incorporó las decisiones ya tomadas, pero no cambió el contenido.

## Endpoints

| Método | Path | Respuestas |
|--------|------|------------|
| GET | `/ensayos` | 200 |
| POST | `/ensayos` | 201, 400, 409 |
| GET | `/ensayos/{ensayoId}` | 200, 404 |
| GET | `/ensayos/{ensayoId}/labores` | 200, 400, 404 |
| POST | `/ensayos/{ensayoId}/labores` | 201, 400, 404 |
| GET | `/ensayos/{ensayoId}/labores/{laborId}` | 200, 404 |
| PATCH | `/ensayos/{ensayoId}/labores/{laborId}` | 200, 400, 404, 409 |
| DELETE | `/ensayos/{ensayoId}/labores/{laborId}` | 204, 404, 409 |

## Decisiones que tomé yo

**Anidar `labores` dentro de `ensayos`.** Cada labor se hace sobre un ensayo concreto y forma parte de su historial de manejo. Dos ensayos contiguos del mismo cultivo son entidades distintas, aunque compartan lote, y cada uno tiene su propia historia. Una labor suelta, sin ensayo, no tiene sentido en este modelo.

**`404` si la labor existe pero pertenece a otro ensayo.** Es la consecuencia de anidar: `/ensayos/A/labores/X` no puede devolver una labor del ensayo B.

**Plazo máximo de 4 días corridos, y la fecha límite la calcula el servidor.** Los plazos se cuentan en días corridos, incluidos fines de semana y feriados. Elegí 4 y no 3 porque, con 3, una solicitud del viernes vence el lunes con el fin de semana adentro. El servidor calcula la fecha para que el investigador no tenga que hacerlo, y el máximo vive en un solo lugar. El solicitante solo envía una fecha cuando la labor necesita un plazo menor, y si manda una fecha más lejana recibe `400`.

**Cancelar no es eliminar.** Una solicitud real que deja de necesitarse se cancela con motivo y queda en el historial. `DELETE` existe solo para limpiar solicitudes pendientes cargadas por error, como un duplicado.

**`409` y no `400` cuando el estado impide la operación.** Si alguien intenta borrar una labor realizada, el pedido está bien formado; lo que lo impide es el estado del recurso. `400` lo reservo para cuerpos o parámetros inválidos.

**La validación de la Cooperadora va aparte del estado.** Los estados son tres (pendiente, realizada, cancelada). La validación es un registro adicional que solo procede sobre una labor realizada, con persona y fecha.

**Separar quién hizo de quién cargó, y cuándo se hizo de cuándo se cargó.** Quien realiza la labor y quien carga el registro pueden ser personas distintas, y en días distintos. Por eso existen `ejecutor` y `registradoPor`, y `fechaEfectiva` y `fechaRegistro`.

**Lo solicitado y lo ejecutado usan la misma estructura, separada.** En un riego se pide una lámina de 20 mm y se registra la que realmente se aplicó, el método y la diferencia. No calculo necesidades hídricas.

**La preparación general de un sector es una labor con `alcance: sector_general`.** Antes de implantar se prepara un sector entero, pero no quise introducir gestión territorial. La solución fue registrar esa preparación en cada ensayo afectado, así queda en su historial.

**Reprogramar conserva el historial y puede superar los 4 días.** Si una labor vence sin ejecutarse, la Cooperadora acuerda un plan B. El contrato guarda la fecha límite anterior, el impedimento y la alternativa. La nueva fecha puede pasar el máximo general porque es un acuerdo explícito. No hay alertas: la regla queda documentada.

**Propuestas del modelo que acepté:** actualizar con un único `PATCH` que lleva un campo `accion` (asignar, registrar ejecución, cancelar, validar, reprogramar), una acción por llamada; responder `409` si se crea un ensayo con un código repetido; guardar los datos numéricos de una labor como `parametros` (nombre, valor, unidad) y lo no numérico, como el producto, en `observaciones`.

## Qué salió mal y cómo lo corregí

El primer contrato validaba sin errores, pero dejaba pasar tres cosas que mis reglas no aceptan.

1. Se podía pedir un riego con alcance `sector_general`. Yo había decidido que ese alcance era solo para preparación del terreno, pero esa regla quedó escrita en el `description` y no en el schema.
2. Se podía cargar alcance `sector_general` sin describir el sector. Pasaba lo mismo: "obligatoria si el alcance es sector general" figuraba solo como texto.
3. Se podía registrar la ejecución de una labor sin que la Cooperadora hubiera designado un agente.

Los tres huecos los marcó el modelo como puntos a confirmar, al entregar el contrato y en la revisión posterior. Yo decidí que eran errores y que había que corregirlos. Para no quedarnos con la lectura, el modelo comprobó con un validador que el schema de entrada anterior aceptaba los casos 1 y 2.

El origen no era el modelo sino mis instrucciones. Las dos primeras reglas las expresé en prosa, y el modelo las trasladó como prosa. La tercera nunca la dije: describí las responsabilidades en orden (la Cooperadora designa, el agente ejecuta) y di por sentado que ese orden era obligatorio.

Las correcciones fueron dos. En la entrada de labores, un `oneOf` separa dos variantes válidas: alcance ensayo con cualquier tipo, o alcance sector general solo con preparación del terreno y con la descripción del sector obligatoria. Lo demás responde `400`. La tercera regla no se puede expresar en un schema porque depende del estado de la labor, así que quedó como regla del `PATCH`: sin agente asignado, registrar la ejecución responde `409`.

Lo que me llevo: una regla escrita en un `description` es una nota para quien lee, no una restricción para quien valida. Y el orden en que cuento un proceso no es una regla hasta que lo escribo como tal.

## Verificación

| Qué | Herramienta | Resultado |
|-----|-------------|-----------|
| Estructura del contrato | openapi-spec-validator 0.9.0 | OK |
| Render del contrato | editor.swagger.io | Abre sin errores |
| 12 ejemplos contra sus schemas | openapi-schema-validator | 12 OK |
| Regla de alcance, 6 casos (3 válidos, 3 inválidos) | openapi-schema-validator | Los 6 se comportan como se espera |
| Schema anterior con los casos 1 y 2 | openapi-schema-validator | Los aceptaba, lo que confirma el error |

No se verificó con herramientas: los formatos de fecha en detalle, el cálculo de plazos ni las transiciones de estado. Esas reglas dependen del servidor y solo fueron revisadas leyendo el contrato. No hay servidor, así que no hay pruebas de ejecución.

## Prompts

El registro completo está en [prompts.md](prompts.md). El prompt inicial fijó el alcance y las reglas. Los que más decidieron fueron el cierre de decisiones (plazo de 4 días, fecha límite calculada por el servidor, alcance del antecedente) y el último, con la corrección de los tres huecos.
