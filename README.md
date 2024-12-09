# ProyectoFinal
Link de descarga del CSV - https://drive.google.com/drive/folders/1FXgSRHyGBuxQpl1SZumY4BMkiF5_t73K?usp=drive_link (120mb~)
# Desglose del Proyecto

## 1. Objetivo Principal:
Realizar un análisis del meta del último parche de League of Legends para ayudar a un equipo de Esports a desarrollar estrategias.

## 2. Ampliaciones Posibles:
- Comparar las tendencias del meta actual con parches anteriores.
- Incorporar análisis de impacto de campeones o roles específicos en la victoria.
- Identificar patrones de juego en diferentes niveles de habilidad o regiones.
- Analizar diferencias entre equipos con alto nivel de coordinación frente a equipos individuales.

## 3. Tareas Desglosadas:

### Etapa 1: Preparación de Datos
- **Scraping y obtención de datos:**
  - Usar la API de Riot para extraer datos de partidas clasificatorias de alto nivel.
  - Limitar los datos al último parche para mantener la relevancia.
  - Descargar datos adicionales para variables exógenas (clima en competencias, horarios de partidas, etc., si es relevante).

- **Limpieza y estructura:**
  - Evaluar calidad de los datos: valores nulos, duplicados, inconsistencias.
  - Normalizar datos relevantes: daño por minuto, oro por minuto, etc.

### Etapa 2: Análisis Exploratorio
- **Crear gráficos y tablas para visualizar:**
  - Frecuencia de campeones, roles y objetos.
  - Relación entre KDA (Kills/Deaths/Assists) y victorias.
  - Eficiencia en objetivos (torretas, dragones, etc.) y resultados de partidas.

- **Realizar segmentaciones:**
  - Por roles (jungla, mid, etc.).
  - Por regiones si los datos están disponibles.

### Etapa 3: Análisis Avanzado
- **Series de tiempo:**
  - Analizar cambios en la popularidad de campeones/estrategias a lo largo del parche.

- **Análisis de correlación:**
  - Evaluar qué métricas tienen mayor correlación con la victoria.

- **Outliers:**
  - Detectar jugadores, campeones o equipos con métricas anómalas (ejemplo: daño altísimo o tasas de victoria extremas).

- **Análisis de Cohortes:**
  - Crear cohortes basadas en tiempo jugado, composición de equipo, o estrategias comunes.

### Etapa 4: Modelado Predictivo
- **Clasificación:**
  - Crear un modelo para predecir si un equipo ganará o no basado en:
    - Selección de campeones.
    - Composición de roles.
    - Estadísticas acumuladas.

- **Regresión:**
  - Modelar el impacto de factores como "oro ganado por minuto" en la probabilidad de victoria.

## 4. Cumplimiento de Requisitos Mínimos

- **Scraping:** La API de Riot proporcionará datos base.
- **EDA:** Gráficos como histogramas, boxplots y diagramas de dispersión ayudarán a cumplir este punto.
- **Análisis de calidad:** Puedes identificar datos faltantes o duplicados en las partidas.
- **Segmentación:** Dividir datos por roles, regiones o niveles de habilidad.
- **Modelos predictivos:** Usar clasificación y regresión regularizada (ej. Ridge o Lasso).
- **Análisis de tiempo:** Detectar cambios en las tendencias del meta a lo largo del parche.
- **Correlaciones:** Examinar qué métricas afectan más a la victoria.
- **Cohortes:** Segmentar por estrategias o jugadores destacados.
- **Gráficos:** Visualizar variables como daño, roles o victorias.
- **Outliers:** Detectar partidas con resultados extremos para análisis adicional.

# Scraping de Datos de League of Legends

Este proyecto utiliza las **APIs públicas de Riot Games** para recopilar datos relacionados con partidas clasificatorias de alto nivel de League of Legends. El scraping está dividido en dos partes principales, diseñadas para extraer información de jugadores Challenger y los detalles de sus partidas.

---

## 1. Extracción de PUUIDs de Jugadores Challenger

El primer paso consiste en obtener una lista de **PUUIDs** (identificadores únicos de usuarios) de los jugadores en la liga **Challenger** de una región específica.

### Proceso
1. **Obtener la lista de jugadores Challenger**:
   - Se consulta la API `challengerleagues/by-queue/RANKED_SOLO_5x5` para obtener información básica sobre los jugadores Challenger.
   - Cada jugador está identificado por su `summonerId`.

2. **Obtener el PUUID**:
   - Usando el `summonerId`, se consulta la API `summoner/v4/summoners/{summonerId}` para obtener su PUUID.
   - Se utiliza un sistema de control de tasa para evitar exceder los límites de la API.

