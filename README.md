# Challenge-Telecom-X-an-lisis-de-evasi-n-de-clientes---Parte-2
Challenge
# 📈 Proyecto de Predicción de Cancelación de Clientes (Churn) - TelecomX LATAM

## 🎯 Introducción y Objetivos del Proyecto

Este proyecto tiene como objetivo desarrollar un pipeline robusto para prever qué clientes de TelecomX LATAM tienen una mayor probabilidad de cancelar sus servicios (`Churn`). La capacidad de anticipar la cancelación permite a la empresa implementar estrategias de retención proactivas, mejorando la lealtad del cliente y reduciendo la pérdida de ingresos.

Los objetivos clave de este desafío incluyen:
* Preparar y preprocesar los datos para el modelado (tratamiento de nulos, codificación de categóricas, normalización).
* Realizar un análisis de correlación y selección de variables para entender la relación de las características con el `Churn`.
* Entrenar y evaluar al menos dos modelos de clasificación predictiva.
* Interpretar los resultados de los modelos, incluyendo la importancia de las variables.
* Proponer conclusiones estratégicas y recomendaciones basadas en los hallazgos para mitigar la cancelación.

---

## 💾 Datos

El dataset utilizado contiene información detallada de clientes de TelecomX LATAM, incluyendo datos demográficos, servicios contratados, información de facturación y, crucialmente, si el cliente ha cancelado el servicio.

**Forma del DataFrame inicial:** `(7267, 20)`
**Forma del DataFrame después del preprocesamiento:** `(7267, 24)` (después de One-Hot Encoding y adición de `Cuentas_Diarias`)

---

## 🛠️ Pipeline de Preprocesamiento de Datos

Los datos fueron sometidos a un riguroso proceso de limpieza y preparación para asegurar su idoneidad para el modelado:

1.  **Carga y Aplanamiento:** Los datos se cargaron desde un archivo JSON y se aplanaron para su fácil manipulación.
2.  **Tratamiento de `account.Charges.Total`:**
    * Valores en blanco fueron convertidos a `NaN`.
    * La columna fue convertida a tipo numérico (`float`).
    * Los valores `NaN` (para clientes nuevos sin cargos totales aún) fueron imputados con la **mediana** de la columna.
3.  **Limpieza de `Churn`:** Se eliminaron registros con valores vacíos en la columna `Churn` y se normalizaron a 'yes'/'no'.
4.  **Ingeniería de Características:** Se creó la nueva columna `Cuentas_Diarias` (`account.Charges.Monthly / 30`).
5.  **Codificación de la Variable Objetivo:** La variable `Churn` fue mapeada a valores numéricos (`yes` = 1, `no` = 0).
6.  **Codificación de Variables Categóricas:** Se aplicó **One-Hot Encoding** a todas las columnas categóricas (ej., servicios de internet, tipo de contrato, método de pago) para convertirlas en un formato numérico adecuado para los modelos.
7.  **Normalización de Variables Numéricas:** Las columnas numéricas continuas (`customer.tenure`, `account.Charges.Monthly`, `account.Charges.Total` y `Cuentas_Diarias`) fueron escaladas utilizando **`StandardScaler`** para asegurar que no tuvieran un peso desproporcionado en los modelos debido a sus diferentes rangos.
8.  **División de Datos:** El dataset se dividió en conjuntos de entrenamiento (80%) y prueba (20%) utilizando `train_test_split` con `stratify=y` para mantener la proporción de clases de `Churn` en ambos conjuntos.

---

## 🧠 Modelos de Clasificación Evaluados

Se entrenaron y evaluaron los siguientes modelos de clasificación para predecir el `Churn`:

| Modelo | Accuracy | Precision | Recall | F1 | AUC |
| :------------------ | :------- | :-------- | :------- | :------- | :------- |
| **Regresión Logística** | 0.735 | 0.501 | **0.800** | 0.616 | **0.841** |
| Decision Tree | 0.735 | 0.501 | 0.790 | 0.613 | 0.828 |

**Conclusión del Modelo Seleccionado:**

