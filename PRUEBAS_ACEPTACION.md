# SARA360 — Catálogo de aceptación P0

Versión 1.0 · 6 de octubre de 2026.

Estos 60 escenarios son **requisitos de prueba**, no resultados de pruebas ejecutadas. Codex debe automatizar lo indicado, ejecutar en su entorno y registrar resultados reales. Ningún escenario autoriza datos reales, exposición pública automática o relajación de controles.

Cada caso debe poder asociarse a un archivo de test y a un resultado. Usa fixtures deterministas. Los casos numéricos de `CASOS_NUMERICOS.json` son un oráculo independiente; los resultados esperados no se importan al código de cálculo. Nunca hagas que el test invoque la misma fórmula que pretende comprobar para construir el resultado esperado.

## A. Construcción, arranque y persistencia

### T01 — Arranque desde cero

Referencia: secciones 13 y 18. Tipo: integración/Compose.

Dado un clon limpio, Docker/Compose disponibles, puerto 8000 libre y ningún volumen previo, al ejecutar el script de demo correspondiente deben construirse imágenes, generarse secretos, aplicarse migraciones, cargar datos y alcanzar readiness. El script termina con código 0 solo si funciona; muestra URL y ubicación de credenciales. No requiere Python o Node en el host.

### T02 — Scripts de ambos entornos

Referencia: 18. Tipo: automatización y revisión.

Comprueba sintaxis, errores y rutas con espacios de `scripts/demo.sh` y `scripts/demo.ps1`. Ejecuta cada uno en su plataforma cuando esté disponible. La ausencia de Windows se declara `NO_VERIFICADO_EN_WINDOWS`, no éxito. Ningún script cambia globalmente la política de ejecución, sobrescribe secretos existentes o elimina volúmenes sin petición explícita.

### T03 — Persistencia tras reinicio

Referencia: 14 y 18. Tipo: integración.

Crea un problema y modifica una acción desde la UI. Reinicia web; después detén y levanta Compose sin eliminar volúmenes. Los valores, relaciones, versiones y auditoría permanecen. No se restauran datos de seed por encima de lo editado. El caso falla si los datos solo existían en memoria, localStorage o una respuesta simulada.

### T04 — Seed idempotente y coherente

Referencia: 17. Tipo: servicio/base de datos.

Ejecuta dos veces `seed_demo` con igual anclaje. A mantiene 12 problemas y 900 observaciones; B, 3 y 120. Los registros de negocio no se duplican y una edición humana intermedia no se pierde. El manifiesto coincide con la base; las fases A contienen 2 S, 3 A, 4 R y 3 evaluación/cierre.

### T05 — Funcionamiento sin red externa

Referencia: 12, 13 y 18. Tipo: navegador/red.

Con imágenes construidas y seed cargado, bloquea tráfico externo del navegador y contenedor de aplicación, preservando red interna a la base. Login, paneles, formularios, biblioteca, gráficos y PDF funcionan. Registra solicitudes de red: no hay CDN, fuentes externas, llamadas de IA, mapas remotos ni telemetría. No confundas este resultado con instalación offline desde cero.

### T06 — Healthchecks y fallos de configuración

Referencia: 18. Tipo: integración.

Readiness devuelve estado no disponible al perder conexión a la base o existir migraciones pendientes, sin divulgar credenciales o trazas. Liveness distingue proceso vivo. Prueba contraseña de DB incorrecta, puerto ocupado y configuración inválida: el arranque informa la causa operativa sin éxito ficticio y sin registrar secretos.

### T07 — Backup y restauración

Referencia: 18. Tipo: integración.

Tras guardar problemas y publicaciones, genera una copia mediante el script documentado y restáurala en un entorno aislado. Comprueba conteos, relaciones, roles y contenido autorizado. Nunca restaures sobre una instalación ajena. Documenta los archivos de configuración/secretos necesarios y que el backup no se añade a Git.

## B. Autorización, gobierno y aislamiento

### T08 — Autenticación real

Referencia: 3 y 15. Tipo: integración/navegador.

Una petición anónima a cualquier ruta de negocio se redirige a login o se deniega sin datos. Inicio con credenciales válidas crea sesión; cerrar sesión la revoca. Una cookie manipulada y un identificador de usuario en parámetros no autentican. Las contraseñas del seed son aleatorias y no están en el repositorio.

### T09 — Aislamiento de detalle entre municipios

Referencia: 3 y 14. Tipo: autorización parametrizada.

