# ProyectoFinal
Link de descarga del CSV - https://drive.google.com/drive/folders/1FXgSRHyGBuxQpl1SZumY4BMkiF5_t73K?usp=drive_link (120mb~)
# Desglose del Proyecto

## 1. Objetivos Principales:

- Realizar un análisis del meta del último parche de League of Legends para ayudar a un equipo de Esports a desarrollar estrategias y fichar nuevas incorporaciones a su equipo.
  
- Ajustar modelos capaces de predecir si una partida de league of legends acabará en victoria o derrota para dar una herramienta al equipo que pueda analizar los resultados de las partidas acabadas o en curso para un mejor análisis de rendimiento

## 2. Ampliaciones Posibles:
- Comparar las tendencias del meta actual con parches anteriores.
- Identificar patrones de juego en diferentes niveles de habilidad o regiones.
- Analizar diferencias entre equipos con alto nivel de coordinación frente a equipos individuales.

## 3. Tareas Desglosadas:

### Etapa 1: Preparación de Datos
- **Scraping y obtención de datos:**
  - Usar la API de Riot para extraer datos de partidas clasificatorias de alto nivel.
  - Limitar los datos al último parche para mantener la relevancia.

- **Limpieza y estructura:**
  - Evaluar calidad de los datos: valores nulos, duplicados, inconsistencias.

### Etapa 2: Análisis Exploratorio
- **Crear gráficos y tablas para visualizar:**
  - Frecuencia de campeones y roles.
  - Estudio del KDA.
  - Eficiencia en objetivos (torretas, dragones, etc.) y resultados de partidas.

- **Realizar segmentaciones:**
  - Por roles (jungla, mid, etc.).
  - Por duración de partidas
  - Por KDA

### Etapa 3: Análisis Avanzado
- **Series de tiempo:**
  - Analizar cambios en la popularidad de campeones a lo largo del parche.
  - Analizar comportamiento a lo largo del parche de los jugadores con más rendimiento

- **Análisis de correlación:**
  - Evaluar qué métricas tienen mayor correlación con la victoria.

- **Análisis de Cohortes:**
  - Crear cohortes de comportamiento de los jugadores con más rendimiento
      -Tasas de retención/abandono después de la primera derrota
      -Estudio de la elasticidad de los jugadores para ver los jugadores más consistentes después de la primera derrota

### Etapa 4: Modelado Predictivo

  - Crear un modelo para predecir si un equipo ganará o no basado en:
    - Desempeño del jugador
    - Duración de la partida
    - Primeros objetivos conseguidos


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

