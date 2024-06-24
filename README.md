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



