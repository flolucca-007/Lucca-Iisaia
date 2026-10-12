# Prompts: TP 2 (API de ensayos y labores)

Registro del proceso, en orden. Herramienta: Claude, en una sola conversación. Antes hice un brainstorming con ChatGPT para evaluar la viabilidad y el alcance de la idea, sin generar archivos; de ahí salieron las reglas del prompt 1.

El prompt inicial se transcribe completo. Los prompts de ajuste se presentan con su contenido técnico.

---

## 1. Prompt inicial

```
Estoy realizando el TP2 de Ingeniería de Software Asistida por IA. Ya hice un brainstorming previo con ChatGPT y tomé las decisiones que resumo abajo. Quiero que me ayudes a elaborar y revisar el contrato, manteniendo mi participación en las decisiones.

CONSIGNA
Entregar una carpeta tp2/ con:
- openapi.yaml.
- prompts.md: prompts reales en orden, con una explicación breve de qué buscaba con cada uno.
- README.md: propósito de la API, decisiones propias, problemas encontrados y correcciones.

El contrato debe incluir GET, POST y DELETE, una jerarquía de recursos visible en el path, al menos un error 400 o 404 documentado y schemas con type, required y format donde corresponda. No hay que implementar servidor, base de datos ni interfaz.

PROYECTO
Gestión coordinada de ensayos agronómicos de la EEA Balcarce y de las labores que realiza la Cooperadora. Cada ensayo tiene un lote asignado, identidad propia y ubicación georreferenciada. Dos ensayos contiguos del mismo cultivo son entidades distintas.

Esta API será una primera etapa de una posible aplicación interna con mapa, identificación por QR, historial de manejo y acceso a datos para análisis.

ALCANCE ACTUAL
Ensayos y solicitudes de labores: preparación del terreno, siembra/plantación, riego, fertilización y aplicaciones generales de fitosanitarios.

Antes de la implantación puede haber preparación general de un sector; después, las solicitudes y registros de manejo corresponden a cada ensayo. Reconocer el antecedente general sin introducir ahora un sistema de gestión territorial.

Quedan fuera: parcelas, tratamientos experimentales, evaluaciones privadas, cálculos agronómicos, integración meteorológica y generación automática de alertas. Son posibles ampliaciones.

RESPONSABILIDADES
- El investigador o un especialista autorizado solicita la labor.
- La Cooperadora coordina y designa al agente ejecutor.
- El agente registra cuándo y cómo se realizó.
- La Cooperadora puede validar la ejecución, dejando persona y fecha de validación.
- Distinguir ejecutor de persona que carga el registro, y fecha efectiva de fecha de carga.
- Los permisos detallados quedan para la implementación futura.

REGLAS ACORDADAS
- Estados básicos: pendiente, realizada y cancelada.
- La validación de la Cooperadora se registra por separado y solo procede sobre una labor realizada.
- Cada solicitud tiene una fecha límite de ejecución.
- Los plazos se cuentan en días corridos, incluidos fines de semana y feriados.
- Se considera un máximo general de 3–4 días desde la solicitud, con plazos menores cuando la labor lo requiera. Falta elegir entre 3 y 4: no lo decidas silenciosamente.
- Si vence sin ejecución, corresponde intervención de la Cooperadora para acordar un plan B. Documentar la regla; no implementar alertas.
- Conservar la fecha límite original, el impedimento y la alternativa acordada si hay reprogramación.
- DELETE elimina únicamente solicitudes pendientes cargadas por error, como duplicados.
- Una solicitud real que deja de necesitarse se cancela con motivo y permanece en el historial.
- Las labores realizadas y las solicitudes canceladas no se eliminan por DELETE.
- Propuesta de respuestas para DELETE: 204 sin cuerpo si se elimina; 404 si no existe dentro del ensayo; 409 si su estado impide eliminarla.

DETALLES DE LAS LABORES
La información solicitada y la efectivamente ejecutada pueden diferir y deben distinguirse.
Ejemplo ilustrativo de riego: lámina solicitada de 20 mm; registrar después la lámina aplicada, el método y cualquier diferencia. No calcular necesidades hídricas.
Para otras labores, proponer una estructura pequeña que permita expresar instrucciones y datos de ejecución sin convertir el TP en un sistema completo.

FORMA DE TRABAJAR
1. Primero resumí el alcance en pocas líneas y proponé una tabla compacta de recursos, campos y operaciones. Marcá qué viene de mis decisiones y qué proponés vos.
2. Hacé como máximo tres preguntas indispensables y esperá mis respuestas antes de generar el YAML. No reabras decisiones ya acordadas.
3. Luego generá un contrato pequeño y coherente, con schemas de entrada y salida separados. Los identificadores generados por el servidor no van en la entrada; el identificador del ensayo del path no se repite en el body.
4. Incluí una forma de actualizar solicitudes para registrar ejecución, cancelación y validación; PATCH puede añadirse a los métodos mínimos.
5. Revisemos después jerarquía, tipos, unidades, fechas, transiciones, pertenencia al ensayo, errores y ejemplos.
6. No inventes fallos, correcciones, pruebas ejecutadas ni decisiones mías. Si validás con una herramienta, indicá cuál y el resultado; si no, distinguí revisión de validación.
7. Prepará README.md y prompts.md al final, basados en lo que realmente sucedió. Reconocé el brainstorming previo con ChatGPT y no afirmes que todo el proceso ocurrió en este chat.
8. Todos los ejemplos deben ser ficticios y estar identificados como tales. No uses datos personales ni información real de ensayos.
```