La **Regresión Logística** fue seleccionada como el modelo más adecuado para este caso de uso. Aunque el `Accuracy` es similar en ambos, la Regresión Logística demostró un `Recall` ligeramente superior (80%) y un `AUC` más alto (0.841). En el contexto de la predicción de `Churn`, un `Recall` alto es fundamental para asegurar que la empresa detecte la mayor cantidad posible de clientes en riesgo, permitiendo intervenciones proactivas.

---

## 📊 Hallazgos Clave y Factores Influyentes en el Churn

El análisis de correlación y los coeficientes de la Regresión Logística revelan los siguientes factores principales que influyen en la cancelación:

### Factores que AUMENTAN la Probabilidad de Churn:

* **Antigüedad Baja (`customer.tenure`):** Los clientes más recientes son significativamente más propensos a cancelar.
* **Cargos Mensuales Elevados (`account.Charges.Monthly`):** Un mayor costo mensual se correlaciona con una mayor probabilidad de `Churn`.
* **Facturación sin Papel (`account.PaperlessBilling`):** Los clientes que optan por esta modalidad muestran una ligera mayor propensión a cancelar.
* **Cliente de Tercera Edad (`customer.SeniorCitizen`):** Este segmento demográfico también presenta una mayor tendencia al `Churn`.
* **Servicio de Fibra Óptica (`internet.InternetService_fiber optic`):** Curiosamente, este servicio de alta velocidad también mostró una correlación positiva, lo que podría indicar que las expectativas de estos clientes son más altas o que los problemas de servicio son más frustrantes para ellos.

### Factores que DISMINUYEN la Probabilidad de Churn (Factores de Retención):

* **Servicios de Seguridad y Soporte:** `internet.OnlineSecurity`, `internet.TechSupport`, `internet.OnlineBackup` y `internet.DeviceProtection` son fuertes indicadores de retención. Los clientes que contratan estos servicios adicionales son menos propensos a cancelar.
* **Contratos de Mayor Duración:** Los clientes con contratos de **uno o dos años** (`account.Contract_one year`, `account.Contract_two year`) tienen una probabilidad significativamente menor de cancelar en comparación con los contratos mes a mes.
* **Tener Pareja (`customer.Partner`) o Dependientes (`customer.Dependents`):** Estos clientes muestran una mayor estabilidad y menor propensión al `Churn`.

---

## 🚀 Conclusiones Estratégicas y Recomendaciones

El modelo de Regresión Logística es una herramienta valiosa para identificar clientes en riesgo de `Churn`. Para mitigar la cancelación, TelecomX LATAM debería implementar las siguientes estrategias:

1.  **Programas de Retención Temprana:** Enfocarse en la **fidelización de nuevos clientes** desde el inicio de su relación, ofreciendo soporte proactivo y una excelente experiencia inicial.
2.  **Promoción de Servicios de Valor Añadido:** Incentivar la contratación de **seguridad en línea, soporte técnico, backup y protección de dispositivos**. Estos servicios no solo generan ingresos adicionales, sino que son potentes factores de retención.
3.  **Revisión y Optimización de Precios:** Analizar los planes de clientes con **cargos mensuales altos** y riesgo de `Churn`, ofreciendo alternativas más competitivas o personalizadas para mejorar la percepción de valor.
4.  **Incentivar Contratos a Largo Plazo:** Promocionar activamente los **contratos de uno y dos años** con beneficios claros, ya que estos clientes demuestran una lealtad significativamente mayor.
5.  **Análisis Específico de Clientes de Fibra Óptica:** Investigar las causas del `Churn` en este segmento de clientes de alto valor para mejorar su satisfacción.
6.  **Atención Segmentada:** Considerar campañas o servicios especializados para clientes de la **tercera edad** y hogares con **pareja o dependientes**, adaptándose a sus necesidades y reforzando los puntos de retención.

Al centrarse en estos factores clave, TelecomX LATAM puede desarrollar estrategias de retención más dirigidas y efectivas, transformando la anticipación del `Churn` en una ventaja competitiva.

---
Autor
Carolina Venegas
