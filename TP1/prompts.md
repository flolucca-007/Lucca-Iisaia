# Historial de Prompts — TP1 IISAIA

## Prompt 1 — Versión inicial funcional

**Objetivo:** generar la app funcional con datos de EIQ hardcodeados, sin mala UX todavía.

**Prompt utilizado:**

TAREA

Creá una aplicación web para comparar el impacto ambiental de programas de fungicidas aplicados para el control del tizón tardío en distintos lotes de papa de un productor durante una campaña o ciclo de cultivo, usando el indicador EIQ Field Use Rating (Environmental Impact Quotient).

TECNOLOGÍA

Un único archivo HTML que integre HTML, CSS y JavaScript (single-file).

Vanilla JavaScript, sin frameworks ni librerías externas.

Debe funcionar abriendo el archivo directamente en el navegador, sin backend ni build.

DATOS

La app debe usar EXACTAMENTE estos datos hardcodeados en el código. No inventes ni modifiques valores.

Productor: "Establecimiento Demostrativo"

Lote A:

Aplicación 1: Bexfond (Corteva), EIQ Field Use = 0.8

Aplicación 2: Bexfond (Corteva), EIQ Field Use = 0.8

Aplicación 3: Zorvec Encantia, EIQ Field Use = 11.2

Lote B:

Aplicación 1: Zorvec Encantia, EIQ Field Use = 11.2

Aplicación 2: Infinito (dosis 1 L/ha), EIQ Field Use = 38.7

Aplicación 3: Clorotalonil, EIQ Field Use = 24.7

Lote C:

Aplicación 1: Mancozeb (dosis 2 kg/ha), EIQ Field Use = 62.8

Aplicación 2: Infinito (dosis 1.5 L/ha), EIQ Field Use = 58.0

Aplicación 3: Fluazinam, EIQ Field Use = 12.5

IMPORTANTE: estos valores de EIQ Field Use Rating ya están calculados y verificados por mí. No calcules ni estimes valores de EIQ; utilizá únicamente los valores proporcionados.

REGLA DE CÁLCULO

El EIQ Field Use acumulado de cada lote es la suma de los EIQ Field Use de todas las aplicaciones realizadas en ese lote durante la campaña.

Para la comparación entre lotes, un menor EIQ Field Use acumulado representa un menor impacto ambiental.

ESTILO

Simple y funcional, sin necesidad de diseño elaborado o sofisticado. Priorizá que la app funcione correctamente por sobre la estética.

COMPORTAMIENTO ESPERADO

La interfaz debe permitir al usuario explorar los distintos lotes y consultar sus resultados.

Para cada lote, el usuario debe poder ver el listado de aplicaciones realizadas, indicando producto y EIQ Field Use de cada una.

Debe mostrarse el EIQ Field Use acumulado de cada lote.

Debe existir un ranking de los tres lotes, ordenado de menor a mayor impacto ambiental acumulado (menor EIQ = mejor desempeño ambiental).

No agregues sistema de estrellas ni ninguna escala de valoración adicional: solo el valor numérico acumulado y el orden del ranking.

No agregues funcionalidades, indicadores, cálculos ni datos que no estén solicitados.

Generá una primera versión completa de la aplicación en un único archivo HTML. 

**Resultado y verificación:** la app generó correctamente los acumulados 
(Lote A = 12.8, Lote B = 74.6, Lote C = 133.3) y el ranking ordenado A-B-C. 
Detecté que usaba Tailwind CSS vía CDN externo, lo cual contradice el requisito 
de "sin frameworks ni librerías externas" y de funcionamiento standalone sin 
conexión. Se corrige en el Prompt 2.

## Prompt 2 — Corrección técnica + fricciones, combinado

**Objetivo:** corregir dependencia externa detectada + introducir fricciones deliberadas de UX (ranking oculto, navegación obligatoria, sin vista comparativa) según consigna de 'interfaz incómoda pero funcional'

**Prompt utilizado:**

CORRECCIÓN TÉCNICA
Quiero que elimines la dependencia de Tailwind CSS vía CDN y de Google 
Fonts externas. Reescribí todo el CSS necesario embebido en un <style> 
dentro del mismo archivo HTML, usando solo CSS puro (sin librerías ni 
CDNs externos). El resultado visual debe ser equivalente al actual. La 
app debe seguir funcionando 100% offline, abriendo el archivo 
directamente sin conexión a internet.

CAMBIOS DE INTERACCIÓN (deliberados)
A partir de ahora quiero que introduzcas fricciones de uso intencionales. 
La app debe seguir funcionando correctamente y todos los cálculos y datos 
deben permanecer exactamente iguales — solo cambia qué tan fácil es llegar 
a la información, no la información en sí.

1. Quitá de la vista principal la sección de ranking y las barras 
   comparativas. Reemplazala por un botón con la etiqueta "Más información" 
   (sin indicar que ahí está el ranking).

2. El botón de "Más información" debe permanecer deshabilitado (o no 
   generar ningún efecto visible) hasta que el usuario haya visitado 
   individualmente las tres pestañas de lotes (Lote A, Lote B y Lote C) 
   al menos una vez.

3. Eliminá completamente la tabla "Resumen General de Lotes" que muestra 
   todos los lotes juntos. El usuario debe consultar cada lote por 
   separado desde las pestañas.

No agregues ningún otro cambio de diseño, cálculo o dato.

**Resultado y verificación:** confirmé que el archivo generado ya no 
contiene referencias a Tailwind CDN ni Google Fonts (todo el CSS quedó 
embebido en <style>), y que la app abre y funciona igual sin conexión 
a internet. Verifiqué las tres fricciones solicitadas: el botón "Más 
información" aparece deshabilitado por defecto y solo se habilita tras 
visitar las tres pestañas de lotes; al habilitarse, muestra el ranking 
oculto hasta ese momento; la tabla "Resumen General de Lotes" fue 
eliminada correctamente. Los cálculos y el ranking se mantuvieron 
intactos (Lote A = 12.8, Lote B = 74.6, Lote C = 133.3; orden A-B-C).
