# Elaboración de recursos para el aula virtual

> Manual de usuario, guía de instalación y estructura propuesta para el repositorio **elaborarecursosaulavirtual**.
> El repositorio en GitHub actualmente no contiene archivos, por lo que este README actúa como guía integral para poblarlo y utilizar las herramientas descritas. ([GitHub][1])

---

## Objetivo

Centralizar materiales y herramientas para la **elaboración de recursos educativos** orientados a cursos semipresenciales o en línea. Incluye una aplicación de escritorio para **extraer calendarios académicos y horarios por asignatura** desde guías o calendarios en PDF, además de cuadernos y presentaciones para el aula virtual.

---

## Público objetivo

* Docentes que necesitan preparar, publicar y actualizar recursos.
* Coordinaciones académicas que requieren consolidar horarios y evaluaciones.
* Estudiantes interesados en reproducibilidad y aprendizaje activo mediante notebooks.

---

## Funcionalidades principales

1. **Extractor de Guía y Calendario (app de escritorio)**

   * Detecta **año** y **encabezados** directamente del PDF.
   * Permite seleccionar **Periodo 1** o **Periodo 2**, y **Modalidad Presencial** o **Modalidad Virtual, A Distancia**.
   * Extrae bloques del calendario: **Clases 1ra etapa**, **1er Parcial**, **Clases 2da etapa**, **2do Parcial**, **Finales**, calcula **duración** y **días disponibles** entre bloques.
   * Construye un **diagrama de Gantt**.
   * Pestaña **Mi asignatura**: detecta **asignaturas** (y cuando es posible, carrera), recupera **horarios de clases** y **horarios de evaluaciones** aunque estén **distribuidos en varias páginas** del PDF.

2. **Materiales docentes**

   * Presentaciones y notebooks reutilizables para el aula virtual.
   * Ejemplo: presentación “De la teoría a la evidencia” que integra **R** con **datos abiertos** y simulaciones para inferencia estadística. 


---

## Requisitos de software

* **Python 3.10 o superior**
* Bibliotecas de la app:

  ```bash
  pip install PyPDF2 polars pandas openpyxl matplotlib
  ```
* Opcional para empaquetar en ejecutable:

  ```bash
  pip install pyinstaller
  ```

---

## Instalación y ejecución rápida

1. Clone el repositorio.

   ```bash
   git clone https://github.com/diegomezapy/elaborarecursosaulavirtual.git
   cd elaborarecursosaulavirtual
   ```

2. (Recomendado) Cree y active un entorno virtual.

   ```bash
   python -m venv .venv
   . .venv/Scripts/activate   # Windows
   # o
   source .venv/bin/activate  # Linux, macOS
   ```

3. Instale dependencias de la app.

   ```bash
   pip install -r apps/guia_calendario/requirements.txt
   ```

4. Ejecute la aplicación.

   ```bash
   python apps/guia_calendario/app_guia_calendario.py
   ```

5. Empaquetado opcional en Windows.

   ```bash
   pyinstaller --noconfirm --onefile --noconsole \
     --name GuiaCalendarioApp apps/guia_calendario/app_guia_calendario.py
   ```

   El ejecutable quedará en `dist/GuiaCalendarioApp.exe`.

---

## Manual de usuario

### 1. Pestaña “Calendario”

* **Seleccionar PDF**, abrir la guía o calendario institucional.
* Elegir **Modalidad** (Presencial, Virtual o A Distancia) y **Periodo** (1 o 2).
* Pulsar **Extraer**.
* La tabla resultante muestra: Año, Periodo, Evento, Fecha inicio, Fecha fin, Duración en días, Días disponibles hasta el próximo evento.
* **Exportar** a CSV o XLSX con los botones inferiores.
* El **Gantt** se actualiza automáticamente con los rangos detectados.

**Criterios de extracción**

* El año se detecta desde encabezados tipo `PRIMER PERIODO <año> - MODALIDAD <X>`, se tolera guion normal o en dash, con o sin espacio.
* “Clases” se captura aun cuando la guía rotule de forma abreviada.
* Para parciales y finales, se aceptan expresiones como “Miércoles 10 al viernes 26 de setiembre”, el mes del primer día se infiere del segundo cuando falta.

### 2. Pestaña “Mi asignatura”

* **Seleccionar PDF** y pulsar **Analizar Guía**.
* Si la guía no provee una lista formal de carreras, el sistema intenta **detectar al menos las asignaturas**.
* Seleccione la **carrera** (si fue detectada) y luego la **asignatura**.
* La tabla muestra:

  * **Clase**: Día, hora de inicio y fin, extraídos de la tabla “Horario de Clases”.
  * **Examen**: etiqueta y fecha, además del horario si está disponible, extraídos de “Horario de Evaluaciones” o “Horario de Exámenes”.
