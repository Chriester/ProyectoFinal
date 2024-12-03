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