Como usuario de A solicita UUID válidos de problemas, acciones, indicadores, evaluaciones, imports y publicaciones de B por GET y POST. Todas las rutas rechazan consistentemente sin devolver contenido, existencia distinguible, títulos o conteos. Repite con cabecera HTMX y endpoints JSON. No basta probar identificadores inexistentes.

### T10 — Aislamiento en relaciones y escrituras

Referencia: 14. Tipo: servicio/base de datos.

Intenta vincular una observación B a un problema A, una acción A a una unidad B y una evaluación A a un indicador B. Prueba formulario y servicio directo. La operación se rechaza íntegramente, no crea relaciones parciales y registra el fallo cuando corresponda sin filtrar contenido de B.

### T11 — Buscador, listas y gráficos

Referencia: 3 y 19. Tipo: integración.

Incluye texto centinela exclusivo de B y un problema restringido no asignado al oficial A. Busca ese texto y consulta filtros, sugerencias, totales y gráficos desde A/oficial. No se devuelve el centinela ni se filtra información por agregados o caché. Repite después de cambiar de usuario y organización en el mismo navegador.

### T12 — Restricción del oficial a lo asignado

Referencia: 3. Tipo: integración/navegador.

Un oficial puede editar avances de una tarea asignada y consultar su contexto permitido. No puede acceder a otro problema restringido, reasignarse privilegios, aprobar respuesta, cerrar problema o publicar información ejecutiva. Los intentos directos de POST se deniegan aunque se fuerce la visualización del botón.

### T13 — Separación de administrador y jefatura

Referencia: 3 y 15. Tipo: autorización.

El administrador organizativo gestiona cuentas conforme a la matriz de delegación, pero no consulta contenido operativo restringido ni se concede a sí mismo rol policial. La jefatura aprueba operaciones; los cambios de rol sensibles necesitan la aprobación definida. Cada acción tiene auditoría. La cuenta técnica no es automáticamente un superusuario de negocio.

### T14 — Vista política minimizada

Referencia: 3 y 16. Tipo: integración y payload.

Carga identificadores centinela en notas, referencias y canales operativos; publica una instantánea ejecutiva revisada. Como rol político inspecciona HTML, JSON, respuestas de gráficos, CSV y PDF. Ningún centinela ni campo restringido se transmite. No se permite consultar expediente directo, editar hipótesis o aprobar medidas operativas.

### T15 — Grupos pequeños y retirada

Referencia: 3. Tipo: integración.

Prepara una publicación con celdas de 2, 8 y total 10. La salida no permite deducir la celda 2 mediante el total y la celda 8; aplica supresión complementaria. Rechaza filtros arbitrarios. Tras retirar la publicación, el rol político no la obtiene por lista, URL, PDF o caché. No describas esta prueba como demostración de anonimato absoluto.

### T16 — Permisos de auditor y concesiones

Referencia: 3 y 15. Tipo: integración.

El auditor ve metadatos autorizados y no texto operativo restringido salvo concesión explícita. Revoca esa concesión y comprueba denegación inmediata en nuevas solicitudes. El auditor no puede alterar o borrar eventos. Los eventos contienen referencia, actor, acción y tiempo, no copias extensas del contenido sensible.

### T17 — Revocación de cuenta o pertenencia

Referencia: 3 y 15. Tipo: sesión/autorización.

Inicia sesión y después desactiva cuenta o pertenencia. Las solicitudes siguientes dejan de permitir el acceso aunque exista cookie válida. Un usuario con dos municipios no hereda roles de A en B. El cambio de organización exige pertenencia activa y no permite reutilizar selectores o formularios del municipio anterior.

## C. Ciclo SARA y planificación institucional

### T18 — Creación y delimitación guiadas

Referencia: 4 y 6. Tipo: E2E.

Una jefatura crea un objetivo y un problema digital con responsable, alcance, periodo y fuente. El asistente ayuda sin exigir coordenada física. Se guarda un borrador incompleto y se puede retomar. Los campos desconocidos permanecen nulos. El problema queda vinculado al objetivo y la relación es navegable en ambas direcciones.

### T19 — Control de paso S→A

Referencia: 6. Tipo: servicio/UI.

Un problema sin responsable o contexto no puede avanzar. Al añadir los requisitos, se permite análisis. Si la recurrencia no está corroborada, se exige motivo de análisis exploratorio y aviso visible. El mismo bloqueo se aplica al POST directo. La transición guarda revisión y decisión, no solo cambia el color de una pestaña.

