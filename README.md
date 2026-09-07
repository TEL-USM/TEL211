# TEL211 - Disponibilidad y Rendimiento de Sistemas TIC

Repositorio de los materiales de clase publicados para TEL211 durante el semestre 2026-2.

## Sobre el ramo

TEL211 estudia cómo modelar y evaluar la confiabilidad, disponibilidad y
rendimiento de sistemas y servicios TIC. El curso conecta datos, modelos
probabilísticos, componentes, arquitecturas, estados del sistema y servicio
bajo carga para fundamentar decisiones de diseño y operación.

**Profesor:** Patricio Olivares R.  
**Correo:** [patricio.olivaresr@usm.cl](mailto:patricio.olivaresr@usm.cl)

## Contenidos publicados

| Unidad | Contenido | Material disponible |
|---|---|---|
| `00` | Presentación y mapa conceptual del curso | [PDF](00-Presentacion-Curso/00-presentacion-curso.pdf) · [fuente Markdown](00-Presentacion-Curso/00-presentacion-curso.md) |
| `01` | Fundamentos de probabilidad para confiabilidad y rendimiento | [PDF](01-Fundamentos-Probabilidad/01-fundamentos-probabilidad.pdf) · [fuente Markdown](01-Fundamentos-Probabilidad/01-fundamentos-probabilidad.md) · [notebook](01-Fundamentos-Probabilidad/codigo/distribuciones.ipynb) · [mapa de distribuciones](01-Fundamentos-Probabilidad/images/mapa-distribuciones.png) |
| `02` | Teoría de confiabilidad: del tiempo de vida a una decisión | [PDF 02_1](02-Teoria-Confiabilidad/02_1-teoria-confiabilidad.pdf) · [Markdown 02_1](02-Teoria-Confiabilidad/02_1-teoria-confiabilidad.md) · [PDF 02_2](02-Teoria-Confiabilidad/02_2-modelacion-e-inferencia-confiabilidad.pdf) · [Markdown 02_2](02-Teoria-Confiabilidad/02_2-modelacion-e-inferencia-confiabilidad.md) · [curva de bañera](02-Teoria-Confiabilidad/images/bathtub-curve.svg) · [componente a servicio](02-Teoria-Confiabilidad/images/componente-a-servicio.svg) · [área bajo la curva MTTF](02-Teoria-Confiabilidad/images/mttf-area.svg) |
| `03` | Diagramas de confiabilidad: RBD y árboles de falla | [PDF](03-Diagramas-Confiabilidad/03-diagramas-confiabilidad.pdf) · [fuente Markdown](03-Diagramas-Confiabilidad/03-diagramas-confiabilidad.md) · [RBD vs. árbol de falla](03-Diagramas-Confiabilidad/images/rbd-vs-ft.png) · [RBD mixto](03-Diagramas-Confiabilidad/images/rbd-mixto.png) |

Las fuentes Markdown corresponden a las presentaciones editables. Los PDF son
las versiones listas para lectura. Los notebooks incluyen cálculos y ejemplos
reproducibles asociados a las unidades correspondientes.

## Material de apoyo y evaluaciones

| Tipo | Material | Recursos disponibles |
|---|---|---|
| Ayudantía 1 | Distribuciones de probabilidad | [Enunciado](Ayudantías/01-Distribuciones-Probabilidad/Ayudantía_1.pdf) · [Ayudantía desarrollada](Ayudantías/01-Distribuciones-Probabilidad/Ayudantia1_TIC_desarrollada.pdf) · [notebook](Ayudantías/01-Distribuciones-Probabilidad/Notebook-Distribuciones-Probabilidad.ipynb) |
| Ayudantía 2 | Teoría de confiabilidad | [PDF](Ayudantías/02-Teoría-Confiabilidad/Ayudantia2.pdf) |
| Ayudantía 3 | Diagramas de confiabilidad | [PDF](Ayudantías/03-Diagramas-Confiabilidad/Ayudantia3_TIC.pdf) |
| Certamen 1 | Preparación y material de apoyo | [Ejercicios tipo certamen](ejercicios/certamen1/ejercicios.pdf) · [formulario](ejercicios/certamen1/formulario-c1.pdf) · [soluciones](ejercicios/certamen1/soluciones.pdf) |
| Tarea 1 | Evaluación del semestre | [Enunciado](evaluaciones/2026-2/tareas/tarea1/enunciado/tarea1.pdf) · [notebook de trabajo](evaluaciones/2026-2/tareas/tarea1/enunciado/tarea1_notebook.ipynb) |

## Estructura del repositorio

```text
00-Presentacion-Curso/                 Presentación y ruta del curso
01-Fundamentos-Probabilidad/           Modelos probabilísticos
02-Teoria-Confiabilidad/               Confiabilidad e inferencia
03-Diagramas-Confiabilidad/            RBD y árboles de falla
Ayudantías/                            Material de apoyo
ejercicios/certamen1/                  Ejercicios y material de preparación para el Certamen 1
evaluaciones/2026-2/                   Tareas y evaluaciones del semestre
```

## Cómo obtener el repositorio

```bash
git clone --branch 2026-2 https://github.com/TEL-USM/TEL211.git
cd TEL211
```

Para actualizar una copia local:

```bash
git pull
```

También se puede descargar la rama desde **Code > Download ZIP** en GitHub.

## Alcance

El repositorio contiene materiales de clase y sus recursos asociados. Las
fuentes Markdown permiten revisar la estructura de las presentaciones, los
notebooks e imágenes complementan el material y la carpeta `evaluaciones/`
conserva los enunciados y recursos de trabajo publicados durante el semestre.
La carpeta `ejercicios/` reúne material de preparación y apoyo para el
Certamen 1.
