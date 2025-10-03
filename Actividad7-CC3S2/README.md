# Actividad 7: Explorando estrategias de fusión en Git

## A) Evitar (o no) `--ff`

**Pregunta:** ¿Cuándo **evitarías** `--ff` en un equipo y por qué?

**Respuesta:** Evitaría `--ff` cuando necesito preservar el contexto de integración de features, especialmente en equipos grandes donde es importante mantener trazabilidad de cuándo se integró cada feature. También cuando trabajo en repositorios con auditoría estricta donde necesito ver claramente los puntos de fusión en el historial.

## B) Trabajo en equipo con `--no-ff`

**Pregunta:** ¿Qué ventajas de trazabilidad aporta? ¿Qué problemas surgen con **exceso** de merges?

**Respuesta:** 
- **Ventajas:** Preserva el contexto de cuándo se integró cada feature, mantiene historial de desarrollo paralelo, facilita auditoría y rollback de features específicas.
- **Problemas con exceso:** Historial muy complejo, dificulta navegación, puede crear "merge commits" innecesarios que ensucian el log.

## C) Squash con muchos commits

**Pregunta:** ¿Cuándo conviene? ¿Qué se **pierde** respecto a merges estándar?

**Respuesta:**
- **Cuándo conviene:** Para mantener `main` limpia, cuando los commits intermedios no aportan valor, en features experimentales.
- **Qué se pierde:** Detalle del desarrollo paso a paso, capacidad de hacer rollback granular, trazabilidad de commits individuales, contexto de desarrollo.

## Conflictos reales con no-fast-forward

**Pregunta:** ¿Qué pasos adicionales hiciste para resolverlo?

**Respuesta:** Identificar archivos en conflicto con `git status`, revisar marcadores de conflicto (`<<<<<<<`, `=======`, `>>>>>>>`), editar manualmente para integrar cambios coherentemente, usar `git add` para marcar como resuelto, y `git commit` para cerrar el merge.

**Pregunta:** ¿Qué prácticas (convenciones, PRs pequeñas, tests) lo evitarían?

**Respuesta:** PRs pequeñas y frecuentes, comunicación entre desarrolladores, tests automatizados, convenciones de código, revisión de código, y evitar editar las mismas líneas en ramas paralelas.

## Comparar historiales tras cada método

**Pregunta:** ¿Cómo se ve el DAG en cada caso?

**Respuesta:**
- **Fast-forward:** Historial lineal, sin ramificaciones visibles
- **No-fast-forward:** Muestra merge commits con dos padres, historial ramificado
- **Squash:** Historial lineal pero se pierde el detalle de commits intermedios

**Pregunta:** ¿Qué método prefieres para: trabajo individual, equipo grande, repos con auditoría estricta?

**Respuesta:**
- **Trabajo individual:** Fast-forward para simplicidad
- **Equipo grande:** No-fast-forward para trazabilidad
- **Auditoría estricta:** No-fast-forward para preservar contexto completo

## Revertir una fusión

**Pregunta:** ¿Cuándo usar `git revert` en vez de `git reset`?

**Respuesta:** Usar `git revert` cuando el historial ya está compartido públicamente, para mantener integridad del repositorio compartido. Usar `git reset` solo en repositorios locales o cuando se puede hacer force push sin afectar a otros colaboradores.

**Pregunta:** ¿Impacto en un repo compartido con historial público?

**Respuesta:** `git revert` es seguro porque no reescribe historial, solo añade un nuevo commit que deshace cambios. `git reset` es peligroso porque reescribe historial y puede causar problemas a otros colaboradores.

## Fusión remota con Pull Request

**Pregunta:** ¿Qué estrategia usó la plataforma?

**Respuesta:** Depende de la configuración, pero típicamente "Create a merge commit" (no-ff) para preservar contexto o "Squash and merge" para historial limpio.

**Pregunta:** ¿Cómo aparece en el historial local tras `git pull`?

**Respuesta:** Aparece como merge commit con dos padres si se usó "Create a merge commit", o como commit único si se usó "Squash and merge".

## Variantes DevOps/DevSecOps

**Pregunta:** ¿Qué opción usaste y por qué?

**Respuesta:** Para estrategias de resolución usé `-X ours` cuando quería mantener la versión actual, `-X theirs` cuando confiaba en los cambios entrantes, y `-X renormalize` para manejar problemas de fin de línea en proyectos con desarrolladores en diferentes sistemas operativos.