### T20 — Hipótesis y evidencia contradictoria

Referencia: 7. Tipo: E2E.

Añade una hipótesis con evidencia favorable y contraria, alternativa e incertidumbre. Guarda, recarga y verifica persistencia. Cambiar su estado a descartada conserva historia. Ninguna puntuación automática declara verdadera una hipótesis, identifica culpables o evalúa credibilidad personal.

### T21 — Control de paso A→R

Referencia: 7 y 8. Tipo: servicio/UI.

Sin hipótesis razonada, factor, competencia o medición preliminar, la respuesta no se activa. Una medida exploratoria permitida requiere motivo, bajo riesgo documentado y aprobación policial. Una acción sin vínculo analítico genera bloqueo o justificación explícita según regla, nunca un paso silencioso.

### T22 — Aprobación operativa y financiación separadas

Referencia: 3 y 8. Tipo: integración.

Registra aprobación de recursos desde el rol político sobre información ejecutiva permitida. El plan operativo sigue pendiente hasta validación policial. Un intento político de aprobación operativa falla. La autoaprobación de jefatura, cuando esté permitida, requiere motivo y se identifica; con regla de doble persona, se bloquea.

### T23 — Competencia y derivación documental

Referencia: 8. Tipo: E2E.

Un problema `UNKNOWN` no adquiere competencia automáticamente por categoría. En `REFER`, la aplicación prepara un documento minimizado de coordinación y deja estado preparado. No aparece enviado o recibido sin registro humano. Los contactos/organismos son ficticios en la demo. La derivación no cierra el problema como resuelto.

### T24 — Acciones, dependencias y finalización

Referencia: 8. Tipo: integración.

Crea acción con responsable, fechas, recursos y dependencia. Rechaza fecha final anterior a inicial y costes negativos no válidos. Una tarea bloqueada explica motivo. Completar exige fecha y entregable o justificación. Cancelar no cuenta como ejecutar. Actualizar avances recalcula paneles autorizados y queda auditado.

### T25 — Evaluación planificada y cierre

Referencia: 9. Tipo: E2E.

El plan no se activa sin pregunta, indicador, línea base o estrategia documentada de obtención, fuentes y periodos. Con evaluación de resultado inconcluso se permite cierre administrativo motivado, sin verde de éxito. Cerrar sin evaluación o sin limitaciones se bloquea. Registrar seguimiento no crea recordatorio externo inexistente.

### T26 — Retorno de fase y revisión

Referencia: 2 y 14. Tipo: integración.

Devuelve un problema de R a A por nueva evidencia. La respuesta aprobada anterior y sus indicadores históricos permanecen. La nueva revisión no reescribe aprobaciones; exige nueva validación para cambios sustantivos. Verifica que la transición y el evento de auditoría se confirman juntos o se revierten juntos.

### T27 — Estrategia más allá de SARA

Referencia: 4. Tipo: E2E.

Crea un proyecto de capacidad de formación vinculado a un programa y objetivo, con hitos y recursos, sin obligarlo a tener hipótesis delictiva. Muestra su contribución separada de resultados preventivos. Una nueva versión del plan conserva la anterior. La revisión muestra objetivos sin métricas y decisiones pendientes con datos reales de la base.

## D. Matemáticas, indicadores y recursos

### T28 — Fórmula de prioridad

Referencia: 6. Tipo: unidad.

Usa el caso de `CASOS_NUMERICOS.json`: H=4, R=3, T=2, E=5 da 70 y categoría alta. Todos ceros da 0; todos cincos da 100. Rechaza dimensiones fuera de rango o pesos negativos/suma distinta de 1. Guarda versión de fórmula. No redondees antes del cálculo final.

### T29 — Prioridad desconocida y decisión humana

Referencia: 6. Tipo: unidad/integración.

Con una dimensión nula el resultado es nulo, no cero ni media de las restantes. La confianza documental se muestra aparte. Modificar el orden requiere motivo y conserva puntuación. Una nueva ponderación genera escenario/revisión sin alterar la decisión aprobada histórica ni incluir variables de reputación política.

### T30 — Variación, reducción y base cero

Referencia: 9. Tipo: unidad.

Para 100→70, cambio −30, variación −30% y reducción 30%. Para 100→120, reducción −20%. Para 0→5, cambio 5 y porcentajes nulos con motivo. Valores faltantes no generan cero. El componente visual respeta la dirección deseable del indicador y no colorea automáticamente de verde todo descenso.

