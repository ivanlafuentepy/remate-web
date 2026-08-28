Ritual de cierre de sesion de REMATE WEB.

Ejecuta estos pasos EN ORDEN, sin saltarte ninguno:

1. **Revisar que cambio en esta sesion:**
   - `git log` ultimos commits de hoy
   - `git status` para ver si quedan cambios sin commitear
   - Repasar mentalmente la conversacion: que se hizo, que se decidio, que quedo a medio

2. **Generar handoff** en `.claude/handoff.md`:
   Crear/sobreescribir con esta estructura exacta:

   ```
   # HANDOFF — [fecha Paraguay DD/MM/YYYY]

   ## Completado en esta sesion
   [lista especifica de todo lo que hicimos]

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

2b. **Archivar el handoff con timestamp** (NO depender de /exit):
   - Ejecutar: `mkdir -p .claude/handoffs && cp .claude/handoff.md ".claude/handoffs/handoff_$(date +%Y%m%d_%H%M).md"`
   - Esto garantiza que el handoff queda archivado independientemente de si despues haces /exit o seguis trabajando.

3. **Actualizar memorias persistentes** en `C:/Users/IVAN LAFUENTE/.claude/projects/c--Users-IVAN-LAFUENTE-Projects-remate-web/memory/`:
   - Actualizar `project_state.md` con el estado actual
   - Si surgio feedback nuevo del usuario, guardarlo
   - Si surgio un pendiente importante para la proxima sesion, anotarlo

4. **Commitear y pushear los cambios** pendientes:
   - Mensaje de commit en formato: `docs: cierre sesion YYYY-MM-DD — [resumen 5 palabras]`
   - NO incluir Co-Authored-By
   - Hacer `git push` automatico despues del commit.
   - Si push falla por rebase necesario: `git pull --rebase && git push`

5. **Avisar al usuario** con este formato exacto:
   ```
   ═══ Cierre de sesion REMATE WEB ═══

   Handoff generado: .claude/handoff.md

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
- Si hay cambios sin commitear que NO son del cierre, avisar al usuario antes de tocar nada
- Push automatico del cierre OK porque solo toca archivos de docs/handoff. Para commits de codigo, NUNCA push sin permiso explicito.
- OJO: este repo es un sitio en produccion (Cloudflare Pages, proyecto `remate-web`,
  branch de produccion **master**). Un push dispara deploy automatico: si tocaste HTML,
  confirmalo con un curl a https://remate.lacasonalafuente.com antes de decir "listo".
- El handoff debe ser CONCISO (max 40 lineas) — es lo que lee la proxima sesion al arrancar

---

## REGLA FINAL — Aprendizajes que NO deben repetirse (obligatorio, ANTES del commit)

Contestate esta pregunta antes de commitear: **"¿Qué problema resolvimos hoy cuya solución
no debe re-descubrirse nunca?"** (criterio: costó >15 min, tocó producción, o Iván tuvo que
recordarnos algo ya resuelto). Si la respuesta es "ninguno", decilo explícito en el resumen
final. Si hubo, registralo en DOS lugares — los dos, no uno:

1. **`memory/errores-aprendidos.md`** del repo (si no existe, crealo; lo más reciente arriba):
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
