Ritual de cierre de sesion de REMATE WEB.

**PRINCIPIO RECTOR (30/07/2026 — el cierre era lento por redactar lo mismo 7 veces):
el handoff es la ÚNICA redacción larga de la sesión. Todo lo demás son filas cortas o
punteros hacia él. NUNCA volver a contar la historia completa en RESUMEN, PROGRESO,
sesiones.md ni project_state.md.**

Ejecuta estos pasos EN ORDEN, sin saltarte ninguno:

## ⚠️ ANTES DE EMPEZAR — ASUMIR QUE HAY OTRA SESION CORRIENDO (obligatorio)

Ivan trabaja con **varias sesiones de Claude en paralelo sobre el MISMO repo y la MISMA
carpeta**. Un cierre descuidado pisa el trabajo de otra sesion o se lo atribuye. Antes de
escribir nada:

- `git fetch origin -q` y `git log --oneline <tu-primer-commit>..HEAD` → **los commits que no
  reconozcas son de otra sesion**. NO los cuentes como tuyos en el handoff ni en el resumen;
  mencionalos aparte ("commits de una sesion paralela") solo si tocan lo mismo que vos.
- `git status` → **NO commitear NADA que no hayas tocado vos**. Lo que no reconozcas es de
  otra sesion o preexistente: se deja intacto y se avisa al final. `git add` SIEMPRE archivo
  por archivo, **NUNCA `git add -A` ni `git add .`**.
- **Antes de sobreescribir `.claude/handoff.md`**: es un archivo compartido y puede tener el
  handoff de otra sesion. Verificar que ya este archivado (`grep -l "<su titulo>"` en la
  carpeta de handoffs) y recien ahi pisarlo; si no esta, archivarlo primero.
- **Editar los archivos de estado/memoria de forma quirurgica**: leerlos JUSTO antes de
  escribir (otra sesion pudo agregar algo hace un minuto), insertar o reemplazar bloques
  puntuales con un script, y NUNCA reescribir el archivo entero desde una copia vieja.