### T31 — Tasas y puntos porcentuales

Referencia: 9. Tipo: unidad.

25 hechos sobre exposición 10.000 con escala 1.000 producen 2,5. Exposición cero o desconocida impide la tasa. Pasar de 20% a 15% representa −5 puntos porcentuales y −25% relativo, correctamente etiquetados. Comprueba unidades en UI, exportaciones y comparación, sin reinterpretar decimales como enteros.

### T32 — Diferencias en diferencias

Referencia: 9. Tipo: unidad/UI.

Intervención 100→70 y comparación 80→72 producen −22 unidades. Muestra que el cálculo es descriptivo por sí solo, no causalidad probada. Sin comparador, exposición comparable o ventanas compatibles no calcula una cifra equivalente inventada. El informe conserva las limitaciones que muestra la pantalla.

### T33 — Periodos, duplicados y fuente de verdad

Referencia: 5, 9 y 14. Tipo: integración.

Con dos publicaciones sobre un mismo registro de hecho, la métrica de hechos no se duplica. Un panel de observaciones sí etiqueta su propia unidad. Ventanas de 30 y 60 días no se comparan como recuentos equivalentes sin normalización. Todos los gráficos permiten identificar fuente, periodo y cobertura autorizados.

### T34 — Capacidad, desconocidos y sobrecarga

Referencia: 8. Tipo: unidad/UI.

Disponibilidad 160 horas, obligaciones 100 y ausencias 20 dan 40 horas planificables. Una carga aprobada de 50 genera exceso 10 y uso 125%. Con capacidad cero y carga positiva, muestra sobrecarga sin división por cero. Con capacidad desconocida no asume disponibilidad. Rechaza entradas que produzcan capacidad negativa.

### T35 — Escenarios y costes compartidos

Referencia: 4 y 8. Tipo: unidad/integración.

Una acción de 1.000 EUR repartida 60/40 entre dos objetivos suma 1.000 al municipio, 600/400 a objetivos. Rechaza reparto total 90% o 110%. Modificar un escenario no altera presupuesto aprobado. Para promoverlo exige decisión y nueva versión. No convierte el cambio de presupuesto en predicción de reducción de delitos.

## E. Señales digitales e importación

### T36 — Importación válida

Referencia: 5. Tipo: E2E.

Importa CSV UTF-8 con BOM y sin él, coma y punto y coma, con vista previa. El mapa de columnas, fechas y categorías se interpreta de forma visible. Confirmar crea los registros y el resumen del lote. Una columna `organization_id` no puede fijar tenant ni evadir la sesión; campos no admitidos se informan.

### T37 — Errores y atomicidad

Referencia: 5. Tipo: integración.

Un archivo contiene fecha inválida, categoría desconocida y exceso de longitud. El modo atómico guarda cero registros y devuelve fila/columna/motivo. El modo parcial solo actúa tras confirmación expresa y devuelve aceptados/rechazados. Archivos mayores de 5 MB o 10.000 filas se rechazan sin lectura ilimitada o fallo de memoria.

### T38 — Reimportación sin sobrescritura

Referencia: 5. Tipo: integración.

Reimporta un lote con la misma fuente e identificadores externos. No duplica ni revierte una corrección humana. Si cambia contenido se informa conflicto y se requiere decisión autorizada para revisión. Registros sin ID usan advertencia de huella, no una fusión automática potencialmente errónea.

### T39 — Verificación de señales y corrección

Referencia: 5 y 10. Tipo: E2E.

Una publicación no revisada sigue siendo señal aunque el CSV la llame corroborada. Una revisión humana con motivo puede corroborar o descartar. La descartada no alimenta métricas de hechos corroborados, pero conserva su historia autorizada. El volumen de viralidad no sube por sí solo la prioridad policial.

### T40 — URL no recuperada y contenido no confiable

Referencia: 10 y 15. Tipo: seguridad/red.

Introduce una URL externa, una URL hacia servicios internos y texto con instrucciones de «ignorar permisos». No se hace petición de red ni se obedecen instrucciones. La referencia se presenta como texto seguro. El contenido se trata como datos y no cambia roles, configuración, consultas o conducta del asistente.

### T41 — Biblioteca digital completa

Referencia: 10 y 11. Tipo: contenido/integración.

