**Autora:** Florencia Lucca

**Materia:** Introducción a la Ingeniería de Software Asistida por IA (ISAIA) — Maestría en Inteligencia Artificial

**Trabajo Práctico N°1**

# Evaluación de Impacto Ambiental EIQ — Programas de Fungicidas en Papa

## Objetivo pedagógico

Este trabajo pone en práctica el rol de arquitecta/supervisora de un 
agente de IA: especificar con precisión los requisitos (incluyendo una 
interfaz deliberadamente incómoda pero funcional), verificar la salida 
generada, y corregir desvíos detectados (ver `Lucca-Isaia_prompts.md`).

## Descripción del proyecto

Aplicación web de una sola página (single-file HTML) que compara el impacto 
ambiental de tres programas de fungicidas aplicados en distintos lotes de un 
mismo productor, para el control de tizón tardío en cultivo de papa, usando 
el indicador **EIQ Field Use Rating (FUR — Field Use Rate)**, una expresión 
del EIQ (Environmental Impact Quotient) ajustada según el uso real a campo.


## Sobre el dataset

Los tres lotes representan programas de fungicidas **ficticios y 
demostrativos**, diseñados para ilustrar un rango de impacto ambiental 
(bajo, medio y alto) según el indicador EIQ-FUR. No corresponden a un caso 
real ni constituyen una recomendación agronómica.

## Metodología EIQ Field Use Rating

El **Environmental Impact Quotient (EIQ)** es un indicador desarrollado por 
Kovach et al. (1992) para cuantificar el impacto ambiental relativo de 
plaguicidas, integrando componentes de toxicidad para el trabajador rural, 
el consumidor y el ambiente (ecológico).

**Fórmula del EIQ:**

``` 
EIQ = {C[(DT×5)+(DT×P)] + [(C×(S+P)/2×SY)+(L)] + [(F×R) +
(D×(S+P)/2×3) + (Z×P×3) + (B×P×5)]} / 3 
```

Donde: DT = toxicidad dérmica, C = toxicidad crónica, SY = sistemicidad, 
F = toxicidad para peces, L = potencial de lixiviación, R = potencial de 
pérdida superficial, D = toxicidad para aves, S = vida media en suelo, 
Z = toxicidad para abejas, B = toxicidad para artrópodos benéficos, 
P = vida media en superficie foliar.

**Fórmula del EIQ Field Use Rating (valor usado en esta app):**

``` 
EIQ Field Use Rating = EIQ × % de ingrediente activo × dosis aplicada 
```

Los valores de EIQ base de cada ingrediente activo fueron obtenidos de la 
lista pública de Cornell University (Cornell Pesticide Management 
Education Program): https://cornell.app.box.com/v/eiq-pesticide-list

Fuente metodológica original: Kovach, J., Petzoldt, C., Degni, J., & 
Tette, J. (1992). *A Method to Measure the Environmental Impact of 
Pesticides.* New York State IPM Program, Cornell University.

Los valores de EIQ Field Use Rating de cada producto/aplicación fueron 
calculados previamente por Florencia Lucca y cargados en la aplicación como 
datos fijos (no se calculan dinámicamente dentro del código).

**Nota sobre el producto biológico:** el valor de EIQ asignado a Bexfond 
(Corteva, *Bacillus velezensis*) es una aproximación demostrativa, dado 
que la lista estándar de Cornell fue diseñada para agroquímicos sintéticos 
y no cubre productos biológicos de forma estandarizada.

## Cálculo del impacto acumulado

El EIQ Field Use acumulado de cada lote es la suma de los EIQ Field Use 
de todas las aplicaciones realizadas en ese lote durante la campaña. El 
ranking ordena los lotes de menor a mayor impacto acumulado (menor EIQ = 
mejor desempeño ambiental relativo).

## Sobre el diseño de la interfaz

Como parte de la consigna del TP1, la interfaz fue diseñada 
**deliberadamente con fricciones de uso** (navegación obligatoria por 
todos los lotes antes de acceder al ranking, ranking oculto tras una 
etiqueta genérica, sin vista comparativa directa). La aplicación es 
completamente funcional y los cálculos son correctos; lo que se dificultó 
a propósito es el camino para llegar a la información, no la información 
en sí. El detalle del proceso de diseño está documentado en `Lucca-Isaia_prompts.md`.

## Cómo usar la aplicación

1. Abrir el archivo `Lucca-Isaia_app.html` directamente en cualquier navegador (no 
   requiere conexión a internet ni instalación).
2. Navegar entre las pestañas de Lote A, Lote B y Lote C para ver el 
   detalle de aplicaciones de cada uno.
3. Tras visitar los tres lotes, se habilita el botón "Más información", 
   que muestra el ranking comparativo.

## Tecnología

- HTML, CSS y JavaScript vanilla, integrados en un único archivo.
- Sin frameworks, librerías externas ni dependencias de CDN.
- Sin backend: funciona abriendo el archivo directamente en el navegador.

## Desarrollo

La aplicación fue desarrollada mediante interacción iterativa con un 
agente de IA (Gemini Canvas), en 2 rondas de prompts. El historial 
completo de prompts utilizados, junto con las verificaciones realizadas 
en cada iteración, se encuentra en `Lucca-Isaia_prompts.md`.

## Archivos incluidos

- `Lucca-Isaia_app.html` — aplicación funcional.
- `Lucca-Isaia_prompts.md` — historial de prompts utilizados con el agente de IA.
- `Lucca-Isaia_README.md` — este archivo.