**Qué buscaba:** fijar el alcance, las reglas ya decididas y una forma de trabajo en la que el modelo propusiera y preguntara antes de generar, sin decidir por mí.

**Resultado:** resumen del alcance, tabla de recursos, campos y operaciones con el origen de cada decisión, y tres preguntas: plazo máximo, forma de definir la fecha límite y registro de la preparación general de un sector.

---

## 2. Cierre de decisiones

```
Decisiones sobre las preguntas planteadas:
1. Plazo máximo general: 4 días corridos desde la solicitud.
2. Fecha límite: la calcula el servidor como fecha de solicitud más 4 días. El solicitante puede indicar una fecha anterior cuando la labor lo requiera.
3. Preparación general de un sector: solicitud de tipo preparación del terreno con campo alcance (ensayo o sector general) y descripción del sector, cargada en cada ensayo afectado.
Antes de generar el contrato, ordenar el prompt con Prompt Master.
```

**Qué buscaba:** resolver las tres preguntas. Para la fecha límite pedí comparar las dos alternativas y elegí que la calcule el servidor, porque el máximo queda garantizado en un solo lugar y el solicitante solo interviene cuando necesita un plazo menor.

---

## 3. Prompt ordenado con Prompt Master

Apliqué la skill Prompt Master a mi prompt inicial. Este es el prompt resultante:

```
<contexto>
Trabajo práctico 2 de la materia Ingeniería de Software Asistida por IA. Hice un brainstorming previo con ChatGPT y luego cerré las decisiones pendientes en un chat con Claude. Tu tarea es ayudarme a elaborar y revisar un contrato OpenAPI. Las decisiones son mías: proponé, señalá problemas y preguntá, pero NUNCA decidas en silencio ni reabras lo acordado.
</contexto>

<entregable>
Carpeta tp2/ con:
1. openapi.yaml
2. prompts.md: prompts reales en orden, con una explicación breve de qué buscaba con cada uno.
3. README.md: propósito de la API, decisiones propias, problemas encontrados y correcciones.

El contrato DEBE incluir:
1. GET, POST y DELETE; PATCH puede agregarse.
2. Jerarquía de recursos visible en el path.
3. Al menos un error 400 o 404 documentado.
4. Schemas con type, required y format donde corresponda, con entrada y salida separadas.
No se implementa servidor, base de datos ni interfaz.
</entregable>

<dominio>
Gestión coordinada de ensayos agronómicos de la EEA Balcarce y de las labores que realiza la Cooperadora. Cada ensayo tiene identidad propia, lote asignado y ubicación georreferenciada. Dos ensayos contiguos del mismo cultivo son entidades distintas.
Es la primera etapa de una posible aplicación interna con mapa, identificación por QR, historial de manejo y acceso a datos para análisis.

Alcance actual: ensayos y solicitudes de labores de preparación del terreno, siembra o plantación, riego, fertilización y aplicaciones generales de fitosanitarios.
Fuera de alcance (posibles ampliaciones): parcelas, tratamientos experimentales, evaluaciones privadas, cálculos agronómicos, integración meteorológica, alertas automáticas, gestión territorial y permisos detallados.
</dominio>

<responsabilidades>
1. El investigador o un especialista autorizado solicita la labor.
2. La Cooperadora coordina y designa al agente ejecutor.
3. El agente registra cuándo y cómo se realizó.
4. La Cooperadora puede validar la ejecución, con persona y fecha de validación.
5. Se distingue ejecutor de quien carga el registro, y fecha efectiva de fecha de carga.
</responsabilidades>

<decisiones_cerradas>
Estados y transiciones
1. Estados: pendiente, realizada y cancelada.
2. La validación se registra por separado y solo procede sobre una labor realizada.
3. Una solicitud real que deja de necesitarse se cancela con motivo y permanece en el historial.

Plazos
4. Cada solicitud tiene fecha límite. Los plazos se cuentan en días corridos, incluidos fines de semana y feriados.
5. Máximo general: 4 días corridos desde la solicitud.
6. El servidor calcula la fecha límite como fecha de solicitud más 4 días. El solicitante puede indicar una fecha anterior cuando la labor lo requiera; una fecha posterior al máximo se rechaza con 400.
7. Si vence sin ejecución, interviene la Cooperadora para acordar un plan B. Se documenta la regla; no se implementan alertas.
8. Si hay reprogramación se conservan la fecha límite original, el impedimento y la alternativa acordada.

Eliminación
9. DELETE elimina solo solicitudes pendientes cargadas por error, como duplicados.
10. Labores realizadas y solicitudes canceladas no se eliminan.
11. Respuestas: 204 sin cuerpo si se elimina; 404 si no existe dentro del ensayo; 409 si su estado lo impide.

Antecedente de preparación
12. La preparación general de un sector previa a la implantación se registra como solicitud de tipo preparación del terreno, con un campo "alcance" (ensayo o sector general) y una descripción del sector, cargada en cada ensayo afectado.

Detalle de labores
13. Lo solicitado y lo ejecutado pueden diferir y deben distinguirse. Ejemplo de riego: lámina solicitada 20 mm; luego se registra lámina aplicada, método y diferencias. No se calculan necesidades hídricas.
14. Para las demás labores, proponé una estructura pequeña para instrucciones y datos de ejecución, sin convertir el TP en un sistema completo.
</decisiones_cerradas>

<forma_de_trabajo>
1. Resumí el alcance en pocas líneas y proponé una tabla compacta de recursos, campos y operaciones, marcando qué viene de mis decisiones y qué proponés vos.
2. Hacé como máximo tres preguntas, solo si son indispensables, y esperá mis respuestas antes de generar el YAML.
3. Generá un contrato pequeño y coherente. Los identificadores generados por el servidor no van en la entrada; el identificador del ensayo del path no se repite en el body.
4. Incluí una forma de actualizar solicitudes para registrar ejecución, cancelación y validación.
5. Revisemos jerarquía, tipos, unidades, fechas, transiciones, pertenencia al ensayo, errores y ejemplos.
6. Al final prepará README.md y prompts.md según lo que realmente sucedió, reconociendo el brainstorming previo con ChatGPT y sin afirmar que todo ocurrió en un solo chat.
</forma_de_trabajo>

<restricciones>
1. NUNCA inventes fallos, correcciones, pruebas ejecutadas ni decisiones mías.
2. Si validás con una herramienta, indicá cuál y el resultado; si no, decí que es revisión y no validación.
3. Todos los ejemplos DEBEN ser ficticios e identificados como tales. Sin datos personales ni información real de ensayos.
</restricciones>

<listo_cuando>
1. Hay GET, POST y DELETE, y la jerarquía /ensayos/{ensayoId}/... es visible.
2. Hay al menos un 400 o 404 documentado, y DELETE documenta 204, 404 y 409.
3. Ningún schema de entrada contiene identificadores del servidor ni el ensayoId del path.
4. Las 14 decisiones cerradas se reflejan en el contrato o en sus descripciones.
</listo_cuando>
```