Existen al menos las doce plantillas definidas, con objetivo, mecanismo, competencia pendiente de validación, acciones, indicadores de proceso/resultado, riesgos y límites. Copiar una plantilla crea borrador propio y no aprueba actuaciones. Los resultados ficticios no se citan como eficacia empírica ni se inventan investigaciones.

### T42 — Comunicación preventiva y límites

Referencia: 10. Tipo: E2E/revisión.

Crea un borrador informativo contra falsas multas, revisa hechos y registra aprobación. Exportar no publica nada fuera. Las métricas de alcance no equivalen a delitos evitados. No existen funciones de rastreo masivo, perfil ideológico, seguimiento de críticos, reconocimiento facial o predicción individual, ni menús que las presenten como futuras opciones habilitables.

## F. Informes y salidas de datos

### T43 — PDF SARA operativo

Referencia: 16. Tipo: integración/visual.

Genera un expediente demo de varias páginas. El PDF es válido, seleccionable y contiene las cuatro fases, datos reales de la base, autor, organización, periodo, clasificación, versión y limitaciones. Revisa tablas, saltos y acentos. No es una captura PNG renombrada ni un archivo vacío.

### T44 — Resumen ejecutivo y cartera

Referencia: 3 y 16. Tipo: integración/visual.

Genera los otros dos tipos de informe. El ejecutivo coincide con la publicación aprobada, incluida fecha de corte, y no incorpora cambios operativos posteriores no publicados. La cartera calcula recursos sin doble conteo y distingue proyectos de capacidad. No contiene campos restringidos en texto, metadatos o adjuntos.

### T45 — Exportación sometida a permisos

Referencia: 3, 15 y 16. Tipo: autorización.

Intenta exportar problemas ajenos, datos de B y campos restringidos desde rol político/oficial. La petición se deniega antes de construir el archivo. Los filtros autorizados se reflejan en contenido. No se crean enlaces públicos ni ficheros persistentes recuperables después de retirar permiso.

### T46 — Fórmulas maliciosas en CSV

Referencia: 15. Tipo: seguridad/unidad.

Exporta campos textuales que empiezan por `=`, `+`, `-`, `@` y prefijos de espacios/control. El lector de hoja de cálculo no debe ejecutarlos como fórmula. Los campos numéricos tipados negativos conservan significado. Reimportar el archivo informa cualquier transformación necesaria, sin ejecutar comandos o fórmulas.

### T47 — Coherencia panel–archivo

Referencia: 9 y 16. Tipo: integración.

Aplica un periodo y filtro autorizado, calcula valores y exporta. Tabla de pantalla, CSV y PDF coinciden en unidad, cobertura, redondeo y cifras. «Sin datos» permanece sin datos. El ejecutivo usa su instantánea propia, no se compara erróneamente con cifras operativas posteriores.

### T48 — Auditoría de consultas y exportaciones

Referencia: 15 y 16. Tipo: integración.

Leer un objeto restringido y exportarlo genera eventos con usuario, organización, ID, resultado, hora real y filtros no sensibles. Un error de generación no se registra como exportación exitosa. No aparecen credenciales ni texto completo de señales en logs o diffs. La aplicación no permite editar eventos.

## G. Orientación y experiencia de uso

### T49 — Asistente de reglas explicable

Referencia: 11. Tipo: unidad/UI.

Activa cada regla mínima con una fixture incompleta. La sugerencia muestra identificador, motivo, campos y siguiente paso. Al corregir datos se actualiza. Sin información suficiente no recomienda una intervención como eficaz. La interfaz lo denomina asistente metodológico, no IA generativa cuando no existe tal proveedor.

### T50 — Modos guiado y experto

Referencia: 11 y 12. Tipo: E2E.

Un usuario crea y retoma un borrador en modo guiado; al pasar a experto conserva datos y permisos. Ejemplos se identifican como ficticios y no se guardan como hechos sin decisión. Las ayudas explican línea base, hipótesis e indicadores sin bloquear el trabajo por jerga.

### T51 — Navegación por teclado y errores

Referencia: 12. Tipo: navegador y revisión manual.

Revisa login, asistente SARA, aprobación y exportación con teclado. Foco visible, etiquetas asociadas, errores anunciables y controles operables. Los gráficos tienen tabla equivalente. Los estados no dependen solo del color. Los resultados automáticos de accesibilidad no se presentan como certificación plena.

### T52 — Responsive y estados vacíos

Referencia: 12. Tipo: navegador/visual.

