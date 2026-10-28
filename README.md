# 🏢 Análisis Comercial y de Retención Inmobiliaria | Power BI

## 📌 Descripción del Proyecto
Este proyecto presenta una solución integral de **Business Intelligence en Microsoft Power BI** diseñada para evaluar el desempeño comercial, la rentabilidad por canal, el mix de propiedades y la recurrencia de clientes en el sector inmobiliario.

A partir de un modelo relacional en **Esquema Estrella** y el desarrollo de métricas avanzadas en **DAX**, el dashboard traduce un volumen transaccional de **8,500 propiedades vendidas ($6,012 Millones en ingresos)** en decisiones estratégicas sobre captación de inmuebles, incentivos para corredores y fidelización de compradores.

---

## 🧹 1. Limpieza de Datos y Control de Calidad (ETL)
Previo a la construcción del modelo relacional, se realizó el proceso de auditoría y transformación de las fuentes de datos para garantizar la integridad de los KPIs:
* **Estandarización de tipos de datos:** Conversión del campo `fecha_venta` a formato `Date`, ajuste de variables monetarias (`precio_venta`) a formato numérico decimal y configuración de `porcentaje_comision` como tipo porcentual.
* **Tratamiento de valores nulos:** Revisión exhaustiva en tablas de dimensiones y de hechos, verificando que no existieran registros huérfanos ni valores ausentes en campos críticos para el cálculo de ingresos y comisiones.
* **Validación de unicidad (Claves Primarias):** Comprobación de ausencia de duplicados en los identificadores únicos de `dim_clientes` y `dim_propiedades`, asegurando relaciones `1:*` limpias y sin distorsión en las agregaciones.

---

## 🧩 2. Arquitectura del Modelo de Datos (Esquema Estrella)
El modelo se diseñó siguiendo las mejores prácticas de modelado dimensional en formato **Estrella (Star Schema)**, con relaciones activas de cardinalidad **uno a muchos (`1:*`)** y dirección de filtro simple (*single*):

* **`hecho_ventas_propiedades` (Tabla de Hechos Central):** Contiene el registro granular de las transacciones de venta (precio, fecha, cliente, propiedad y canal).
* **`dim_clientes` (Dimensión):** Atributos demográficos y segmentación de los compradores (*Primera vez, Inversionista, Alto Patrimonio*).
* **`dim_propiedades` (Dimensión):** Características físicas y geográficas del catálogo inmobiliario (*Casa, Comercial, Departamento*, tamaño y ciudad).
* **`dim_fecha` (Tabla Calendario DAX):** Construida dinámicamente mediante las funciones `CALENDAR` y `ADDCOLUMNS` abarcando desde la fecha mínima hasta la máxima de `hecho_ventas_propiedades`. Incluye las jerarquías de `Año`, `Mes (nombre)`, `Número de mes` y `Año-Mes` (`YYYY-MM`), ordenadas cronológicamente por su clave numérica para garantizar la lectura secuencial en series de tiempo.

---

## 📐 3. Desarrollo de Medidas DAX y Cálculos

### 🔹 Medidas Base (KPIs Principales)
* **Ingreso Total:** Suma total de `precio_venta` ($6,012,502,170).
* **Cantidad de Ventas:** Conteo total de transacciones cerradas (8,500 unidades).
* **Ticket Promedio:** Ingreso promedio generado por cada propiedad vendida ($707,353).
* **Comisión Total:** Suma de las comisiones captadas por la empresa ($201 Millones).

### 🔹 Medidas con Modificación de Contexto de Filtro (`CALCULATE` / `ALL`)
Implementadas para evaluar el peso relativo (%) de cada categoría sobre el total general de ingresos e integradas en *tooltips* informativos:
* **% Participación por Tipo de Propiedad**
* **% Participación por Canal de Venta**
* **% Participación por Segmento de Cliente**

### 🔹 Inteligencia de Tiempo (Time Intelligence)
* **Ventas Acumuladas del Año (YTD - Year to Date)** y **MTD (Month to Date)**.
* **Ventas del Año Anterior (PY)** y **Comparativa / Crecimiento Interanual (YoY)**.

### 🔹 Ingeniería de Columnas para Análisis de Cohortes
Implementadas como columnas calculadas en `hecho_ventas_propiedades` para rastrear el ciclo de vida del cliente:
* **`Primera compra por cliente`:** Fecha mínima de adquisición registrada por cada `ID_Cliente`.
* **`Mes Cohorte`:** Mes y año de adquisición inicial del cliente.
* **`Mes Venta`:** Mes en que ocurre cada transacción subsecuente para medir la retención en el tiempo.