* Las tablas que abarcan varias páginas se consolidan, por lo que asignaturas con filas fragmentadas se unifican.

**Heurísticas que se aplican**

* Encabezados de carrera como “LIC. EN …” o “Licenciatura en …” se usan como separadores de bloque.
* “Horario de Clases” se interpreta con separación por espacios múltiples o tabulaciones, mapeando columnas a Lunes, Martes, Miércoles, Jueves, Viernes, Sábado.
* “Horario de Evaluaciones” admite cuadrículas por fecha, por ejemplo tablas con columnas Sábado, Domingo y fechas tipo `13/12/2025`, `14/12/2025`, típicas de las modalidades a distancia, y horarios por asignatura dentro de cada fecha.
* Si una fila no es separable de forma inequívoca, se asignan celdas detectadas y se completan vacíos para el resto.

---

## Flujo de trabajo recomendado

1. Colocar la guía PDF en `samples/`.
2. Ejecutar la app, validar el **Calendario** para cada Periodo y Modalidad.
3. Ejecutar **Mi asignatura**, validar horarios y evaluaciones de algunas materias críticas.
4. Exportar a CSV y XLSX, luego publicar en el LMS (Moodle, Classroom) y en el repositorio.
5. Guardar capturas o imprimir a PDF el Gantt para anexos del sílabo.

---

## Materiales docentes incluidos

* **Presentaciones** en `materiales/`, por ejemplo `Tema1.pptx`, con propuestas para integrar software libre y datos abiertos al aula virtual. 
* **Notebooks** en `notebooks/`, por ejemplo `materiales_BigData_VF.ipynb`, pensados para actividades guiadas y evaluación formativa.

---

## Personalización y extensión

* **Nuevas modalidades**, por ejemplo “Híbrida”, se agregan en el selector y en los patrones de búsqueda de encabezados.
* **Patrones del calendario** se ajustan en el código de la app donde se definen las expresiones regulares de “Clases”, “Evaluación Parcial” y “Evaluación Final”.
* **OCR**, si el PDF es escaneado, integrar `ocrmypdf` o `pytesseract` en una rama específica y normalizar el texto antes de aplicar los patrones.

---

## Resolución de problemas

* **“No se encontró el encabezado de la sección solicitada”**
  Verificar que el PDF corresponda al año correcto y que contenga encabezados de periodo con modalidad. En algunos documentos el guion entre año y modalidad puede estar pegado, se contempla este caso, pero si el encabezado cambió de sintaxis se debe actualizar el patrón.

* **No se detectan carreras**
  La app continuará con **detección de asignaturas**, útil cuando la guía distribuye horarios por páginas o cuando las carreras aparecen abreviadas.

* **Filas de horario desalineadas**
  Usar la exportación CSV como base, revisar celdas vacías y confirmar si en el PDF original hay celdas multilínea. Ajustar el separador de columnas en el código si la guía cambió el espaciado.

---

## Contribución

1. Crear rama desde `main`.
2. Incluir tests de extracción para nuevos PDFs en `samples/`.
3. Abrir un Pull Request con descripción técnica y ejemplos exportados.
4. Reportar problemas en **Issues** con el PDF, periodo, modalidad y página donde se observa la divergencia.

---

## Licencia

Indicar la licencia elegida, por ejemplo **MIT** para el código y **CC BY 4.0** para materiales docentes. Asegurar permisos para redistribuir guías institucionales.

---

## Estado del repositorio

El repositorio en GitHub se encuentra vacío a la fecha de esta redacción. Una vez que se incorporen los archivos propuestos, este README servirá como manual de usuario y guía de mantenimiento. ([GitHub][1])

---

## Créditos

* Material de apoyo y enfoque didáctico con simulaciones de R y datos abiertos, ver presentación incluida en este repositorio. 

---

### Anexos, comandos rápidos

```bash
# Crear entorno e instalar dependencias de la app
python -m venv .venv
source .venv/bin/activate          # Linux, macOS
# . .venv/Scripts/activate         # Windows
pip install PyPDF2 polars pandas openpyxl matplotlib

# Ejecutar app
python apps/guia_calendario/app_guia_calendario.py

# Exportar ejecutable en Windows
pyinstaller --noconfirm --onefile --noconsole \
  --name GuiaCalendarioApp apps/guia_calendario/app_guia_calendario.py
```

Si desea, puedo añadir un **README_app.md** con capturas, además de plantillas de `requirements.txt` y `.gitignore` para facilitar la primera publicación.

[1]: https://github.com/diegomezapy/elaborarecursosaulavirtual "diegomezapy/elaborarecursosaulavirtual · GitHub"

