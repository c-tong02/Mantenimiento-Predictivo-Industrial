# Mantenimiento Predictivo Industrial

### Descripción del proyecto

El objetivo de este proyecto es predecir fallas en máquinas industriales usando datos de sensores en tiempo real. Se abordan dos problemas complementarios: detectar si una máquina va a fallar (clasificación binaria) y diagnosticar el tipo específico de falla cuando ocurra (clasificación multiclase). El propósito es habilitar el paso de un mantenimiento reactivo a uno predictivo, reduciendo paradas no planificadas y costos asociados al downtime industrial.

### Fuentes de dato

La base de datos utilizada para este proyecto es el dataset AI4I 2020 Predictive Maintenance, disponible en el archivo "ai4i2020.csv" y publicado originalmente en Kaggle. Contiene 10,000 registros de sensores industriales con variables de temperatura del aire, temperatura del proceso, velocidad rotacional, torque, desgaste de herramienta, tipo de producto y 5 indicadores de tipos de falla (TWF, HDF, PWF, OSF, RNF).

### Herramientas

* Python — EDA, ingeniería de variables y modelamiento
* scikit-learn — modelos clásicos de Machine Learning
* CatBoost — gradient boosting
* Metodología CRISP-DM

### Limpieza / preparación de datos

En la fase inicial de preparación de datos, se hizo lo siguiente:

1. Carga de data e inspección de tipos y valores nulos.
2. Detección y eliminación de data leakage (las columnas de tipo de falla coincidían en 99.7% con el target).
3. Análisis de correlación y distribución de variables por clase.
4. Ingeniería de variables basada en conocimiento de dominio industrial: torque_speed_ratio, power, wear_torque, overheat_indicator, overstrain_indicator, power_anomaly, entre otras.
5. Codificación de variables categóricas y escalamiento con StandardScaler.
6. Manejo del desbalance de clases con class_weight balanceado.

### Modelamiento de datos

Se entrenaron 5 modelos de Machine Learning para cada uno de los dos casos del proyecto, comparando rendimiento con validación cruzada estratificada (5-fold).

* Modelos: Logistic Regression, Decision Tree, Random Forest, Gradient Boosting y CatBoost.
* Caso 1 — Clasificación binaria: predecir si una máquina va a fallar.
* Caso 2 — Clasificación multiclase: predecir el tipo específico de falla entre 7 categorías.
* Métricas binario: AUC-ROC, F1, Recall y Precision.
* Métricas multiclase: F1-macro, AUC One-vs-Rest, Recall macro y Precision macro.

### Resultados

Los resultados de este proyecto son:

**Caso 1 — Detección de fallas (binario):**

1. Random Forest obtuvo el mejor rendimiento general con AUC-ROC = 0.987 y Precision = 94.9%.
2. CatBoost lideró en Recall (86.8%), detectando 59 de 68 fallas reales en el conjunto de prueba.
3. La elección entre RF y CatBoost depende del costo relativo de cada error: RF para minimizar falsas alarmas, CatBoost para minimizar fallas no detectadas.

**Caso 2 — Diagnóstico del tipo de falla (multiclase):**

1. Random Forest obtuvo el mejor F1-macro (0.681) entre los 5 modelos.
2. CatBoost obtuvo el mejor AUC One-vs-Rest (0.926), superando a Random Forest en ranking de probabilidades.
3. Tres tipos de falla se predicen con excelente rendimiento: PWF (F1 = 1.00), HDF (F1 = 0.98) y OSF (F1 = 0.91).
4. Dos tipos de falla resultaron problemáticos en todos los modelos: TWF (F1 < 0.10) por overlap con OSF y RNF (F1 = 0.00) por ser fallas aleatorias sin patrón.

**Aplicación recomendada:**

5. En producción, ambos modelos forman un pipeline jerárquico: el binario filtra primero (AUC 0.99) y, cuando detecta riesgo, el multiclase diagnostica el tipo específico para despachar el recurso correcto al equipo de mantenimiento.