---

## 🖥️ 4. Estructura del Dashboard (3 Páginas)

1. **Vista 1 — Overview Ejecutivo:**
   * Tarjetas con los **4 KPIs principales** (Ingreso Total, Cantidad de Ventas, Ticket Promedio y Comisión Total).
   * Gráfico de evolución temporal de ventas ordenado cronológicamente por `Año-Mes`.
   * Desempeño geográfico de ingresos por ciudad.
   * Análisis comparativo de rendimiento interanual (**YoY** vs. Año Anterior) ordenado de enero a diciembre mediante `Número de mes`.
2. **Vista 2 — Análisis Comercial:**
   * Desglose de ingresos y volumen por **Tipo de Propiedad**, **Canal de Venta** y **Segmento de Cliente** complementados con *tooltips* de participación porcentual.
   * **Tabla Comercial con Formato Condicional (Semáforo):** Evalúa `tipo_propiedad`, `Ingreso Total`, `Cantidad Ventas` y `Ticket Promedio` para identificar contrastes entre volumen operativo y rentabilidad.
3. **Vista 3 — Análisis de Cohortes:**
   * **Matriz de Retención y Recurrencia:** Cruza `Mes Cohorte` (filas) contra `Mes Venta` (columnas) evaluando el volumen de recompras e ingresos a lo largo del tiempo.

---

## 💡 5. Resumen Ejecutivo y Hallazgos Clave

### 📊 Métricas Principales del Periodo
| Indicador (KPI) | Valor |
| :--- | :--- |
| **Ingreso Total** | **$6,012,502,170** (~$6,012 Millones) |
| **Cantidad de Ventas** | **8,500 propiedades** |
| **Ticket Promedio** | **$707,353** |
| **Comisión Total** | **$201 Millones** |

### 🔍 Insights Accionables
* **Discrepancia entre Volumen y Valor por Tipo de Propiedad:** Las propiedades tipo **Casa** lideran la facturación (superando los **$2.24 mil millones**), seguidas muy de cerca por el sector **Comercial**. En contraste, los **Departamentos** concentran el mayor volumen operativo con **5,105 ventas**, pero aportan el menor ingreso total (identificado en rojo en la matriz de semáforo) debido a un ticket promedio considerablemente más bajo.
* **Liderazgo Geográfico:** **Ciudad de México** se posiciona como el mercado número uno en generación de ingresos, superando ligeramente a **Bogotá**.
* **Dominio del Canal de Corredores:** El canal **Corredor** es el motor comercial de la empresa, generando un contundente **72.85%** de las ventas totales frente a una baja penetración del canal **Directo**.
* **Perfil del Comprador:** El segmento de clientes de **"Primera vez"** concentra la gran mayoría de los ingresos totales, superando por amplio margen a los segmentos de *Inversionistas* y *Alto Patrimonio*.
* **Comportamiento de Retención (Cohortes):** Las cohortes adquiridas en el **Q1 de 2023 (enero – marzo)** presentan la mayor estela de actividad posterior; no obstante, a nivel general, la tasa de recompra cae drásticamente después del primer mes de adquisición.

---

## 🎯 6. Recomendaciones Estratégicas de Negocio
1. **Reorientación del Esfuerzo Comercial hacia Propiedades de Alto Ticket:** Priorizar la captación y promoción de inmuebles tipo **Casa** y **Comercial**, ya que requieren un menor volumen transaccional (menor desgaste operativo) para maximizar el impacto en el *revenue* global y elevar el ticket promedio.
2. **Blindaje del Canal Corredor y Auditoría del Canal Directo:** Fortalecer la red de **Corredores** mediante esquemas de incentivos y comisiones escalonadas para proteger el 72.85% de la facturación. Paralelamente, auditar el embudo de adquisición del canal **Directo** para detectar cuellos de botella en conversión.
3. **Programa de Transición de "Primera Vez" a "Inversionista":** Diseñar estrategias de *remarketing* y asesoría patrimonial dirigidas a los compradores de **Primera vez** (el segmento más grande de la cartera) con el objetivo de incentivar una segunda compra de inversión y elevar las tasas de retención en la matriz de cohortes.

---

## 🚀 Cómo visualizar este proyecto
1. Clona este repositorio o descarga el archivo `.pbix` directamente.
2. Ábrelo utilizando **Microsoft Power BI Desktop**.
