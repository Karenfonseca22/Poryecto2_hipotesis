### Proyecto2_hipotesis
## 🟦 5.1 Procesar y preparar base de datos
Herramienta principal: BigQuery
### PASO 1: Conectar/importar datos a otras herramientas

Crear tu cuenta en Google BigQuery, en la consola y es muy importante ver que estas trabajando en el SANDBOX, o en español ZONA DE PRUEBAS.
Para importar tus tablas desde el ordenador, mirar este video https://www.youtube.com/watch?v=BN8WAOtJ4CY, desde el minuto 6 se puede observar como crear un conjunto de datos y también sus tablas.

- Para importar las tablas, primero se debe crear un dataset, y nombrarlo. Luego se suben los datos, en este caso las tres tablas estaban en archivos de Excel

### PASO 2: Identificar y manejar valores nulos

Desición: Identificarlos primero, y al ver que estos valores nulos hacen parte del 10% de los datos, fueron removidos de las tablas (simplemente no se mencionan en el SELECT).

**Consulta en SQL para la tabla de track_in_competition**
```sql
-- Esta es una formula en SQL para BigQuery
SELECT
  COUNT(*) AS cantidad_nulos
FROM
  `proyecto-hipotesis-lab1.dataset.track-in-competition`
WHERE
  track_id IS NULL
  OR in_apple_playlists IS NULL
  OR in_apple_charts IS NULL
  OR in_deezer_charts IS NULL
  OR in_shazam_charts IS NULL;
```

**Consulta en SQL para la tabla de track_in_spotify**
```sql
-- Esta es una formula en SQL para BigQuery
SELECT COUNT (*) AS cantidad_nulos 
FROM `proyecto-hipotesis-lab1.dataset.track_in_spotify`
WHERE track_id IS NULL
OR track_name IS NULL
OR artist_s__name IS NULL
OR artist_count IS NULL
OR released_year IS NULL
OR released_month IS NULL
OR released_day IS NULL
OR in_spotify_playlists IS NULL
OR in_spotify_charts IS NULL
OR streams IS NULL
```

**Consulta en SQL para la tabla de track_in_spotify**

--- En esta consulta se retorno el numero de nulos, por columna.
```sql
SELECT
  COUNT(CASE WHEN key IS NULL THEN 1 END) AS nulos_key,
  SUM(CASE WHEN bpm IS NULL THEN 1 ELSE 0 END) AS nulos_bpm,
  SUM(CASE WHEN track_id IS NULL THEN 1 ELSE 0 END) AS nulos_track_id,
  SUM(CASE WHEN MODE IS NULL THEN 1 ELSE 0 END) AS nulos_mode,
  SUM(CASE WHEN `danceability_%` IS NULL THEN 1 ELSE 0 END) AS `nulos_danceability_%`, --si el nombre de la columna contiene caracteres especiales como %, debes rodear el nombre de la columna con comillas invertidas (`)
  SUM(CASE WHEN `valence_%` IS NULL THEN 1 ELSE 0 END) AS `valence_%`,
  SUM(CASE WHEN `energy_%` IS NULL THEN 1 ELSE 0 END) AS `energy_%`,
  SUM(CASE WHEN `acousticness_%` IS NULL THEN 1 ELSE 0 END) AS `acousticness_%`,
  SUM(CASE WHEN `instrumentalness_%` IS NULL THEN 1 ELSE 0 END) AS `instrumentalness_%`,
  SUM(CASE WHEN `liveness_%` IS NULL THEN 1 ELSE 0 END) AS `liveness_%`,
  SUM(CASE WHEN `speechiness_%` IS NULL THEN 1 ELSE 0 END) AS `spechiness_%`,
FROM
  `proyecto-hipotesis-lab1.dataset.track_technical_info`
```

### Paso 3: Identificar y manejar valores duplicados
Identificar duplicados a través de comandos SQL COUNT, GROUP BY, HAVING

--- Se menciona la columna en la que queremos evaluar el duplicado, y le damos el nombre de columna a los reultados, ejemplo AS num_tracks. Luego se agrupan esa columna en la cual se van a contar, si no se agrupa, BigQuery no sabe en donde buscar.

```sql
SELECT 
track_id,
COUNT(*) AS num_tracks
FROM `proyecto-hipotesis-lab1.dataset.track_technical_info`
GROUP BY
track_id
HAVING
num_tracks >1
```

### Paso 4: Identificar y manejar datos fuera del alcance del análisis
Manejar variables que no son útiles para el análisis a través de comandos SQL SELECT EXCEPT

Para este punto se analizo la formula EXCEPT para sacar las variables que tenian el 10% con datos como null
```sql
SELECT *
EXCEPT (mode,`liveness_%`)
FROM `proyecto-hipotesis-lab1.dataset.track_technical_info`
```
### Paso 5: Identificar y manejar datos discrepantes en variables categóricas
-- CONSULTA PARA QUITAR LOS CARACTERES ESPCIALES DE LOS NOMBRES DE LOS ARTISTAS Y LAS CANCIONES ---
SELECT
  track_name,
  artist_s__name,
  REGEXP_REPLACE(track_name,r'[^a-zA-Z0-9]', ' ') AS track_name_limpia,
  REGEXP_REPLACE(artist_s__name,r'[^a-zA-Z0-9]', ' ') AS artist_s__name_limpia,
FROM
  `proyecto-hipotesis-lab1.dataset.track_in_spotify`

> Este texto r'[^a-zA-Z0-9]' indica caracteres especiales


### Paso 6: Identificar y manejar datos discrepantes en variables categóricas
Identificar y manejar datos discrepantes en variables numéricas

Luego de esta limpieza, podemos agregar variables nuevas, datos adicionales que son importantes para concluir y validar hipotesis al final del proyecto, las variables nuevas también sirven para hacer la lectura de los datos mas facil, ejemplo, cual es el dato mayor en estos datos, cual es el promedio de las repdroducciónes.

```sql
SELECT
MAX(stream_limpio) AS maximo,
MIN(stream_limpio) AS minimo,
AVG (stream_limpio) AS promedio,
FROM `proyecto-hipotesis-lab1.dataset.cast_integer_stream`
```
> Para usar MAX, MIN y AVG es fundamental que esten en formato INTEGER, ya que si no lo identifica como numero no podra retornar exitosamente la consulta.

### Paso 7: Comprobar y cambiar tipo de dato
El objetivo, es cambiar el tipo de dato , en STRING, a INTEGER

```sql
SELECT *,
IF
  (REGEXP_CONTAINS(streams, r'^[0-9]+$'), CAST(streams AS INT64), NULL) AS stream_limpio
