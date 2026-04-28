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
- Librerías:
  - pandas
  - numpy
  - matplotlib
  - seaborn

- Control de versiones:
  - Git / GitHub

---

## Estructura del Notebook

### Importación de datos
- Carga del dataset desde CSV
- Visualización inicial con `.head()`

---

### Análisis exploratorio inicial
- Inspección con:
  - `.info()`
  - `.shape`
  - `.describe()`
- Identificación de:
  - Variables numéricas y categóricas
  - Columnas de fecha mal tipadas
  - Alta variabilidad en variables numéricas

---

### Diagnóstico de problemas

#### Valores nulos
Columnas con mayor cantidad:
- `tags` (~171k)
- `region` (~56k)
- `funded_time` (~48k)
- `partner_id` (~13k)

#### Duplicados
- No se detectaron duplicados

#### Tipos incorrectos
- Fechas en formato `object`
- Variables numéricas como `float` por nulos

#### Inconsistencias
- Texto con espacios y formatos distintos
- Columnas con múltiples valores en una celda (`borrower_genders`, `tags`)

#### Outliers
- Valores extremos en `loan_amount`, `funded_amount`, `lender_count`

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
- `disbursed_time` → inconsistencias temporales

---

### Tratamiento de valores nulos
- `region` → imputado como `"Unknown"`
- `use` → imputado como `"Unknown"`
- `borrower_genders` → eliminación de filas nulas
- `funded_time` → se mantienen nulos (significado real: no financiado)
- `country_code` → se mantiene sin imputar

---

### Limpieza de texto
- Eliminación de espacios extra (`strip`, regex)
- Normalización:
  - `country`, `sector`, `activity` → Title Case
  - `currency`, `country_code` → MAYÚSCULAS
  - `borrower_genders`, `repayment_interval` → minúsculas
- Correcciones específicas:
  - eliminación de puntos finales en `use`

---

### Reglas de coherencia
- Eliminación de registros donde:
  - `funded_amount > loan_amount`

---

### Outliers
- Detectados pero **NO eliminados**
- Justificación: representan casos reales del negocio

---

### Feature Engineering
- `gender_group`:
  - female
  - male
  - mixto

- `year`:
  - extraído de la fecha

- `funding_ratio`:
  - proporción financiada

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
- **df_clean**: dataset final tras el proceso de limpieza.

Esto permite:
- Comparar cambios realizados
- Validar transformaciones
- Garantizar reproducibilidad

El dataset final se exporta en formato CSV para su uso posterior.

---

## Análisis visual (post-limpieza)
Se incluyen gráficos para validar y entender los datos:

- Distribución de préstamos (<10.000)
- Top 5 sectores por presencia internacional
- Evolución temporal:
  - Número de préstamos
  - Tasa de financiación
- Análisis por género y país

---

## Principales insights

- Fuerte crecimiento de préstamos entre **2014–2016**
- Alta tasa de financiación (~95–98%)
- Predominio claro de mujeres:
  - ~95% en países como Filipinas
- Filipinas destaca como el país con mayor volumen de préstamos

---

## Cómo ejecutar el proyecto

1. Clonar el repositorio:
```bash
git clone <URL>
```

2. Abrir en Google Colab

3. Ejecutar todas las celdas en orden

---

## Entregables
- Notebook `.ipynb` completo y documentado
- Dataset limpio (CSV)
- README explicativo

---

## Buenas prácticas aplicadas
- Reproducibilidad total
- Documentación clara (markdown + comentarios)
- Justificación de cada decisión
- Minimización de pérdida de datos
- Validaciones finales

---

## Conclusión
El proyecto demuestra un proceso completo de limpieza de datos, abordando problemas reales como valores nulos, inconsistencias y normalización.

El dataset final es consistente, interpretable y preparado para análisis avanzados o modelado.

---