# Guía Práctica: Google Trends en la Investigación Científica y Social

Google Trends es una herramienta gratuita proporcionada por Google que analiza la popularidad de las consultas de búsqueda en el motor de búsqueda de Google a lo largo del tiempo y en diferentes regiones geográficas. En el ámbito académico e interdisciplinario, se ha consolidado como un recurso clave de **infodemiología**, **analítica predictiva** y **sociología computacional**.

---

## 1. ¿Qué es Google Trends?

A diferencia de las bases de datos estadísticas tradicionales que ofrecen volúmenes absolutos, Google Trends provee **Volúmenes de Búsqueda Relativos (RSV - Relative Search Volume)**. 

### Características Clave de los Datos
- **Normalización (Escala 0 a 100):** Las puntuaciones no reflejan el número total de búsquedas, sino la proporción de búsquedas de un término respecto al total de búsquedas en un lugar y tiempo determinado.
  - **100:** Punto de máxima popularidad de la palabra clave en el período analizado.
  - **50:** La mitad de la popularidad máxima.
  - **0:** Datos insuficientes para el término elegido.
- **Muestreo:** Los datos presentados se basan en muestras representativas y no sesgadas de los miles de millones de búsquedas diarias.
- **Anonimización:** Ningún dato permite identificar individualmente a los usuarios.

---

## 2. Ventajas y Limitaciones para Investigación

### Ventajas
- **Acceso a datos en tiempo real:** Permite estudiar comportamientos inmediatos frente a eventos críticos (pandemias, elecciones, crisis económicas).
- **Cobertura geográfica y temporal masiva:** Datos disponibles desde 2004 a nivel global, nacional y subnacional.
- **Sin sesgo de deseabilidad social:** Captura lo que las personas realmente buscan en privado, sin la presión social que ocurre en encuestas o entrevistas.

### Limitaciones y Sesgos
- **Opacidad del algoritmo:** Google no revela la fórmula exacta del muestreo ni del proceso de normalización.
- **Sesgo de acceso:** Representa únicamente a la población con acceso a Internet que utiliza Google como motor de búsqueda.
- **Variabilidad en consultas cortas:** Las búsquedas pequeñas pueden mostrar fluctuaciones por variaciones en el muestreo diario.

---

## 3. Metodología de Uso en Proyectos de Investigación

Para integrar Google Trends de forma rigurosa en una investigación cuantitativa o cualitativa, se recomienda seguir este flujo de trabajo:

### Paso 1: Definición de Términos vs. Temas (Topics)
- **Términos de búsqueda:** Coincidencia exacta de palabras (ej. *inflación*). Sensible a errores ortográficos e idiomas.
- **Temas (Topics):** Grupos de conceptos que comparten una misma idea en cualquier idioma (ej. *Inflación - Tema económico*). **Se recomienda usar Temas** para evitar sesgos lingüísticos y variaciones léxicas.

### Paso 2: Selección de la Ventana Temporal y Geográfica
- Delimita con precisión el marco geográfico (País, Estado/Provincia o Ciudad).
- Elige el rango temporal adecuado. Ten en cuenta que si el rango es mayor a 5 años, Google Trends agrega los datos de forma semanal o mensual en lugar de diaria.

### Paso 3: Descarga y Normalización de Datos
1. Exporta los datos en formato `.csv` directamente desde la interfaz.
2. Si vas a comparar términos en diferentes períodos o regiones que no se analizaron en la misma consulta, debes aplicar una **renormalización matemática** mediante una variable de anclaje (un término constante usado como punto de referencia).

### Paso 4: Análisis Estadístico
Los datos extraídos se suelen analizar con metodologías como:
- **Análisis de Series Temporales:** Modelos ARIMA o SARIMA para predecir tendencias futuras (*nowcasting*).
- **Correlación de Pearson/Spearman:** Comparación con indicadores reales (ej. índices de desempleo vs. búsquedas de "trabajo").
- **Análisis de Regresión:** Evaluar el impacto de eventos externos en el interés público.

---

## 4. Ejemplos de Aplicación Científica

1. **Salud Pública (Infodemiología):**
   - Rastreo de brotes de enfermedades (ej. monitoreo de la gripe o síntomas de COVID-19 antes de los reportes oficiales).
2. **Economía y Finanzas (*Nowcasting*):**
   - Predicción del consumo del hogar, la intención de compra de vivienda o la tasa de desempleo a corto plazo.
3. **Ciencia Política y Sociología:**
   - Medición del interés del electorado en candidatos políticos durante campañas electorales.
   - Análisis de la estigmatización o preocupación social sobre temas sensibles (salud mental, discriminación, etc.).

---

## 5. Buenas Prácticas para la Publicación Académica

Si vas a incluir datos de Google Trends en una publicación o tesis:
- **Registra la fecha y hora de extracción:** Puesto que el muestreo de Google se actualiza continuamente, los valores pueden sufrir pequeñas variaciones si se descargan en momentos distintos.
- **Especifica la categoría y los filtros utilizados:** Incluye si filtraste por categoría (ej. *Salud*, *Negocios*) o tipo de búsqueda (Web, Imágenes, YouTube).
- **Asegura la reproducibilidad:** Guarda y adjunta los archivos `.csv` originales como material complementario (*Supplementary Material*).