**Qué buscaba:** ordenar el prompt e incorporar las decisiones del prompt 2 antes de generar el contrato.

**Resultado:** el prompt quedó organizado en bloques, con las decisiones cerradas numeradas y un criterio de "listo cuando" derivado de la consigna. No cambió reglas ni agregó funcionalidades.

---

## 4. Generar el contrato

```
ADELANTE
```

**Qué buscaba:** generar el `openapi.yaml` a partir del prompt del punto 3.

**Resultado:** contrato OpenAPI 3.0.3 con cuatro paths y ocho operaciones. Validó con openapi-spec-validator y los ejemplos pasaron contra sus schemas. El modelo marcó seis puntos propuestos por él para que yo los confirmara.

---

## 5. Revisión contra la consigna

```
Revisar el contrato y el entregable contra la consigna oficial del TP 2 en el repositorio de la materia, para cumplir todos los requisitos.
```

**Qué buscaba:** asegurar que el trabajo cumpliera la consigna para obtener la máxima nota.

**Resultado:** el contrato cumple los requisitos del yaml. La revisión mostró que en el README pesan especialmente las decisiones propias y los problemas encontrados, lo que llevó a revisar qué permitía el contrato que mis reglas no aceptan. Aparecieron tres huecos, detallados en el [README](README.md).

---

## 6. Corrección de huecos y confirmación de propuestas

```
Correcciones:
1. El alcance sector general solo vale para labores de preparación del terreno.
2. Con alcance sector general, la descripción del sector es obligatoria.
3. No se puede registrar la ejecución de una labor sin agente designado; responde 409.

Propuestas confirmadas:
4. Una reprogramación puede fijar una fecha que supere los 4 días, porque es el plan B acordado.
5. Crear un ensayo con un código repetido responde 409.
6. El producto y otros datos no numéricos de una labor van en observaciones.
```

**Qué buscaba:** que el contrato rechace lo que mis reglas no aceptan, y convertir en decisiones las propuestas del modelo que acepté.

**Resultado:** la entrada de labores pasó a tener un `oneOf` con dos variantes válidas, y el `PATCH` sumó la regla de `409` sin agente asignado. Nueva validación: estructura OK, 12 ejemplos OK y 6 casos de prueba de la regla de alcance con el resultado esperado. El schema anterior aceptaba los casos inválidos, lo que confirma el error.
