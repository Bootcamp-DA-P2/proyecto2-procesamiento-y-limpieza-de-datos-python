# Procesamiento y Limpieza de Datos – Kiva Crowdfunding

## Autores
### Proyecto 2 - Grupo 1
- Elena Suárez Serrano  
- Miguel Ángel Moreno Delgado  
- Irene Condado Alcantarilla 

---

## Descripción del Proyecto
Este proyecto tiene como objetivo preparar y limpiar el dataset de **Kiva Crowdfunding**, con el fin de obtener un conjunto de datos estructurado, consistente y listo para análisis.

Cada fila del dataset representa un préstamo de microfinanciación gestionado por Kiva. A lo largo del proyecto se aplican técnicas de exploración, diagnóstico y limpieza siguiendo buenas prácticas de reproducibilidad y trazabilidad.

---

## Objetivos
- Analizar la estructura y calidad del dataset original.
- Detectar problemas (valores nulos, tipos incorrectos, inconsistencias).
- Aplicar técnicas de limpieza justificadas.
- Generar un dataset final limpio y utilizable.
- Documentar todas las decisiones tomadas.

---

## Dataset
- Dataset: **Kiva Crowdfunding (Kaggle)**
- Registros iniciales: **671.205 filas**
- Columnas iniciales: **20**

Incluye información sobre:
- Cantidad solicitada y financiada
- País y región
- Sector y actividad
- Género de los prestatarios
- Fechas del préstamo

---

## Tecnologías utilizadas
- Python
- Google Colab
- Librerías: `pandas`, `numpy`, `matplotlib`, `seaborn`
- Control de versiones: `Git / GitHub`

---

## Estructura del Notebook

### Importación de datos
- Carga del dataset desde CSV
- Visualización inicial con `.head()`
- Creación de copia `df_raw` para preservar el dataset original sin modificaciones

---

### Análisis exploratorio inicial
- Inspección con: `.info()`, `.shape`, `.describe()`

#### Observaciones principales:

- El dataset contiene 671.205 filas y 20 columnas.
- Mezcla de variables numéricas (`funded_amount`, `loan_amount`, `lender_count`) y categóricas (`sector`, `country`, `repayment_interval`).
- Las columnas de fecha (`posted_time`, `disbursed_time`, `funded_time`, `date`) están almacenadas como object.
- `partner_id` y `term_in_months` están almacenadas como `float64` por la presencia de nulos.
- Las variables `funded_amount` y `loan_amount` presentan rangos amplios; `lender_count` varía de 0 a 2.986.

---

### Diagnóstico de problemas

#### Valores nulos
Columnas con mayor número de valores faltantes:
 - `tags`: 171.416 nulos
 - `region`: 56.800 nulos
 - `funded_time`: 48.331 nulos
 - `partner_id`: 13.507 nulos
 - `use`: 4.232 nulos
 - `borrower_genders`: 4.221 nulos
 - `disbursed_time`: 2.396 nulos
 - `country_code`: 8 nulos

#### Duplicados
- No se detectaron duplicados

#### Tipos incorrectos
- Fechas en formato `object`
- Variables numéricas como `float64` por presencia de nulos

#### Inconsistencias
- Texto con espacios y formatos distintos en variables en `use`
- Columnas con múltiples valores en una celda (`borrower_genders`, `tags`)
- `disbursed_time` anterior a `posted_time` en algunos registros (inconsistencia lógica en el ciclo del préstamo)

#### Outliers
- Valores extremos en `loan_amount`, `funded_amount`, `term_in_months`, `lender_count`
- Visualizados mediante boxplots con escala logarítmica para cada variable numérica clave

---

## Proceso de limpieza (decisiones clave)

### Conversión de tipos
- Fechas convertidas a `datetime`:
  - `posted_time`, `disbursed_time`, `funded_time`, `date`
- Variables numéricas corregidas:
  - `term_in_months`
  - `partner_id`

---

### Eliminación de columnas
- `tags` → demasiados nulos y poco valor analítico  
- `partner_id` → alto número de nulos y poco uso  
- `disbursed_time` → inconsistencias temporales (regitros con fecha de desembolso anterior a la publicación)

---

### Tratamiento de valores nulos
- `region` → imputado como `"Unknown"`
- `use` → imputado como `"Unknown"`
- `borrower_genders` → eliminación de filas nulas (variable clave para el análisis)
- `funded_time` → se mantienen nulos (significado real: préstamo aún no financiado)
- `country_code` → se mantiene sin imputar; se propone mejora futura mediante validación automática desde el campo `country`

