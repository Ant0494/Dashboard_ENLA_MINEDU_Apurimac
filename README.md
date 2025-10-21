# Dashboard de Monitoreo: Resultados Educativos ENLA 2024 (MINEDU)

Este proyecto ejemplifica la aplicación de técnicas de Business Intelligence (BI) para la **gestión de la información pública**, utilizando Microsoft Excel  (Power Pivot) como herramienta principal.

## 1. Contexto de Negocio y Metodología 

### Contexto: La Evaluación Nacional de Logros de Aprendizaje (ENLA) 2024
La **Evaluación Nacional de Logros de Aprendizaje (ENLA) 2024**, implementada por el MINEDU, tuvo como objetivo conocer la situación de los aprendizajes de los estudiantes, incluyendo la evaluación censal de 4.° grado de primaria.

### Reporte de Resultados y Métricas Oficiales
Los resultados de la ENLA se informan de dos maneras:
1.  **Medida Promedio:** El **promedio aritmético** de los puntajes individuales, siendo la base para la medida **`Puntaje`** del dashboard.
2.  **Niveles de Logro:** Clasificación de los estudiantes basada en su puntaje, que describe los aprendizajes logrados según el CNEB:
    * **Satisfactorio:** Logró los aprendizajes **esperados**.
    * **En proceso:** Logró **parcialmente** los aprendizajes esperados.
    * **En inicio:** Logró aprendizajes **elementales**.
    * **Previo al inicio:** El estudiante **aún no ha logrado** los aprendizajes necesarios.

### Impacto y Problema Resuelto
El proyecto fue solicitado por la DRE Apurímac para transformar datos brutos en una **plataforma visual y funcional** que permita la **identificación precisa de desafíos** y la focalización de recursos.

---

## 2. Diccionario de Datos (Preparación de la Fuente) 

A continuación, se describe detalladamente la estructura de la fuente de datos plana antes de la fase de modelado:

| Elemento | Descripción | Tipo de Dato | Obligatoriedad | Rol Clave / Uso |
| :--- | :--- | :--- | :--- | :--- |
| **Año** | Año de la evaluación (2024). | INT (4) | Obligatorio | Atributo de contexto. |
| **Evaluación** | Nombre del estudio (ECE 2024). | VARCHAR(15) | Obligatorio | Atributo de contexto. |
| **Nivel** | Nivel educativo evaluado. | VARCHAR(15) | Obligatorio | Atributo de contexto. |
| **Grado** | Grado evaluado (CUARTO). | VARCHAR(15) | Obligatorio | Atributo de contexto. |
| **Ubigeo** | Código geográfico oficial de la ubicación. | VARCHAR(6) | Obligatorio | **Clave de conexión** (`Dim_Geografia`). |
| **Región** | Nombre del departamento (APURÍMAC). | VARCHAR(30) | Obligatorio | Atributo de `Dim_Geografia`. |
| **Provincia** | Nombre de la provincia. | VARCHAR(30) | Obligatorio | Atributo de `Dim_Geografia`. |
| **Distrito** | Nombre del distrito. | VARCHAR(30) | Obligatorio | Atributo de `Dim_Geografia`. |
| **Centro Poblado** | Nombre del centro poblado. | VARCHAR(50) | Obligatorio | Atributo de `Dim_Geografia`. |
| **Código Modular** | Código único de identificación de la IE. | VARCHAR(10) | Obligatorio | **Clave de conexión** (`Dim_IE`). |
| **Anexo** | Indicador de anexo de la IE (0 para principal). | INT (2) | Obligatorio | Atributo de `Dim_IE`. |
| **Nombre de la IE** | Denominación completa de la Institución Educativa. | VARCHAR(100) | Obligatorio | Atributo de `Dim_IE`. |
| **UGEL** | Unidad de Gestión Educativa Local a la que pertenece la IE. | VARCHAR(30) | Obligatorio | Atributo de `Dim_IE`. |
| **Gestión** | Tipo de administración (ESTATAL / NO ESTATAL). | VARCHAR(15) | Obligatorio | Atributo de `Dim_IE`. |
| **Area** | Clasificación geográfica (URBANA / RURAL). | VARCHAR(10) | Obligatorio | Atributo de `Dim_IE`. |
| **Característica** | Tipo de organización (UNIDOCENTE / POLIDOCENTE). | VARCHAR(30) | Obligatorio | Atributo de `Dim_IE`. |
| **Sección** | Identificador del grupo de aula (A, B, C y D). | VARCHAR(5) | Obligatorio | **Clave de conexión** (`Dim_Sección`). |
| **Area Academica** | Asignatura evaluada (Lectura o Matemática). | VARCHAR(15) | Obligatorio | **Clave de conexión** (`Dim_Area`). |
| **Estudiantes programados** | Cantidad total de alumnos programados por IE/Sección. | INT | Obligatorio | **Hecho / Ponderación**. Se utiliza para calcular el promedio ponderado del puntaje. |
| **Puntaje Promedio** | Puntaje promedio obtenido por el conjunto de estudiantes. | DECIMAL(10, 2) | Obligatorio | **Hecho** (base para el Promedio Ponderado). |
| **Secciones evaluadas** | Número de secciones que participaron. | INT | Obligatorio | **Hecho**. |
| **Cantidad \[Nivel]** | Conteo de estudiantes en cada nivel de logro. | INT | No Nulo | **Hecho** (base para la distribución porcentual). |

