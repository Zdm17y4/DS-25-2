# Actividad 6: Evidencias de Conceptos y Operaciones en Git

## Conceptos y Comandos Utilizados

- **`git config`**: Se utilizó para establecer la identidad del usuario (nombre y correo electrónico) a nivel global, esencial para la autoría de los commits.
- **`git init`**: Creó un nuevo repositorio local en el directorio de trabajo, lo que permitió comenzar a rastrear el historial de cambios del proyecto.
- **`add/commit`**: `git add` se usó para preparar los cambios en los archivos (como `main.py` y `README.md`) para ser guardados, y `git commit` para registrar esos cambios de forma permanente en el historial.
- **`log`**: El comando `git log` fue fundamental para visualizar el historial de commits, permitiendo revisar los cambios, sus autores y las fechas de manera organizada.
- **Ramas (branching)**: Se crearon ramas (`git branch`, `git switch -c`) para desarrollar funcionalidades de forma aislada. La fusión (`git merge`) se usó para integrar los cambios de vuelta a la rama `main`, incluyendo la resolución manual de un conflicto de fusión que surgió en `main.py`.
- **`cherry-pick`**: Se utilizó para seleccionar y aplicar un commit específico desde una rama a otra, permitiendo portar un cambio concreto sin fusionar la rama completa.

---

## Preguntas de la Actividad

### git log

- **Pregunta:** ¿Cuál es la salida de este comando? (`git log --graph --pretty=format:'%x09 %h %ar ("%an") %s'`)
  - **Respuesta:** La salida es un historial de commits en formato de gráfico ASCII. Cada línea muestra de forma compacta el hash del commit, el tiempo transcurrido, el autor y el mensaje, facilitando la visualización de las ramificaciones y fusiones.

### Ramas y Fusión

- **Pregunta:** ¿Cómo te ha ayudado Git a mantener un historial claro y organizado de tus cambios?
  - **Respuesta:** Git ha sido clave para registrar cada cambio como un `commit` con un mensaje descriptivo. Esto crea un historial cronológico y lógico que permite entender la evolución del proyecto y localizar cambios específicos fácilmente con `git log`.

- **Pregunta:** ¿Qué beneficios ves en el uso de ramas para desarrollar nuevas características o corregir errores?
  - **Respuesta:** El principal beneficio es el aislamiento. Las ramas permiten trabajar en nuevas funcionalidades o arreglos sin desestabilizar la base de código principal (`main`), facilitando la experimentación y el desarrollo en paralelo.

- **Pregunta:** Realiza una revisión final del historial de commits para asegurarte de que todos los cambios se han registrado correctamente.
  - **Respuesta:** Se utilizó `git log --oneline --graph --all` para realizar una revisión final. El gráfico confirmó que todos los commits, incluyendo la creación de ramas, la fusión y la resolución de conflictos, se registraron correctamente en el historial.

- **Pregunta:** Revisa el uso de ramas y merges para ver cómo Git maneja múltiples líneas de desarrollo.
  - **Respuesta:** La revisión del historial gráfico mostró cómo Git maneja las ramas como líneas de desarrollo independientes. Al usar `git merge`, Git integró los historiales, y en caso de cambios conflictivos, detuvo el proceso para que el conflicto pudiera ser resuelto manualmente.

---

## Archivos de Log

- `logs/git-version.txt`: Generado con `git --version`.
- `logs/config.txt`: Generado con `git config --list`.
- `logs/init-status.txt`: Generado con `git init` seguido de `git status`.
- `logs/add-commit.txt`: Generado con `git add .` seguido de `git status` y luego `git commit`.
- `logs/log-oneline.txt`: Generado con `git log --oneline`.
---

## Repositorio Remoto

sin remoto