---

### Normalización de texto
- Eliminación de espacios extra
- Estandarización de formato por columna:
  - `activity`, `sector`, `country`, `region`,  → Title Case
  - `currency`, `country_code` → MAYÚSCULAS
  - `borrower_genders`, `repayment_interval` → minúsculas
  - `use` → eliminación de puntos finales + primera letra en mayúscula (capitalize)

---

### Reglas de coherencia
- Eliminación de registros donde:
  - `funded_amount > loan_amount`

---

### Outliers
- Detectados pero **no eliminados** representan casos reales del negocio (heterogeneidad propia de la microfinanciación)

---

### Feature Engineering
- `gender_group`:clasifica cada préstamo según el género de los prestatarios: `Female`, `Male` y `Mixto`
- `year`: año extraído de la columna `date`
- `funding_ratio`: proporción financiada (`funded_amount`/`loan_amount`), calculada en la fase de visualización

---

## Validación
- Verificación de:
  - Eliminación de nulos en columnas clave
  - Coherencia de datos
  - Tipos correctos
- Comparación antes/después del dataset

---

### Snapshots del dataset
Durante el proceso se guardaron dos versiones del dataset:
- **df_raw**: copia del dataset original sin modificaciones, utilizada para mantener trazabilidad.
- **df_clean**: dataset final tras el proceso de limpieza, exportado como `.csv`

Esto permite:
- Comparar cambios realizados
- Validar transformaciones
- Garantizar reproducibilidad

El dataset final se exporta en formato CSV para su uso posterior.

---

## Análisis visual (EDA post-limpieza)
Se incluyen cuatro gráficos para validar y entender los datos limpios:

1. Distribución de montos de préstamos (< 10.000 USD)
Histograma con KDE que muestra que la gran mayoría de los préstamos son pequeños. El 80% de la cartera se sitúa por debajo de 1.000 USD, lo que indica una entidad de alto volumen y bajo monto cuya estabilidad depende de los pequeños préstamos.
2. Top 5 sectores por presencia internacional
Gráfico de barras horizontales con el número de países únicos por sector. No existe una brecha significativa entre los cinco primeros, lo que refleja una distribución equilibrada de la actividad a nivel global.
3. Evolución temporal de solicitudes y tasa de financiación
Gráfico de doble eje que combina barras (número de solicitudes por año) y línea (tasa media de financiación). Muestra un crecimiento sostenido entre 2014 y 2016, con una caída en 2017 explicada por la incompletitud del año en el dataset. La tasa de financiación se mantiene entre el 95 y el 98%.
4. Comparación por país y género / distribución en Filipinas
Gráfico de barras agrupadas con los 10 países con más préstamos y un gráfico circular específico para Filipinas. Reflejan el predominio del grupo femenino (~95% en Filipinas) y la posición destacada de Filipinas como principal mercado en volumen.

---

## Principales insights
- Fuerte crecimiento de préstamos entre 2014 y 2016, con máximo en 2016.
- Alta tasa de financiación (~95–98%).
- Predominio claro de mujeres: ~95% en países como Filipinas.
- Filipinas es el país con mayor volumen de préstamos y la distribución de género más sesgada.
- La cartera es de alto volumen y bajo monto: el 80% de los préstamos es inferior a 1.000 USD.
- Los cinco sectores con mayor presencia internacional están distribuidos de forma equilibrada entre países.

---

## Cómo ejecutar el proyecto

1. Clonar el repositorio:
```bash
git clone <URL>
```

2. Abrir el notebook en Google Colab

3. Ejecutar todas las celdas en orden

---

## Entregables
- Notebook `.ipynb` completo y documentado
- Dataset limpio (kiva_clean.csv)
- README explicativo

---

## Buenas prácticas aplicadas
- Reproducibilidad total
- Documentación clara (markdown + comentarios en código)
- Justificación de cada decisión de limpieza
- Minimización de pérdida de datos
- Validaciones finales antes y despúes de cada transformación

---

## Conclusión
El proyecto demuestra un proceso completo de limpieza de datos, abordando problemas reales como valores nulos, inconsistencias temporales, duplicados implícitos por formato y normalización textual. 

El dataset final es consistente, interpretable y está preparado para análisis avanzados o modelado.

---

## 🐧
*Así nos sentimos tras limpiar 671.205 filas de datos...*
![Grupo 1](img/grupo1.gif)
