# Actividad 5: Pipeline DevOps con Make y Bash

## Parte 1: Construir - Pipeline Básico

### Ejercicio 1: Make Help y Configuración
- El comando `make help` muestra los objetivos disponibles y sus descripciones gracias a `.DEFAULT_GOAL := help`.
- `.PHONY` indica objetivos que no son archivos reales, como `clean` y `help`.
- La salida detallada se encuentra en `logs/make-help.txt`.

### Ejercicio 2: Build e Idempotencia
- Primera ejecución de `build` crea `out/hello.txt` (`logs/build-run1.txt`).
- Segunda ejecución no hace nada porque el archivo ya está actualizado (`logs/build-run2.txt`).
- La verificación del contenido está en `evidencia/out-hello-run1.txt`.

### Ejercicio 3: Fallo Controlado
- Al usar `PYTHON=python4`, el comando falla porque el intérprete no existe.
- `.DELETE_ON_ERROR` asegura que no queden archivos corruptos.
- El log completo está en `logs/fallo-python4.txt`.

### Ejercicio 4: Dry-run y Debug
- `make -n` muestra el plan de ejecución sin ejecutar nada (`logs/dry-run-build.txt`).
- `make -d` revela el proceso de decisión de Make (`logs/make-d.txt`).

### Ejercicio 5: Incrementalidad
- Al tocar `src/hello.py`, Make detecta el cambio y reconstruye.
- Al tocar `out/hello.txt`, Make no reconstruye porque la fuente no cambió.
- Logs en `logs/rebuild-after-touch-src.txt` y `logs/no-rebuild-after-touch-out.txt`.

### Ejercicio 6: Linting y Formateo
- `shellcheck` analiza `run_tests.sh` para buenas prácticas.
- `shfmt` verifica el formato del código shell.
- Resultados en `logs/lint-shellcheck.txt` y `logs/format-shfmt.txt`.

### Ejercicio 7: Reproducibilidad
- Dos builds consecutivos producen el mismo hash SHA256.
- La comparación de hashes está en `logs/sha256-diff.txt`.
- Los hashes individuales en `logs/sha256-1.txt` y `logs/sha256-2.txt`.

### Ejercicio 8: Error de Separador
- Se reprodujo el error "missing separator" cambiando tabulaciones por espacios.
- El error se documenta en `logs/error-missing-separator.txt`.

## Parte 2: Leer - Pipeline Completo

### Ejercicio 1: Análisis del Pipeline Completo
- El dry-run muestra el plan completo de `all` en `logs/dry-run-all.txt`.
- El debug revela las decisiones de Make en `logs/make-d-full.txt`.

### Ejercicio 2: Verificación de Reproducibilidad
- `verify-repro` confirma builds idénticos.
- Los resultados están en `logs/verify-repro.txt`.

### Ejercicio 3: Benchmark
- Build limpio toma más tiempo (`logs/benchmark-clean.txt`).
- Build incremental es más rápido (`logs/benchmark-incremental.txt`).

### Ejercicio 4: Variables Sobrescritas
- Se probó `PYTHON=python` para demostrar la flexibilidad.
- Los resultados varían según la configuración del sistema.

### Ejercicio 5: Ejecución Paralela
- `make -j4` ejecuta tareas independientes en paralelo.
- Mejora significativa en tiempo de construcción.

## Parte 3: Extender - Mejoras

### Ejercicio 1: Lint Mejorado
- Se introdujeron errores deliberados para probar `shellcheck`.
- Las advertencias demuestran la utilidad del linting.

### Ejercicio 2: Reproducibilidad con GNU tar
- Se verificó la necesidad de opciones específicas de GNU tar.
- `--sort`, `--numeric-owner` y `--mtime` son cruciales.

## Archivos de Evidencia

### Logs
- Todos los archivos `.txt` generados se encuentran en la carpeta `logs/`.
- Cada archivo documenta una parte específica del proceso.

### Evidencia
- Capturas de salida específicas en la carpeta `evidencia/`.
- Incluye resultados de pruebas y errores reproducidos.

### Meta
- Versiones de herramientas en `meta/entorno.txt`.
- Hash del commit final en `meta/commit.txt`.