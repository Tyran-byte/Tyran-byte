<!-- aprendizaje-pasado 2026-10-29: generado por el obrero aprendizaje-pasado (dev-bridge); se puede corregir a mano -->

# POR QUÉ

> Aclaración importante: este repo («Tyran-byte») es el repositorio de perfil de GitHub. Tiene un solo archivo, `README.md`, y 5 commits. No contiene el código de los sistemas: los describe. Todo lo que sigue sale de ese README y de su historial, y son afirmaciones del dueño sobre repos privados que no pude ver.

## Qué es

Es una carta de presentación técnica. Está dirigida a quien evalúa al dueño: reclutadores, equipos de ingeniería y clientes institucionales. Resuelve el problema de mostrar trabajo real cuando el código está bajo NDA y no se puede publicar. Para eso resume qué hay en cada sistema privado, con números verificables (migraciones, tests, casos de evaluación) y con una pieza pública que sí se puede auditar (`deterministic-evals`).

Los sistemas que describe son:
- Una plataforma de gestión educativa en producción desde agosto de 2026, con unos 600 usuarios y 5 roles.
- Dos asistentes basados en modelos de lenguaje (LLM), con guardas probadas.
- Un agente de voz local.
- Infraestructura personal de IA, con un servidor MCP en Cloudflare Workers.
- Una plataforma heredada en Google Apps Script que sigue en producción.
- Código de laboratorio de la tesis de ingeniería química.

## Con qué está hecho y por qué

El repo en sí es solo Markdown, que sirve porque GitHub lo muestra como portada del perfil. Las tecnologías de los sistemas descritos son:

- **React 19 + TypeScript:** interfaz tipada, para detectar errores antes de ejecutar.
- **Supabase (PostgreSQL) + funciones edge en Deno:** base relacional con seguridad a nivel de fila y de columna, y lógica de servidor sin operar servidores propios. El README no explicita el motivo; es una inferencia razonable.
- **Python + asyncio (agente de voz):** concurrencia para encadenar streaming de tokens, oraciones y TTS. SQLite con FTS5 guarda la memoria local. Ollama corre los modelos locales.
- **Cloudflare Workers + MCP:** exponer repos privados como herramientas para claude.ai con un servicio remoto liviano.
- **Google Apps Script:** es la plataforma heredada. Sigue porque está en producción con más de 550 usuarios.
- **pytest:** pruebas automáticas, también en el laboratorio de la tesis.

## Decisiones grandes

1. **Evaluar los LLM sin otro LLM como juez.** (README; commits `f3c8843` y `1eae28b`.)
   - *Qué:* cuatro evaluadores deterministas y un contrato de código de salida que distingue una regresión real de una corrida no concluyente.
   - *Por qué:* un juez-LLM es no determinista y puede equivocarse igual que lo evaluado. Con reglas fijas el resultado es reproducible y puede bloquear un merge.
   - *Riesgo:* las reglas solo detectan lo que alguien pensó de antemano. Los evaluadores fallan a propósito una negativa parafraseada, lo que es estricto pero asegura que el texto canónico venga de la guarda.

2. **Guardas antes y después del modelo.** (README; `f3c8843`.)
   - *Qué:* un clasificador determinista rechaza pedidos de datos personales, SQL crudo y órdenes de ignorar instrucciones antes de llamar al modelo. Una verificación posterior en el servidor, que falla cerrada, atrapa el resto.
   - *Por qué:* no se puede confiar en que el modelo se autocontrole.
   - *Riesgo:* falsos positivos que bloqueen consultas legítimas, y ataques nuevos que ninguna regla prevea. Por eso hay umbrales: golden ≥90% y adversarial 100%.

3. **Seguridad en la base y no solo en la aplicación.** (README; `f3c8843`.)
   - *Qué:* RLS por rol y seguridad por columna sobre datos personales. Se encontraron y cerraron 40 funciones internas que se podían llamar sin autenticar.
   - *Por qué:* son datos de escuelas, personal y estudiantes. Si falla la aplicación, la base igual protege.
   - *Riesgo:* las políticas son complejas, y un error de política es difícil de ver sin pruebas.

4. **ETL con aserciones generadas desde el catálogo de datos.** (README; `254ec2e`.)
   - *Qué:* 23 pasos desde Google Sheets, con unas 280 aserciones de integridad generadas, no escritas a mano. Los 88 fallos de paridad se resolvieron arreglando el pipeline, nunca relajando una aserción.
   - *Por qué:* las planillas son una fuente propensa a errores, y el catálogo evita que las pruebas se desincronicen de los datos.
   - *Riesgo:* si el catálogo está mal, las aserciones heredan el error. Además, mantener el rigor tiene costo en velocidad.

