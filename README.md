# Generador de Datos Sintéticos — Trabajo Científico

Aplicación web interactiva para generar datasets de dispersión (scatter) dibujando directamente sobre un lienzo. Diseñada para investigación, docencia y prototipado rápido de datos sintéticos etiquetados.

**Autor:** Oscar Iván Vargas Pineda · Asistido por IA

---

## Tabla de Contenidos

1. [Descripción General](#descripción-general)
2. [Requisitos](#requisitos)
3. [Instalación y Ejecución](#instalación-y-ejecución)
4. [Flujo de Uso](#flujo-de-uso)
   - [Pantalla de Configuración](#1-pantalla-de-configuración)
   - [Pantalla de Dibujo](#2-pantalla-de-dibujo)
5. [Herramientas de Dibujo](#herramientas-de-dibujo)
   - [Modo Dibujar](#modo-dibujar)
   - [Modo Borrar](#modo-borrar)
6. [Sistema de Grupos (Etiquetas)](#sistema-de-grupos-etiquetas)
7. [Tamaño del Pincel](#tamaño-del-pincel)
8. [Vista Previa de Datos](#vista-previa-de-datos)
9. [Exportación de Datos](#exportación-de-datos)
10. [Estructura del Archivo Exportado](#estructura-del-archivo-exportado)
11. [Atajos y Controles](#atajos-y-controles)
12. [Compatibilidad](#compatibilidad)
13. [Arquitectura Técnica](#arquitectura-técnica)
14. [Casos de Uso](#casos-de-uso)
15. [Limitaciones Conocidas](#limitaciones-conocidas)

---

## Descripción General

El **Generador de Datos Sintéticos** permite crear datasets de puntos (x, y) con etiquetas de grupo, dibujándolos a mano sobre un plano cartesiano interactivo. Es útil cuando se necesitan datos con distribuciones espaciales específicas que son difíciles de generar algorítmicamente (clusters irregulares, formas curvas, patrones personalizados, etc.).

El flujo es simple:

1. Configurar los ejes y nombres de variables.
2. Dibujar puntos sobre el lienzo con el ratón o la pantalla táctil.
3. Exportar el dataset como `.csv` o `.xlsx`.

---

## Requisitos

- Un navegador web moderno (Chrome, Firefox, Edge, Safari).
- No requiere instalación, servidor, ni dependencias locales.
- Conexión a internet solo para la carga inicial (fuentes de Google Fonts y librería XLSX desde CDN).

---

## Instalación y Ejecución

1. Descargar o clonar el archivo `GeneradorDeDatos.html`.
2. Abrir el archivo directamente en el navegador (doble clic o `Archivo > Abrir`).

No se necesita servidor web, compilador, ni herramientas de build. Es una aplicación de un solo archivo HTML autocontenida.

---

## Flujo de Uso

### 1. Pantalla de Configuración

Al abrir la aplicación se muestra un formulario centrado con los siguientes campos:

| Campo | Descripción | Valor por defecto |
|-------|-------------|-------------------|
| **Nombre var. X** | Nombre de la columna X en el archivo exportado | `x` |
| **Nombre var. Y** | Nombre de la columna Y en el archivo exportado | `y` |
| **Etiqueta eje X** | Texto que aparece en el eje horizontal del lienzo | `Variable X` |
| **Etiqueta eje Y** | Texto que aparece en el eje vertical del lienzo | `Variable Y` |
| **X mínimo** | Valor mínimo del rango en el eje X | `0` |
| **X máximo** | Valor máximo del rango en el eje X | `100` |
| **Y mínimo** | Valor mínimo del rango en el eje Y | `0` |
| **Y máximo** | Valor máximo del rango en el eje Y | `100` |
| **Nombre archivo** | Nombre base del archivo al exportar (sin extensión) | `datos_dibujados` |
| **Formato de exportación** | Formato del archivo de salida | `.csv` (o `.xlsx`) |

> **Nota:** Los rangos de los ejes definen la escala del plano cartesiano. Los puntos dibujados se mapean a coordenadas reales dentro de estos rangos. Por ejemplo, si X va de 0 a 100, un punto en el centro horizontal del lienzo tendrá un valor de X ≈ 50.

Tras completar la configuración, presionar el botón **"Comenzar a Dibujar →"** para pasar a la pantalla de dibujo.

### 2. Pantalla de Dibujo

La pantalla de dibujo está organizada en las siguientes zonas:

```
┌──────────────────────────────────────────────────┐
│  ← Config  │  Generador de Datos  │  N pts      │  ← Barra de herramientas
│             │                      │  Limpiar    │
│             │                      │  Exportar   │
├──────────────────────────────────────────────────┤
│  Dibujar/Borrar │ Grupo: [A][B][+] │ Pincel: 1 3│  ← Barra de controles
├──────────────────────────────────────────────────┤
│                                                  │
│                                                  │
│               LIENZO DE DIBUJO                   │  ← Área principal (canvas)
│            (ocupa la mayor parte                 │
│             de la ventana)                       │
│                                                  │
│                                                  │
├──────────────────────────────────────────────────┤
│              ● Grupo A (15)  ● Grupo B (8)       │  ← Leyenda
├──────────────────────────────────────────────────┤
│              Trabajo Científico                  │  ← Pie de página
└──────────────────────────────────────────────────┘
                                    ┌──────────────┐
                                    │ Vista previa │  ← Panel flotante
                                    │ #  x   y  lbl│     (esquina inferior
                                    │ 1  23  45 A  │      derecha)
                                    └──────────────┘
```

#### Barra de herramientas superior

- **← Config**: Regresa a la pantalla de configuración (los puntos se conservan en memoria).
- **Contador de puntos**: Muestra el total de puntos dibujados (ej. `142 pts`).
- **Limpiar todo**: Elimina todos los puntos del lienzo (pide confirmación).
- **Exportar**: Descarga el dataset en el formato seleccionado. Se habilita cuando hay al menos 1 punto.

---

## Herramientas de Dibujo

### Modo Dibujar

Activado por defecto. Permite agregar puntos al lienzo.

- **Clic simple**: Coloca un punto (o grupo de puntos según el tamaño del pincel) en la posición del cursor.
- **Clic y arrastrar**: Dibuja una línea continua de puntos siguiendo el movimiento del ratón.
- El cursor se muestra como una cruz (`crosshair`).
- Cada punto se asigna al grupo actualmente seleccionado.

### Modo Borrar

Se activa haciendo clic en el botón **"Borrar"** en la barra de controles.

- Muestra un cursor circular rojo punteado que indica el área de borrado.
- **Clic o arrastrar**: Elimina todos los puntos dentro del radio del borrador.
- El radio del borrador es configurable: **10**, **20**, **40** o **60** píxeles.
- Los controles de grupo y pincel se ocultan automáticamente al entrar en modo borrar.

Para volver al modo dibujar, hacer clic en el botón **"Dibujar"**.

---

## Sistema de Grupos (Etiquetas)

Los grupos permiten asignar etiquetas categóricas a los puntos dibujados. Esto es fundamental para generar datasets de clasificación supervisada.

- **Grupo por defecto**: `Grupo A` (color rojo).
- **Agregar un nuevo grupo**: Escribir el nombre en el campo de texto `+ nuevo` y presionar `Enter` o el botón `+`.
- **Cambiar de grupo activo**: Hacer clic en el botón del grupo deseado en la barra de controles.
- **Colores automáticos**: Cada grupo recibe un color distinto de una paleta de 8 colores:
  1. Rojo (`#EF4444`)
  2. Azul (`#3B82F6`)
  3. Verde azulado (`#14B8A6`)
  4. Dorado (`#F59E0B`)
  5. Naranja (`#F97316`)
  6. Violeta (`#8B5CF6`)
  7. Cian (`#06B6D4`)
  8. Rosa (`#EC4899`)
- Si se crean más de 8 grupos, los colores se repiten cíclicamente.
- La **leyenda** debajo del lienzo muestra los grupos que tienen al menos un punto, junto con el conteo.

---

## Tamaño del Pincel

El tamaño del pincel controla cuántos puntos se generan por cada interacción:

| Tamaño | Puntos por clic/movimiento | Dispersión |
|--------|---------------------------|------------|
| **1** | 1 punto exacto | Ninguna (punto preciso) |
| **3** | 3 puntos | Leve dispersión aleatoria |
| **5** | 5 puntos | Dispersión moderada |
| **10** | 10 puntos | Dispersión amplia |

La dispersión es proporcional al tamaño del pincel: con tamaños mayores, los puntos generados se distribuyen aleatoriamente en un área más amplia alrededor del cursor. Esto es útil para crear clusters con distribuciones naturales rápidamente.

> **Fórmula de dispersión**: Cada punto se desplaza aleatoriamente en X e Y con un factor de `±(tamaño × rango_eje × 0.004)`.

---

## Vista Previa de Datos

Un panel flotante en la esquina inferior derecha muestra las **últimas 8 observaciones** del dataset en formato tabular:

| Columna | Descripción |
|---------|-------------|
| **#** | Índice del punto |
| **x** | Valor de la variable X (nombre personalizado) |
| **y** | Valor de la variable Y (nombre personalizado) |
| **label** | Etiqueta del grupo al que pertenece el punto |

Características del panel:

- **Aparece automáticamente** al dibujar el primer punto.
- **Semi-transparente** (85% opacidad) para no obstruir el lienzo; se vuelve opaco al pasar el ratón.
- **Colapsable**: Hacer clic en el título "Vista previa" para plegar/desplegar la tabla.
- Se actualiza en tiempo real con cada nuevo punto o borrado.

---

## Exportación de Datos

### Formato CSV

- Archivo de texto separado por comas con codificación UTF-8 (con BOM para compatibilidad con Excel).
- Los valores de texto que contengan comas se envuelven en comillas dobles.
- Primera fila: encabezados con los nombres de las variables configurados.

Ejemplo de salida:
```csv
x,y,label
23.4521,67.8901,Grupo A
45.1234,12.3456,Grupo A
78.9012,89.0123,Grupo B
```

### Formato XLSX

- Archivo Excel nativo generado con la librería [SheetJS (xlsx)](https://sheetjs.com/).
- Una hoja llamada "Datos" con los mismos encabezados y estructura.
- Compatible con Excel, LibreOffice Calc, Google Sheets, y cualquier lector de archivos XLSX.

### Nombre del archivo

El nombre del archivo descargado es el configurado en la pantalla inicial + la extensión correspondiente:
- Ejemplo: `datos_dibujados.csv` o `datos_dibujados.xlsx`

---

## Estructura del Archivo Exportado

Cada fila representa un punto dibujado con tres columnas:

| Columna | Tipo | Descripción |
|---------|------|-------------|
| `x` (nombre configurable) | `number` | Coordenada X del punto, mapeada al rango configurado. Precisión de 4 decimales. |
| `y` (nombre configurable) | `number` | Coordenada Y del punto, mapeada al rango configurado. Precisión de 4 decimales. |
| `label` | `string` | Nombre del grupo al que pertenece el punto. |

---

## Atajos y Controles

| Acción | Control |
|--------|---------|
| Dibujar puntos | Clic izquierdo / toque en el lienzo (modo Dibujar) |
| Dibujar continuamente | Mantener clic y arrastrar |
| Borrar puntos | Clic izquierdo / toque en el lienzo (modo Borrar) |
| Agregar grupo | Escribir nombre + `Enter` en el campo "nuevo" |
| Cambiar grupo activo | Clic en el botón del grupo |
| Cambiar tamaño de pincel | Clic en botones `1`, `3`, `5`, `10` |
| Cambiar radio del borrador | Clic en botones `10`, `20`, `40`, `60` |
| Colapsar/expandir vista previa | Clic en el título "Vista previa" |
| Limpiar todos los puntos | Botón "Limpiar todo" (con confirmación) |
| Exportar datos | Botón "Exportar" |
| Volver a configuración | Botón "← Config" |

---

## Compatibilidad

| Característica | Soporte |
|----------------|---------|
| **Navegadores desktop** | Chrome 60+, Firefox 55+, Edge 79+, Safari 13+ |
| **Navegadores móvil** | Chrome Android, Safari iOS 13+ |
| **Entrada táctil** | Sí (usa Pointer Events para compatibilidad mouse/touch) |
| **Responsive** | Sí. El lienzo se adapta al tamaño de la ventana. En pantallas < 600px la barra de herramientas se reorganiza verticalmente. |
| **Offline** | Funciona sin conexión tras la primera carga (las fuentes y la librería XLSX se cachean por el navegador). |

---

## Arquitectura Técnica

| Aspecto | Detalle |
|---------|---------|
| **Stack** | HTML5 + CSS3 + JavaScript vanilla (sin frameworks) |
| **Archivo único** | Todo el código (HTML, CSS, JS) está en `GeneradorDeDatos.html` |
| **Renderizado** | Canvas 2D (`<canvas>`) para el plano cartesiano y los puntos |
| **Interacción** | Pointer Events API (unifica mouse y touch) |
| **Exportación CSV** | Generación nativa con `Blob` + URL.createObjectURL |
| **Exportación XLSX** | Librería SheetJS v0.18.5 cargada desde CDN |
| **Tipografías** | IBM Plex Mono (monoespaciada) + Outfit (sans-serif) desde Google Fonts |
| **Tema visual** | Modo oscuro con paleta de colores CSS custom properties |
| **Layout** | Flexbox. El lienzo ocupa todo el espacio vertical disponible |

### Sistema de coordenadas

El lienzo usa un sistema de doble coordenadas:

- **Coordenadas de píxel** (`px`, `py`): Posición real en el canvas HTML.
- **Coordenadas de datos** (`dx`, `dy`): Valor real mapeado al rango configurado por el usuario.

Las funciones `px2data()` y `data2px()` convierten entre ambos sistemas, teniendo en cuenta el padding interno del lienzo (márgenes para ejes y etiquetas).

---

## Casos de Uso

1. **Docencia en Machine Learning**: Crear datasets de ejemplo para demostrar algoritmos de clasificación (K-NN, SVM, árboles de decisión) con distribuciones espaciales controladas.

2. **Prototipado de modelos**: Generar datos rápidos con formas específicas (espirales, lunas, clusters superpuestos) para probar pipelines de análisis.

3. **Investigación científica**: Crear datos sintéticos de referencia para validar métodos estadísticos o visualizaciones.

4. **Presentaciones y publicaciones**: Generar datasets de demostración con distribuciones visualmente claras para figuras y gráficos.

5. **Ejercicios y talleres**: Los estudiantes pueden crear sus propios datasets y explorar cómo diferentes distribuciones afectan los resultados de algoritmos.

---

## Limitaciones Conocidas

- Los datos se almacenan solo en memoria del navegador. **Si se recarga la página, todos los puntos se pierden.** Exportar antes de cerrar.
- El número máximo de grupos está limitado visualmente por la paleta de 8 colores (aunque se pueden crear más grupos, los colores se repiten).
- No hay funcionalidad de deshacer/rehacer (undo/redo). Para corregir errores se debe usar el modo borrador.
- En dispositivos táctiles, el pincel de tamaño 1 puede ser difícil de controlar con precisión.
- La exportación XLSX requiere que la librería SheetJS se haya cargado correctamente desde el CDN.