- **El numero de sesion (#NNN)**, si el repo lo usa, sale de leer el archivo en el momento:
  otra sesion pudo cerrar antes y ya haberlo incrementado.
- **Y VERIFICAR DESPUES DE ESCRIBIR, no solo antes**: leer achica la ventana pero no la
  cierra —entre tu lectura y tu escritura la otra sesion puede haber insertado la suya—. Al
  terminar, releer el archivo y comprobar que no quedaron numeros repetidos ni notas
  duplicadas. Paso el 02/09/2026: dos sesiones escribieron la nota #205.
- **Verificar el deploy por commit, no por status**: el health de produccion puede devolver el
  commit de OTRA sesion que deployo despues. Confirmar que el tuyo entro con
  `git merge-base --is-ancestor <tu-commit> <commit-desplegado>`.
- `git fetch` OTRA VEZ justo antes del push. Si falla: `git pull --rebase && git push`.
- **Cerrar el aviso final listando lo que NO tocaste**: archivos sin commitear ajenos y
  commits de otras sesiones. Ivan necesita saber que quedo suelto y de quien es.


0. **Pedir nombre de sesion (SIEMPRE con 3 opciones marcables):**
   - ANTES de hacer cualquier otra cosa, ofrecer el nombre con la herramienta **AskUserQuestion** — NUNCA preguntar abierto pidiendo que Iván escriba.
   - Generar **3 propuestas de nombre** basadas en lo que se hizo en la sesión (descriptivas, no genéricas). Iván solo marca una (o usa "Other" si ninguna le cierra).
   - Reglas: una sola pregunta, header "Nombre sesión", 3 opciones, la más representativa primera.
   - Esperar la selección. Usar ese nombre en el handoff, resumen y commit.
   - NO continuar sin el nombre. NO volver a preguntar abierto: si lo hiciste mal antes, esta línea es la fuente de verdad.

1. **Revisar que cambio en esta sesion:**
   - `git log` desde el ultimo commit que toco docs/estado/RESUMEN.md
   - `git status` para ver si quedan cambios sin commitear
   - Repasar mentalmente la conversacion: que se hizo, que se decidio, que quedo a medio

2. **Generar handoff** en `.claude/handoff.md` — LA redacción canónica y completa de la
   sesión (la única larga). Crear/sobreescribir con esta estructura exacta:

   ```
   # HANDOFF — [fecha Paraguay DD/MM/YYYY] — [nombre sesion]

   ## Completado en esta sesion
   [lista especifica de todo lo que hicimos, con commits]

   ## En progreso (quedo a medias)
   [que quedo incompleto y en que punto exacto]

   ## Proxima sesion — arrancar por aca
   [PRIMER paso exacto, sin ambiguedad]
   [resto en orden de prioridad]

   ## Errores encontrados hoy
   [errores con causa y solucion — o "Ninguno"]

   ## Decisiones tomadas
   [decisiones importantes y por que]

   ## Archivos modificados
   [lista de archivos tocados hoy]
   ```

2b. **Archivar el handoff con timestamp** (NO depender de SessionEnd):
   - Ejecutar: `mkdir -p .claude/handoffs && cp .claude/handoff.md ".claude/handoffs/handoff_$(TZ='Etc/GMT+3' date +%Y%m%d_%H%M).md"`
   - Guardar el nombre del archivo generado: es el **puntero** que usan los pasos siguientes.
   - El hook SessionEnd queda como redundancia — esta logica es la fuente de verdad.

3. **Actualizar `docs/estado/RESUMEN.md`:**
   - Marcar como resuelto lo que ya esta listo; si descubriste cosas estructurales nuevas
     (campos, flows, archivos), actualizar la seccion correspondiente
   - Agregar UNA fila al historial de **máximo 3 líneas**: fecha, commits con hash, una
     frase de qué se hizo, y el puntero `(detalle: .claude/handoffs/handoff_XXXX.md)`.
     **PROHIBIDO el párrafo-ladrillo de 4KB** — el detalle ya vive en el handoff.
   - El historial mantiene **máximo ~15 filas**: si al agregar la nueva quedan más,
     mover las más viejas a `docs/estado/RESUMEN_archivo.md` (cirugía del 02/08/2026:
     235 filas → 15 + archivo).
   - NO reescribas todo el documento — solo lo que cambio

3b. **Actualizar `docs/estado/PROGRESO.md` (bitacora narrativa):**
   - Agregar UNA entrada nueva al inicio (despues del header y la nota de archivo): título
     `## YYYY-MM-DD — [nombre]` + **3-5 líneas máximo** (el qué y el porqué, commits
     referenciados) + el puntero al handoff archivado. La narración completa NO va acá — va
     en el handoff.
   - El archivo mantiene **~1 mes de entradas**: al agregar la nueva, mover las que tengan
     más de un mes a `docs/estado/PROGRESO_archivo.md` (cirugía del 02/08/2026: 144
     entradas → 47 + archivo). Nunca BORRAR — siempre archivar.

3c. **Actualizar `docs/estado/PENDIENTES.md` si surgio algo nuevo:**
   - Si en la sesion aparecio un pendiente nuevo, agregarlo en la seccion que corresponda
   - Si se resolvio un pendiente, BORRAR la linea (no tachar)

4. **Actualizar memorias persistentes** en `C:/Users/IVAN LAFUENTE/.claude/projects/c--Users-IVAN-LAFUENTE-Projects-remate-web/memory/`:
   - `project_state.md` es un **SNAPSHOT, no un historial**: agregar la nota de esta sesión
     (misma altitud que una entrada de PROGRESO: unas líneas + puntero al handoff) y
     **mantener SOLO las últimas 5 notas** — al agregar la nueva, borrar la más vieja
     (su contenido ya vive en RESUMEN/PROGRESO/handoffs del repo; las podadas antiguas
     están en `project_state_archivo.md`). El archivo NO debe superar ~15 KB.
   - Si surgio feedback nuevo del usuario, guardarlo
   - Si surgio un pendiente importante para la proxima sesion, anotarlo
   - Si hubo errores nuevos, agregarlos a `lessons_patterns.md`

4b. **Índice de sesiones** — registrar esta sesión en `docs/sesiones.md` (la extensión VS Code no deja renombrar el panel: cachea los nombres y no relee archivos). Llevamos un índice propio. Obtener el código de la sesión activa:
   ```bash
   DIR=$(ls -dt ~/.claude/projects/*remate-web*/ | head -1)
   SID=$(basename "$(ls -t "$DIR"*.jsonl | head -1)" .jsonl)
   ```
   Agregar a `docs/sesiones.md` una fila de **UNA línea corta**: `Nombre | SID | fecha | frase de una línea` (sin re-narrar la sesión — para eso está el handoff). Si el archivo no existe, crearlo con el encabezado. NUNCA editar `~/.claude/sessions/` (no funciona). Para retomar después: `claude --resume <SID>`.

5. **Commitear y pushear los cambios** del resumen, progreso, pendientes, handoff y archivos relacionados:
   - Mensaje de commit en formato: `docs: cierre sesion YYYY-MM-DD — [resumen 5 palabras]`
   - NO incluir Co-Authored-By
   - Hacer `git push` automatico despues del commit. Solo afecta docs/estado/ y .claude/,
     asi que no cambia nada visible del sitio — pero OJO: este repo es un sitio en
     produccion (Cloudflare Pages, proyecto `remate-web`, branch de produccion **master**) y
     todo push dispara un build.
   - Si push falla por rebase necesario: `git pull --rebase && git push`

6. **Avisar al usuario** con este formato exacto:
   ```
   ═══ Cierre de sesion REMATE WEB ═══

   Handoff generado: .claude/handoff.md
   Resumen actualizado: docs/estado/RESUMEN.md
   Progreso actualizado: docs/estado/PROGRESO.md
   Pendientes actualizados: docs/estado/PENDIENTES.md

   Cambios:
   - [punto 1]
   - [punto 2]
   - [punto 3]

   Memorias actualizadas: [lista corta]

   Commit local hecho: [hash corto + mensaje]

   Proxima sesion arrancar por: [lo mas importante]

   Handoff archivado en: .claude/handoffs/handoff_YYYYMMDD_HHMM.md
   Listo. Podes seguir trabajando o cerrar con /exit cuando quieras.
   ```

REGLAS:
- Si no hay nada que actualizar en el resumen (sesion sin cambios reales), avisarlo y no commitear vacio
- Si hay cambios sin commitear que NO son del resumen (codigo de produccion, etc), avisar al usuario antes de tocar nada
- Push automatico del cierre OK porque solo toca docs/. Para commits que tocan HTML/CSS/JS,
  NUNCA push sin permiso explicito — y despues del deploy verificar con
  `curl -s https://remate.lacasonalafuente.com | grep ...` que el cambio esta arriba de verdad, nunca asumirlo por el push.
- El handoff debe ser CONCISO (max 40 lineas) — es lo que lee la proxima sesion al arrancar
- docs/estado/PROGRESO.md es el log acumulado: NUNCA borrar entradas — las de más de un mes se ARCHIVAN en PROGRESO_archivo.md
- **Una sola redacción larga por cierre (el handoff). Si estás escribiendo el mismo párrafo
  por segunda vez en otro archivo, estás haciendo el cierre viejo — pará y poné el puntero.**

---

## REGLA FINAL — Aprendizajes que NO deben repetirse (obligatorio, ANTES del commit)

Contestate esta pregunta antes de commitear: **"¿Qué problema resolvimos hoy cuya solución
no debe re-descubrirse nunca?"** (criterio: costó >15 min, tocó producción, o Iván tuvo que
recordarnos algo ya resuelto). Si la respuesta es "ninguno", decilo explícito en el resumen
final. Si hubo, registralo en DOS lugares — los dos, no uno:

1. **`docs/errores-aprendidos.md`** del repo (lo más reciente arriba) — OJO: es `docs/`,
   NO `memory/` (esa carpeta está en el .gitignore y lo escrito ahí no se versiona;
   error cometido y corregido el 30/07/2026):
   **qué falló · la causa raíz · cómo se resolvió · la regla para la próxima**.
2. **La memoria automática de ESTE proyecto** (`~/.claude/projects/<carpeta-de-este-proyecto>/memory/`):
   un archivo nuevo (o actualizar uno existente) con frontmatter `type: feedback`, el **Why**
   y el **How to apply**, + su línea en `MEMORY.md`. Esa memoria se carga SOLA en cada sesión:
   es la que evita repetir el error aunque nadie la busque.

> Por qué doble: el archivo del repo es la bitácora compartida (greppeable, versionada), pero
> solo sirve si alguien lo lee; `MEMORY.md` llega en cada arranque sin pedirlo. El bug del cert
> de Railway (2026-07-09) se re-diagnosticó desde cero teniendo la solución escrita en el repo.
> Y si el problema se resolvió a mitad de sesión, registralo EN ESE MOMENTO — este paso es la
> red por si se escapó, no el único lugar.