---

---

## 3. Fase de Modelado de Datos (Power Pivot) 

Para garantizar la integridad de los cálculos (promedios ponderados) y optimizar el rendimiento, se implementó un **Esquema Estrella** en Power Pivot, separando los datos en Hechos y Dimensiones.

### Estructura del Modelo ⭐️

El modelo está construido con un **Esquema Estrella** que conecta la tabla de hechos con **cuatro dimensiones**, tal como fue diseñado:


| Tipo de Tabla | Nombre de la Tabla | Propósito | Claves Primarias/Únicas |
| :--- | :--- | :--- | :--- |
| **HECHOS (Fact)** | `F_Resultados` | Contiene los valores numéricos a medir. | `Ubigeo`, `Código Modular`, `Clave_Prueba` |
| **DIMENSIÓN** | `Dim_Geografia` | Filtros por ubicación física. | `Ubigeo` |
| **DIMENSIÓN** | `Dim_IE` | Filtros por institución y contexto administrativo. | `Código Modular` |
| **DIMENSIÓN** | **`Dim_Sección`** | Filtros por grupo de aula. | `Sección` |
| **DIMENSIÓN** | **`Dim_Area`** | Filtros por asignatura. | `Area Academica` |


<img width="1495" height="951" alt="image" src="https://github.com/user-attachments/assets/6846f133-8f0f-47e7-be8b-debfd57787dc" />



---

## 4. Descripción de Funcionalidades del Dashboard

La interfaz de usuario fue diseñada para ofrecer una **navegación jerárquica y segmentada**:

* **Segmentadores Dinámicos:** Resultados segmentables por `UGEL`, `Área` (Rural/Urbana), y `Característica` (Unidocente/Polidocente).
* **Desplegables Jerárquicos:** Los gráficos principales permiten el **despliegue (drill-down)** del resultado por **Distrito** y, subsecuentemente, por **Institución Educativa (IE)**, ajustándose dinámicamente a la selección de la UGEL.
* **Comparativa Estática:** Barra compilada que muestra el resultado general de la región Apurímac en comparación con las UGEL que la componen.
* **Fichas de Monitoreo:** Muestran la cantidad de **Estudiantes Programados** y **Estudiantes Evaluados** según los filtros aplicados.
* **Distribución de Logro:** Anillos (Donut Charts) separados para Lectura y Matemática que muestran la **distribución porcentual** de alumnos clasificados por los cuatro Niveles de Logro.

---

## 5. Justificación Técnica

Aunque Power BI es reconocida como la herramienta líder para BI, la elección de Microsoft Excel (Power Pivot) se debió a un **requisito explícito** (DRE Apurímac), priorizando la accesibilidad institucional:

* **Requisito:** El entregable final fue solicitado explícitamente en formato Excel.
* **Accesibilidad Institucional:** La DRE opera con licencias universales de Microsoft Office 365, haciendo de Excel una herramienta accesible para **todos los gestores y directores** sin la barrera de las licencias de Power BI Pro.
* **Entregable Offline:** El proyecto priorizó una solución **fácilmente transferible** y operativa en entornos con conectividad limitada.

Este enfoque demostró la capacidad de adaptar soluciones de BI a las **restricciones tecnológicas y logísticas** del cliente.

---

## 6. Evidencia del Trabajo y Demostración

<img width="1135" height="642" alt="image" src="https://github.com/user-attachments/assets/c84b5559-58e2-4dba-81f2-d5d3cec29d0b" />


### Demostración Interactiva del Dashboard

https://github.com/user-attachments/assets/0726cbf8-3187-408d-8494-bb5a89fc412a


