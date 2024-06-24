### Proyecto2_hipotesis
## Procesar y preparar base de datos
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
--- Formula para pasar de STRING (Modo texto) A INTEGER (Numero)

```sql
SELECT *,
IF
  (REGEXP_CONTAINS(streams, r'^[0-9]+$'), CAST(streams AS INT64), NULL) AS stream_limpio
FROM
  `proyecto-hipotesis-lab1.dataset.track_in_spotify`
WHERE
  streams NOT LIKE '%[^0-9]%'
```
> Este texto r'^[0-9]+$' indican los caracteres que de texto en STRINGS

La formula CAST me indica "conversión, convertir los datos"

### Paso 6: Identificar y manejar datos discrepantes en variables categóricas
 Identificar y manejar datos discrepantes en variables numéricas