FROM
  `proyecto-hipotesis-lab1.dataset.track_in_spotify`
WHERE
  streams NOT LIKE '%[^0-9]%'
```
> Este texto r'^[0-9]+$' indican los caracteres que de texto (STRINGS)

La formula CAST me indica "conversión, convertir los datos"

### Paso 8: Crear nuevas variables

En este paso se crea una nueva variable uniendo, año, mes y dia, en una misma, para que quede la fecha de lanzamiento en un mismo formato y en una misma variable.

--- Formula para convertir el dato de fechas en STRINGS y asi poder pasarlo al formato de fecha, de año, mes y dia

```sql
SELECT
  PARSE_DATE('%Y-%m-%d', CONCAT(CAST(released_year AS STRING), '-', CAST(released_month AS STRING), '-', CAST(released_day AS STRING))) AS fecha_lanzamiento
FROM
  `proyecto-hipotesis-lab1.dataset.track_in_spotify`
```

### Paso 8: Unir tablas
Teniendo en cuenta que se han usado los anteriores procesos para la limpieza de las 3 tablas iniciales, es hora de unirlas. Este paso se logro con la formula de JOIN (LEFT JOIN, RIGHT JOIN, INNER JOIN), se decidio usar la fomrula de INNER JOIN, ya que todas se unirán a través del track_id, contando que ninguna de las tres tablas tiene este dato repetido o duplicado.

```sql
SELECT
  T.*,
  S.track_name_limpio,
  S.artist_s__name_limpio,
  S.artist_count,
  S.streams_limpio,
  S.released_year,
  S.released_month,
  S.released_day,
  S.fecha_lanzamiento,
  S.in_spotify_playlists,
  S.in_spotify_charts,
  C.in_apple_charts,
  C.in_apple_playlists,
  C.in_deezer_charts,
  C.in_deezer_playlists,
  C.in_shazam_charts,
  (S.in_spotify_playlists + C.in_deezer_playlists + C.in_apple_playlists) AS total_participation_playlist
FROM
  `proyecto-hipotesis-lab1.dataset.track_in_competition_limpia` AS C
INNER JOIN
  `proyecto-hipotesis-lab1.dataset.track_spotify_limpia` AS S
ON
  C.track_id = S.track_id
INNER JOIN
  `proyecto-hipotesis-lab1.dataset.track_technical_limpia` AS T
ON
  C.track_id = T.track_id
```

### Paso 9: Construir tablas auxiliares
¿Que son las tablas auxiliares? Son tablas en donde podemos hacer una consulta y usando el comando JOIN, se unira a la tabla general o prinicipal, esta no se creará como tabla, sino siendo auxiliar se podrá ver reflejada en la vista/tabla creada y mencionada en la consulta.

```sql
WITH
  total_canciones_solistas AS(
  SELECT
  IF
    (artist_count = 1, artist_s__name_limpio,'NO SOLISTA') AS solista,
    COUNT (*) AS total_canciones
  FROM
    `proyecto-hipotesis-lab1.dataset.union_tablas`
  GROUP BY
    solista)
SELECT
  ut.*,
  IFNULL(tcs.solista, 'No es solista') AS solista,
  IFNULL(tcs.total_canciones,0) AS total_canciones_solista_
FROM
  `proyecto-hipotesis-lab1.dataset.union_tablas` AS ut
LEFT JOIN
  total_canciones_solistas AS tcs
ON
  tcs.solista = ut.artist_s__name_limpio
```

## 🟪 5.2 Hacer un análisis exploratorio
Herramienta principal: PowerBI

### Paso 1: Agrupar datos según variables categóricas
![image](https://github.com/Karenfonseca22/Poryecto2_hipotesis/assets/149629605/6478d35e-6dbc-498b-9ea2-f81466b52736)