![Imagen de WhatsApp 2024-12-14 a las 11 23 12_78acdca8](https://github.com/user-attachments/assets/412bc1cd-f53c-4401-91d1-662b0f34e0cd)


A destacar:
- Eliminación de columnas que no aportaban valores analizables(ejemplo: iconos de los jugadores)
- Filtro de partidas por el último parche (14.23)
- Filtro de partidas solo CLASSIC

![image](https://github.com/user-attachments/assets/232900d2-b0a2-4472-817e-989d59a5b4ce)


- Comprobación de valores nulos (Gestionado por el scraping, se han asignado valores por defecto en valores faltantes)
- Comprobación de duplicados:
  
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
  Las fechas de las partidas estaban en formato UNIX (milisegundos pasados desde 1970) así que las convertimos a una fecha legible:
  
 ![image](https://github.com/user-attachments/assets/cc503815-5060-4596-84b6-7e6cce59c86f)

  Cambio del formato de win de 0 y 1 a bool

  ![image](https://github.com/user-attachments/assets/67cfe57a-04ab-4c1e-9834-fc6a53f2fe63)


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

**Gráficas:**  

![image](https://github.com/user-attachments/assets/8816a8a7-f3a7-4bc2-bc5b-53b92cb18c12)


![image](https://github.com/user-attachments/assets/70a6d811-11a4-4d3f-9619-82daf312517a)


![image](https://github.com/user-attachments/assets/316e160f-bf87-4824-ab57-4050981d1f8e)


![image](https://github.com/user-attachments/assets/7af0f368-b552-411f-97d8-0c407ed1fdfc)



**Conclusión:**  
Se ve claramente que hay predominancia de ciertos campeones frente a otros en ciertos roles, por ejemplo, Nautilus y Lulu en SUPPORT
---

## 2. Distribución de Campeones por Porcentaje de Victoria
Se exploró el porcentaje de victoria de los campeones en el meta actual, segmentando los resultados en cuatro grupos para identificar campeones consistentes y outliers.  

Las victorias están distribuidas en un 50% victorias y derrotas dada la naturaleza del juego ( 5 vs 5)

![image](https://github.com/user-attachments/assets/18975ec1-444b-41cc-9bf6-2c1941dea0b0)


**Gráficas:**  

![image](https://github.com/user-attachments/assets/5d5ecab4-caaa-47e4-bc70-632a557ffc2d)


![image](https://github.com/user-attachments/assets/a6adb82e-3ba6-4e10-a2c7-72acbb412d63)


![image](https://github.com/user-attachments/assets/e047be2e-dc85-4a5f-91f6-6164eee2e963)


![image](https://github.com/user-attachments/assets/bc74ba10-9acc-4955-b3f5-601ca4aff6f9)



**Conclusión:**  
Hay ciertos campeones que pese a tener una cantidad mayor de partidas jugadas su porcentaje de victorias es muy bajo(por ejemplo Lux, Caitlyn o Galio)
y otros que pasa lo contrario ( Warwick o Briar)

---

## 3. Top 20 Campeones con Más Porcentaje de Victoria con un número mínimo de partidas jugadas
Se identificaron los campeones más efectivos en términos de porcentaje de victorias con un mínimo de 150 partidas jugadas.  

**Tabla:**  

![image](https://github.com/user-attachments/assets/4beeafba-5554-49bc-a32f-f25f9016ccad)



**Conclusión:**  
Estos 20 personajes parecen ser los más favorecidos por el metajuego actual

---

## 4. Distribución de diferentes métricas de las partidas
Hemos seleccionado diferentes métricas dentro de las partidas para ver cuáles influyen más en las victorias.

**Gráficas:**  
Oro ganado:

![image](https://github.com/user-attachments/assets/38cb1f52-9399-46cd-94fc-07f91fc2f0a7)

![image](https://github.com/user-attachments/assets/7b5db672-2305-47c6-b8a8-1da0e96e4286)


Al conseguir más oro, los jugadores obtienen más poder, lo que facilita la victoria.
Sin embargo, la cantidad de oro ganado está muy determinada por la duración de la partida, ya que en partidas más largas se va a ganar más dinero que en las cortas, eso hace que la distribución de oro ganado sea similar a la de la duración de las partidas.

Duración de partidas:

![image](https://github.com/user-attachments/assets/bdc3e0cf-f216-49b8-9e32-dfa914eec7bd)


![image](https://github.com/user-attachments/assets/1f05e460-3415-4df5-b1ce-8506bd9fdd00)

La mayor cantidad de victorias están tienen una duración de entre 23 y 31 minutos, y hay una cantidad muy alta de partidas de 15 minutos, lo que nos ha dado la idea de estudiar las partidas cortas y largas por separado para analizar el metajuego.

KDA:

El **KDA** (Kill/Death/Assist) es una métrica comúnmente utilizada en juegos como *League of Legends* para medir el rendimiento de un jugador. Se calcula de la siguiente manera:


KDA = (Asesinatos + Asistencias) / Muertes

- **Asesinatos**: Número de enemigos eliminados por el jugador.
- **Asistencias**: Número de eliminaciones en las que el jugador participó directamente.
- **Muertes**: Número de veces que el jugador fue eliminado por el enemigo.

El KDA es útil para evaluar la contribución general de un jugador al equipo, ya que combina tanto el impacto ofensivo como defensivo.

Añadimos una columna de KDA para cada jugador para analizarlo más adelante:

![image](https://github.com/user-attachments/assets/cfa03a9f-0160-45a0-9b8b-1237c9a5116b)

Sacamos una gráfica con los 10 jugadores con más KDA promedio:

![image](https://github.com/user-attachments/assets/934e978f-9a28-4404-9576-798f44c0d011)

Y también una gráfica con los 10 jugadores con mejor KDA segmentados por el rol dentro de una partida:

![image](https://github.com/user-attachments/assets/bcaa5a23-ef98-4e65-a243-fa9176b14587)

Si quisieramos fichar a jugadores únicamente por su desempeño individual, aquí tendríamos candidatos para cada rol.

---

## 5. Estudio de los objetivos de las partidas
Hemos hecho un estudio de los primeros objetivos más relevantes de la partida para ver su importancia en las victorias.
 Primer Barón Nashor:

![image](https://github.com/user-attachments/assets/88a36aed-03fb-4d78-aed9-b28904f2b93e)

Primer Dragón:

![image](https://github.com/user-attachments/assets/1839d7ed-f885-4a46-bb97-82e5ef7a7563)

Primer Inhibidor:

![image](https://github.com/user-attachments/assets/f939a3ee-77ef-420e-a499-280e3f87c534)

Vemos que los tres objetivos tienen peso, siendo el inhibidor el que más.