5. **Migraciones versionadas y mucha prueba.** (`1eae28b`: 178 migraciones, 299 archivos de test.)
   - *Qué:* cada cambio de esquema queda como migración, con tests muy extensos.
   - *Por qué:* el sistema está en producción con usuarios reales.
   - *Riesgo:* el mantenimiento de tantos tests, y que los números sean una métrica de volumen y no de calidad.

6. **Failover entre 5 proveedores de LLM y memoria en 3 capas** (agente de voz).
   - *Por qué:* la disponibilidad no depende de un solo proveedor, y corriendo local se evita enviar todo a la nube.
   - *Riesgo:* el comportamiento varía entre modelos, y la complejidad del enrutamiento es mayor.

7. **Servidor MCP con doble autenticación resistente a ataques de tiempo y escrituras con compuerta.**
   - *Por qué:* da acceso remoto a repos privados, por lo que cada escritura es una superficie de riesgo.
   - *Riesgo:* una credencial filtrada o una compuerta mal configurada expone código privado.

8. **Mantener la plataforma heredada en Apps Script** (bloqueo pesimista, sincronización bidireccional de 5 pasos).
   - *Por qué:* está en producción con más de 550 usuarios en unas 1.200 escuelas, así que migrar tiene riesgo operativo.
   - *Riesgo:* límites de la plataforma y deuda técnica de unas 40.000 líneas.

9. **Publicar solo el núcleo del evaluador.** (README; `6110f27`.)
   - *Por qué:* demuestra el método sin violar el NDA.
   - *Riesgo:* el resto no se puede auditar, así que los números son declarativos.

## Conceptos para poder defenderlo

- **LLM como pieza y prompt injection** (`llm-como-pieza-y-prompt-injection`) — `README.md: clasificador previo y verificación posterior en los asistentes`
- **Fallar cerrado** (`fail-closed`) — `README.md: verificación posterior en el servidor (commit f3c8843)`
- **Seguridad a nivel de fila (RLS)** (`rls`) — `README.md: RLS por rol y seguridad por columna en la plataforma educativa`
- **Autenticación vs autorización** (`autenticacion-vs-autorizacion`) — `README.md: 40 funciones internas llamables sin autenticar, cerradas`
- **Vistas y permisos (grants)** (`vistas-y-grants`) — `README.md: seguridad por columna sobre datos personales`
- **Base de datos y SQL** (`base-de-datos-y-sql`) — `README.md: 178 migraciones de PostgreSQL`
- **Migraciones de base de datos** (`migraciones`) — `commit 1eae28b: 178 migraciones`
- **Tests automáticos** (`tests-y-tdd`) — `commit 1eae28b: 299 archivos de test`
- **Pipeline de integración y compuerta de merge** (`ci-cd-pipeline`) — `README.md: el evaluador bloquea merges con código de salida distinto de cero`
- **Git: commit, rama y merge** (`git-commit-rama-merge`) — `git log de este repo (5 commits)`
- **ETL y pipelines por pasos** (`tareas-programadas`) — `commit 254ec2e: ETL de 23 pasos desde Google Sheets`
- **Fuente única de verdad (catálogo de datos)** (`fuente-unica-de-verdad`) — `README.md: aserciones generadas desde el catálogo de datos`
- **Concurrencia y bloqueos** (`concurrencia-y-locks`) — `README.md: bloqueo pesimista en la plataforma de Apps Script; asyncio en el agente de voz`
- **Google Apps Script** (`apps-script`) — `README.md: plataforma heredada de unas 40.000 líneas`
- **Credenciales, tokens y secretos** (`credenciales-tokens-y-secretos`) — `README.md: servidor MCP con doble autenticación`
- **Despliegue y hosting (Workers, edge)** (`deploy-y-hosting`) — `README.md: MCP en Cloudflare Workers y funciones edge en Deno`
- **Cliente y servidor** (`cliente-servidor`) — `README.md: servidor MCP remoto consumido desde claude.ai`
- **Frontend y aplicación de una página** (`frontend-y-spa`) — `README.md: React 19 y TypeScript`
- **Evaluación determinista sin LLM como juez** (`evaluacion-determinista-sin-llm-juez`) — `README.md: repo público deterministic-evals y 110 casos`
- **Failover entre proveedores con cooldown exponencial** (`failover-entre-proveedores`) — `README.md: agente de voz con 5 proveedores de LLM`

## Qué no revisé

- Ningún código de los sistemas privados, que no están en este repo.
- El repo público `deterministic-evals`.
- Que las cifras del README (migraciones, tests, usuarios, casos) sean ciertas. Solo vi que se actualizaron en `254ec2e` y `1eae28b`.
- Los motivos reales de cada decisión. Los inferí del texto y no hay DECISIONES.md, CLAUDE.md, docs ni .planning en este repo.
- Configuración de despliegue, CI y secretos: no existen aquí y no busqué ninguno.