3. **Control del Límite de Tasa**:
   - El script regula las solicitudes para cumplir con las restricciones de Riot Games: 
     - 20 solicitudes por segundo.
     - 100 solicitudes por cada 2 minutos.

### Código Principal
- `get_challenger_players`: Obtiene la lista de jugadores Challenger.
- `get_puuid`: Convierte el `summonerId` en PUUID.
- `rate_limited_request`: Controla la tasa de solicitudes.

### Salida
- Una lista de PUUIDs lista para usarse en el análisis de datos o para extraer datos de partidas.

---

## 2. Extracción de Datos de Partidas

El segundo paso utiliza los **PUUIDs** generados en el primer script para recopilar información detallada de partidas clasificatorias.

### Proceso
1. **Obtener IDs de Partidas**:
   - Para cada PUUID, se consulta la API `match/v5/matches/by-puuid/{puuid}/ids` para obtener una lista de IDs de partidas.
   - Puedes limitar el número de partidas por jugador (por ejemplo, 20).

2. **Obtener Detalles de las Partidas**:
   - Usando los IDs de las partidas, se consulta la API `match/v5/matches/{match_id}` para obtener información detallada.
   - Los datos incluyen estadísticas de los jugadores, resultados de los equipos, tiempos de juego, entre otros.

3. **Control del Límite de Tasa**:
   - Similar al primer script, regula las solicitudes para evitar exceder los límites.

### Código Principal
- `get_matches`: Recupera los IDs de partidas para un PUUID.
- `get_match_details`: Obtiene los detalles completos de una partida específica.
- `extract_match_data`: Itera sobre los PUUIDs y combina los datos de las partidas.

### Salida
- Una lista de datos de partidas en formato JSON, lista para ser convertida a CSV o utilizada directamente para análisis.

---

## Configuración

### Límites de la API de Riot Games
- **Tasa de solicitudes**:
  - 20 solicitudes por segundo.
  - 100 solicitudes por cada 2 minutos.
- **Región**:
  - `euw1` para jugadores.
  - `europe` para datos de partidas (clústeres regionales).