Comprueba al menos 1440×900, 1024×768 y 390×844. No hay controles críticos fuera de pantalla ni desbordamiento horizontal general. Tablas pueden tener scroll accesible localizado. Prueba organización sin datos, filtro sin resultados, fallo de servidor y permiso denegado: todos muestran mensaje y acción útiles, no números inventados.

### T53 — Fecha, moneda y datos ficticios

Referencia: 1, 12 y 17. Tipo: unidad/UI.

Comprueba EUR, decimales, acentos, fecha española y Europe/Madrid, incluida transición de horario. La fecha anclada del escenario no sustituye el reloj de seguridad/auditoría. Toda vista e informe mantienen distintivo de datos sintéticos. No se afirma que la población o los incidentes correspondan a un municipio real.

## H. Robustez, mantenimiento y entrega

### T54 — Concurrencia optimista

Referencia: 14. Tipo: integración/E2E.

Dos usuarios abren la misma revisión. El primero guarda; el segundo intenta guardar o aprobar su versión antigua. Se informa conflicto y se conserva su entrada para resolverla, sin sobrescribir cambios. La aprobación de contenido modificado exige nueva revisión. Prueba también el endpoint sin navegador.

### T55 — CSRF, XSS y métodos HTTP

Referencia: 15 y 19. Tipo: seguridad.

Rechaza POST sin CSRF donde corresponda; GET no altera fases, aprobaciones, sesiones o archivos. Texto con etiquetas y manejadores JS queda escapado, también en HTMX y reportes. Redirecciones no autorizadas se rechazan. Las cookies y cabeceras se ajustan al perfil local/HTTPS sin dejar DEBUG público.

### T56 — Intentos de acceso y secretos

Referencia: 15. Tipo: seguridad/revisión.

Repetidos intentos fallidos activan limitación controlada sin enumerar usuarios. Revísense repositorio, imagen y logs para detectar claves universales, secretos o credenciales de demo. Los secretos se generan por instalación y no cambian al reiniciar. No hay un endpoint que permita seleccionar un rol omitiendo login.

### T57 — Conservación y purga

Referencia: 15. Tipo: servicio.

Crea registros vencidos, vigentes y sujetos a retención. El modo seco informa candidatos sin borrar. Confirmar elimina o minimiza solo lo autorizado, preserva retenciones y conserva auditoría mínima sin reconstruir texto eliminado. No elimina objetos de otro tenant ni usa un plazo universal presentado como obligación legal.

### T58 — Reset de demo acotado

Referencia: 17. Tipo: servicio/integración.

Con `DEMO_MODE=0`, organización no sintética, nombre de confirmación erróneo o un registro no sintético, reset se rechaza. Con datos demo válidos solo reinicia el municipio elegido, registra nueva generación y preserva trazas mínimas. B permanece sin cambios al resetear A. No existe botón destructivo público.

### T59 — Escala de referencia y comprobación de consultas

Referencia: 1, 13 y 19. Tipo: rendimiento diagnóstico.

Ejecuta el dataset demo y una fixture de 10.000 observaciones en el entorno documentado. Registra latencias y número de consultas de listados/paneles; aplica paginación e índices y corrige cargas completas innecesarias. No impongas un benchmark inventado ni declares rendimiento en hardware no probado. Ninguna pantalla devuelve todo el dataset al cliente sin necesidad.

### T60 — Entrega verificable y límites

Referencia: 20–23. Tipo: revisión final.

Existen todos los entregables, comandos de arranque y pruebas, dependencias fijadas, migraciones, documentación, capturas reales y guía demo. `docs/VALIDACION.md` distingue PASA/FALLA/NO_VERIFICADO y entorno. No hay P0 fingido, botones inertes, fuentes inventadas o declaración de cumplimiento certificado. El resultado no se anuncia listo para datos policiales reales.

## Formato de informe que debe producir Codex

Para cada T01–T60 registra ID, archivo/caso automatizado o procedimiento manual, fecha, entorno, comando, resultado, evidencia y limitación. Usa `PASA`, `FALLA`, `NO_VERIFICADO` o `NO_APLICA_JUSTIFICADO`; no uses el último para eludir un requisito P0. Si un caso tiene partes verificadas y pendientes, desglósalas.

La aceptación funcional exige superar los casos de aplicación y corregir defectos bloqueantes. Las verificaciones dependientes de una plataforma no disponible se dejan expresamente pendientes. Una demo compartida no debe abrirse a terceros hasta probar sus controles de acceso y configuración de red en el entorno de destino.
