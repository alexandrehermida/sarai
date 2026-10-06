# Prompt para iniciar el trabajo en Codex

Copia el bloque siguiente en Codex después de colocar los archivos de este paquete en la raíz del repositorio de trabajo. No es necesario pegar toda la especificación en el chat.

---

Construye en este repositorio SARA360, la aplicación descrita en `ESPECIFICACION.md`. Lee íntegramente `AGENTS.md`, `ESPECIFICACION.md`, `PRUEBAS_ACEPTACION.md`, `CASOS_NUMERICOS.json` y `GUION_DEMO.md` antes de empezar.

Necesito código ejecutable y probado, no solo arquitectura, un frontend simulado o un plan. El producto es un gestor estratégico de seguridad para ayuntamientos y policías pequeñas y medianas. Debe implementar el ciclo SARA completo, su ampliación propuesta a problemas digitales y redes sociales, estrategia, recursos, evaluación, orientación para usuarios no especialistas y separación entre vista policial y vista política.

Inspecciona el repositorio, conserva el trabajo existente y resuelve detalles no esenciales con supuestos documentados. Si está vacío, crea la aplicación desde cero con el monolito Django definido. No cambies de stack por preferencia ni rebajes el alcance P0.

Ejecuta los hitos H0–H5. Empieza por la base y un recorrido completo de falsas multas por SMS; luego amplía módulos. Mantén trazabilidad requisito → código → prueba en `docs/PROGRESO.md`. Implementa persistencia, permisos de servidor, control de versiones, importación/exportación, datos ficticios, scripts Docker y pruebas de navegador. Sigue corrigiendo hasta superar las pruebas que puedas ejecutar.

La demo debe arrancar mediante los scripts que construirás, sin claves de APIs ni servicios de pago. Los conectores externos no son P0 y no deben aparecer conectados. El asistente P0 es de reglas y plantillas. No utilices datos reales, no crees perfiles de ciudadanos, no automatices decisiones operativas y no publiques servicios externos.

Al finalizar, aporta comandos exactos de arranque y pruebas, ubicación de las credenciales generadas, resultados reales de validación, recorrido de demo y limitaciones. No declares pruebas superadas si no las has ejecutado. Si el entorno carece de Docker o navegador, documenta el bloqueo y deja una comprobación reproducible, sin sustituirla por una afirmación de éxito.

Comienza ahora con la inspección y la implementación.

---

Los comandos de instalación y despliegue citados en la especificación son entregables que Codex debe construir. Este paquete contiene el encargo, no la aplicación ya programada.