### Requisitos
- Llave de API de Riot Games (`API_KEY`).
- Instalación de bibliotecas necesarias:
  ```bash
  pip install requests


# Análisis de calidad de los datos
## 1. Información del DataFrame
Este DataSet contenía una cantidad gigante de información y columnas, así que hicimos un análisis para seleccionar qué iba a ser útil para nuestros objetivos.
![image](https://github.com/user-attachments/assets/fe952d8c-bbaf-4b17-8377-d1521f06463a)

A destacar:
- Eliminación de columnas que no aportaban valores analizables(ejemplo: iconos de los jugadores)
- Filtro de partidas por el último parche (14.23)
- Comprobación de valores nulos (Gestionado por el scraping, se han asignado valores por defecto en valores faltantes)
- Comprobación de duplicados:
- 
En este DataSet se guardan todas las partidas de los 300 mejores jugadores del servidor de Europa, estos 300 jugadores, por cómo funciona el sistema de emparejamiento del juego, la mayoría de veces se van a enfrentar entre ellos 5 vs 5, con la posibilidad de que haya alguna persona cercana a la liga de Challenger (Grand Master) en alguna de las partidas
Debido a esto, el mismo match_id podría aparecer hasta 10 veces por cada partida, una vez por cada integrante de los dos equipos que se enfrentan. Estos duplicados son útiles a la hora de agrupar por partidas totales así que los mantenemos.
Aquí se puede ver que de los más de 50k registros en el dataframe solo hay esta cantidad de partidas únicas:

![image](https://github.com/user-attachments/assets/f8d7e985-7bbd-49bd-a1d2-2d8473d87d19)

-Duración de las partidas:

Al hacer una distribución de las partidas vimos unos outliers que analizar:
![image](https://github.com/user-attachments/assets/ac840be5-9eda-43a2-a6c0-e3e37d91d2a8)

Aquí se ve claramente que hay un número de partidas anómalamente cortas y largas.
Al analizar el funcionamiento de las partidas, sabemos que a partir del minuto 15 de partida se activa la posibilidad de rendición de los equipos.
Sin embargo, League of Legends tiene la funcionalidad de que si una persona de tu equipo se desconecta en los primeros minutos de partida se activa la posibilidad de rendición prematura.
Por lo tanto, hemos decidido eliminar las partidas con menor duración a 15 minutos, ya que en esas partidas han sucedido cosas externas a el desarrollo normal de la misma.
Para las partidas largas, hemos decidido tomar un valor máximo de duración (45 minutos).
Esto es porque hemos determinado que las partidas con más relevancia para el análisis estarán entre 15 y 45 minutos.

- Cambio de formato de datos
  Las fechas de las partidas estaban en formato UNIX (milisegundos pasados desde una fecha de 1970) así que las convertimos a una fecha legible:
  
 ![image](https://github.com/user-attachments/assets/cc503815-5060-4596-84b6-7e6cce59c86f)

  Cambio del formato de win de 0 y 1 a bool (TODO)

  Cambio de nombre de team_position
  
  ![image](https://github.com/user-attachments/assets/22a09177-e6bb-44bf-ab9b-9d4e0fc11d83)
  
  La posición 'SUPPORT' estaba anotada como 'UTILITY', se normaliza para que coincida con el nombre real de la posición

- Número total de filas y columnas.
- ![image](https://github.com/user-attachments/assets/62e9728f-582f-4e3d-9dda-a495ff50844c)

- Información sobre el DataFrame.  
(![image](https://github.com/user-attachments/assets/0c30f6af-c111-424f-b9a4-d671988085e6)



# Exploratory Data Analysis (EDA) - Análisis del Meta de League of Legends

## Introducción
Este análisis tiene como objetivo proporcionar información clave sobre el meta actual de League of Legends, enfocándose en datos que puedan ser útiles para un coach profesional al tomar decisiones estratégicas. 

A continuación, se detallan las visualizaciones y los insights que se generaron durante la exploración de los datos.

---

## 1. Distribución de Campeones por Rol
Se analizó cómo se distribuyen los campeones según los roles principales del juego (Top, Jungle, Mid, ADC, Support). Para facilitar la interpretación, se dividieron en cuatro segmentos.  

**Gráfica:**  

![download](https://github.com/user-attachments/assets/1347222c-0781-4251-a041-7453ccda3fcc)

![download](https://github.com/user-attachments/assets/a96c2704-6687-4316-a264-42bb6a606561)

![download](https://github.com/user-attachments/assets/c2bc2f19-6518-4cdb-a957-e23861f41589)

![download](https://github.com/user-attachments/assets/2ff2e401-c1a0-4246-8065-484cd07e603d)


**Conclusión:**  
*(Espacio para destacar roles con mayor concentración o diversidad de campeones.)*

---

## 2. Distribución de Campeones por Porcentaje de Victoria
Se exploró el porcentaje de victoria de los campeones en el meta actual, segmentando los resultados en cuatro grupos para identificar campeones consistentes y outliers.  

**Gráfica:**  
![download](https://github.com/user-attachments/assets/34c175e1-8d02-4ab3-a2ae-13a21f211481)

![download](https://github.com/user-attachments/assets/3ff98c89-692f-4b21-8ddc-ac114f32c1f6)

![download](https://github.com/user-attachments/assets/9f760b60-40c2-4fca-b367-aa6ecd61cdf4)

![download](https://github.com/user-attachments/assets/313598f0-8303-487b-ae89-fcb1a7c800cf)






**Conclusión:**  
*(Espacio para resaltar campeones meta-dominantes y aquellos con bajo rendimiento.)*

---

## 3. Top 20 Campeones con Más Porcentaje de Victoria
Se identificaron los campeones más efectivos en términos de porcentaje de victorias.  

**Tabla:**  

![image](https://github.com/user-attachments/assets/9201eae5-123d-4b9e-ae21-3368fcae5ed9)


**Conclusión:**  
*(Espacio para mencionar tendencias o recomendaciones basadas en este ranking.)*

---

## 4. Distribución de la Duración de las Partidas
El análisis de la duración de las partidas puede ayudar a identificar si el meta actual favorece juegos largos o rápidos.  

**Gráfica:**  

![download](https://github.com/user-attachments/assets/7d4b0099-0a00-489a-8f6a-632eac90e819)


**Conclusión:**  
*(Espacio para incluir observaciones como duración promedio y posibles implicaciones para el draft.)*

---

## 5. Top 10 Jugadores con Mejor KDA por Rol
Este análisis identifica a los jugadores más consistentes en términos de KDA, segmentado por roles.  

**Gráfica:**  

![download](https://github.com/user-attachments/assets/61584bbe-c1fe-42b9-ab4e-ed832801c750)


**Conclusión:**  
*(Espacio para destacar patrones o jugadores excepcionales en su rol.)*

## 6. TODO: porcentaje de victorias por objetivos(primer dragon, baron, torre...) 


