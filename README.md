### Proyecto2_hipotesis
# Procesar y preparar base de datos
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


### PASO 3




