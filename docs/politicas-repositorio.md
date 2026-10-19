# Política de Configuración, Modelo de Ramificación y Gobernanza de Repositorio

## 1. Introducción
El presente documento define la estrategia de gestión de configuración, el modelo de ramificación en Git y las políticas de control de repositorios para el proyecto de E-commerce llevado a cabo en la materia Desarrollo de Software. Su objetivo es garantizar la trazabilidad, la calidad del código, la estabilidad de los entornos de integración y producción, y la correcta sincronización entre el equipo de desarrollo y las herramientas de gestión ágil.

## 2. Modelo de Ramificación en Git
Se adopta una variante estructurada de Gitflow / GitHub Flow, adaptada para optimizar los ciclos de integración continua (CI) y entrega continua (CD) sobre plataformas como GitHub Actions, Docker y Microsoft Azure.

* **`master`** (Producción)
* **`dev`** (Desarrollo)
* **`feature/*`** (Nuevas funcionalidades)
* **`bugfix/*`** (Corrección de errores en desarrollo)
* **`hotfix/*`** (Correcciones urgentes en producción)

### 2.1 Tipos de Ramas y su Ciclo de Vida
* **`master` (Rama de Producción):**
  * Contiene exclusivamente código estable, probado y listo para producción.
  * Está estrictamente prohibido realizar commits directos (`push`) a esta rama.
  * Todo cambio en esta rama proviene de un *Merge* validado desde `dev` o mediante un *hotfix*.

* **`dev` (Rama de Integración):**
  * Es la rama central de trabajo donde confluyen las nuevas características, correcciones de errores y mejoras en curso.
  * Representa el estado actual del próximo ciclo de lanzamiento.
  * Las pruebas automatizadas de integración continua (CI) se ejecutan obligatoriamente ante cada *Pull Request* (PR) hacia esta rama.

* **`feature/` (Ramas de Funcionalidad):**
  * **Naming:** `feature/TPI-<ID>-descripcion-breve` (ej. `feature/TPI-12-dockerfile-multistage`).
  * **Origen y Destino:** Nacen de `dev` y se fusionan nuevamente en `dev` mediante un *Pull Request*.
  * **Propósito:** Desarrollo de nuevas historias de usuario o tareas específicas asignadas en el tablero de gestión.

* **`bugfix/` (Ramas de Corrección en Desarrollo):**
  * **Naming:** `bugfix/TPI-<ID>-descripcion-breve` (ej. `bugfix/TPI-18-fix-db-connection`).
  * **Origen y Destino:** Nacen de `dev` y retornan a `dev`.
  * **Propósito:** Resolver bugs, fallos lógicos o inconsistencias detectadas durante las fases de pruebas o integración en la rama `dev`, antes de que el software alcance el entorno de producción.

* **`hotfix/` (Ramas de Parche URGENTE en Producción):**
  * **Naming:** `hotfix/descripcion-breve` (ej. `hotfix/security-patch-auth`).
  * **Origen y Destino:** Nace preferentemente a partir de una etiqueta (*tag*) o línea base estable en producción (es recomendable evitar hacerlo directamente desde el último commit inestable de `master` si este contiene cambios no verificados), y debe fusionarse simultáneamente hacia `master` y `dev` para mantener la paridad del código.
  * **Propósito:** Solucionar errores críticos reportados en el entorno productivo de manera urgente.

## 3. Convención de Commits
Para mantener un historial limpio y facilitar la generación de registros de cambios (*changelogs*), los mensajes de commit seguirán estricta y obligatoriamente el formato:

`<tipo>(<alcance>): <descripción corta> [TPI-<ID_Trello>]`

**Tipos permitidos:**
* `feat`: Introducción de una nueva funcionalidad.
* `fix`: Corrección de un error (aplicable tanto en `bugfix` como `hotfix`).
* `docs`: Cambios exclusivos en documentación.
* `style`: Ajustes de formato, espacios, puntos y comas (sin cambios en la lógica del código).
* `refactor`: Refactorización de código que no corrige bugs ni añade características.
* `test`: Adición o corrección de pruebas unitarias/integración.
* `chore`: Actualización de tareas de compilación, configuraciones de paquetes o dependencias.

## 4. Políticas de Repositorios y Protección de Ramas
Con el fin de asegurar la calidad del software y evitar el ingreso de código defectuoso a los entornos estables, se configuran las siguientes *Branch Protection Rules* en GitHub:

1. **Protección sobre `master` y `dev`:**
   * **Requerir Pull Request:** No se permiten cambios directos mediante comandos `git push`. Todo cambio requiere una revisión de código explícita (*Code Review*).
   * **Aprobaciones por Aprobación de Pares (*Peer Review* estricto):** Se exige obligatoriamente la aprobación de al menos 2 (dos) pares del equipo de desarrollo, asegurando que el código haya sido verificado de forma cruzada.
   * **Código Verificado y Pruebas Logradas:** Todo *Pull Request* debe demostrar formalmente que el código ha sido verificado mediante revisión estricta y que todas las pruebas automatizadas (unitarias, de integración y compilación en el pipeline de CI/CD) han sido superadas con éxito (*Green Build*).
   * **Linealidad del historial:** Se habilitará la opción de *Squash and merge* para mantener un historial de commits limpio y lineal en las ramas principales.
   * **Bloqueo de exclusiones:** Ningún usuario (incluyendo administradores) podrá omitir o saltarse las reglas de protección establecidas.

## 5. Trazabilidad e Integración con Trello
La gestión ágil y el control de versiones deben mantener una correspondencia biunívoca estricta:
* **Tarjetas en Trello:** Cada tarea debe reflejar claramente su estado en las columnas del tablero (*Backlog, Sprint Backlog, In Progress, Code Review, Testing, Done*).
* **Vínculo de IDs:** Toda rama (`feature/`, `bugfix/`, `hotfix/`) y todo *Pull Request* creado en GitHub deberá incluir explícitamente en su título o descripción el identificador de la tarjeta correspondiente en Trello.
* **Automatización:** El avance de las tareas en Trello se actualizará de manera correlativa conforme los PRs avancen en su revisión y aprobación en GitHub.