# SARA360 — Paquete de encargo para Codex

Versión 1.0 · 6 de octubre de 2026.

## Qué contiene

Este paquete convierte la idea de una plataforma municipal de gestión estratégica basada en SARA, ampliada a problemas digitales y redes sociales, en un encargo técnico implementable. **No contiene todavía el software ni acredita que se haya construido, desplegado o auditado.**

`ESPECIFICACION.md` es el documento maestro autónomo: visión, alcance, metodología, roles, módulos, fórmulas, arquitectura, modelo de datos, despliegue, datos ficticios, hitos y fuentes.

`AGENTS.md` contiene las instrucciones persistentes y breves del repositorio. `PRUEBAS_ACEPTACION.md` define 60 escenarios de aceptación que Codex debe convertir en comprobaciones. `CASOS_NUMERICOS.json` aporta fixtures de cálculo. `GUION_DEMO.md` define el recorrido que debe ser posible mostrar en la aplicación.

`PROMPT_INICIO.md` contiene el texto de arranque. `PROMPT_CONTINUACION.md` sirve para retomar el trabajo. Estos archivos evitan depender de una única conversación o de recordar requisitos que salgan del contexto.

## Cómo utilizarlo

1. Crea una carpeta de proyecto o un repositorio dedicado. Descomprime el paquete en su raíz; no lo pongas dentro de la carpeta de otro proyecto ajeno. Si hay un `AGENTS.md` preexistente, integra las reglas y conserva las instrucciones legítimas ya presentes, en vez de sobrescribirlo sin revisión.
2. Abre esa carpeta o repositorio en la modalidad de Codex que utilices, con permisos para leer, escribir y ejecutar las herramientas de desarrollo necesarias. La instalación de Codex y las credenciales de tu cuenta son independientes de la aplicación que se va a construir.
3. Pega el texto de `PROMPT_INICIO.md`. Verifica que Codex está leyendo los archivos del repositorio y programando, no solo describiendo el proyecto.
4. Cuando exista una entrega de código, utiliza el `README.md` que Codex haya generado. Los scripts `scripts/demo.sh` y `scripts/demo.ps1` deben haberse creado y probado; no existen en este paquete de encargo.
5. Antes de enseñar la demo, recorre `GUION_DEMO.md` y revisa `docs/VALIDACION.md` y las pruebas. No uses datos policiales reales para probarla.

Codex documenta `AGENTS.md` como mecanismo de instrucciones de proyecto. Mantener ese archivo breve y remitir a especificación y tests reduce el riesgo de truncar instrucciones extensas. Fuente: OpenAI, https://developers.openai.com/codex/guides/agents-md (consultado el 6 de octubre de 2026).

## Decisiones asumidas

Primera configuración: España, español, EUR y Europe/Madrid. Público: ayuntamientos y cuerpos pequeños/medianos. Nombre provisional: SARA360. Metodología ampliada propuesta: SARA-D. Arquitectura: Django y PostgreSQL, interfaz servida por el mismo backend. Despliegue: Docker local y perfil compartido con HTTPS y autenticación. El nombre y el diseño no implican reconocimiento oficial.

Todo el núcleo SARA, la planificación institucional, la orientación, los recursos, la evaluación y los informes son obligatorios en la primera versión. Se han dejado fuera conectores reales, IA generativa externa y producción con datos reales, para que la demo no dependa de cuentas, contratos o autorizaciones externas.

## Cómo distinguir una entrega válida de una maqueta

Debes poder crear un problema, cerrar el navegador, volver a entrar y encontrar los cambios; aprobar una respuesta; ver que se recalculan los recursos; registrar una evaluación; y generar un PDF con los datos correctos. El rol político no puede recuperar detalles operativos mediante otra URL. Los datos no deben desaparecer al reiniciar contenedores conservando volúmenes.

Las pruebas deben mostrar qué comandos se ejecutaron y cuáles fallaron o quedaron pendientes. Una captura de una pantalla, un alto porcentaje de cobertura o una frase «todo listo» no sustituyen esas comprobaciones.

## Uso responsable

El paquete diseña una demo funcional, no una autorización para usar información policial. La puesta en producción exige revisión profesional de competencias, protección de datos, seguridad, continuidad y, en su caso, inteligencia artificial. Los ejemplos, pesos y resultados son ficticios o propuestos; no demuestran eficacia preventiva real.
