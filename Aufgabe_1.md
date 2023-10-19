# Geodatendienste
## Aufgabe 1: WMS

### 1. 

#### 1a: 

Um das Passwort Problem zu lösen, kann man vor dem Loop eine Umgebungsvariable namens PGPASSWORT festlegen, welches dann automatisch übernommen wird.
``` set PGPASSWORD=postgres ```

#### 1b:
```
c:
cd c:\Program Files\PostgreSQL\15\bin

set PGPASSWORD=postgres

FOR %%G IN (gdd rmdg) DO (
psql -h localhost -p 5432 -U postgres -c "DROP DATABASE IF EXISTS %%G;"
psql -h localhost -p 5432 -U postgres -c "CREATE DATABASE %%G WITH ENCODING 'UTF8'"
psql -h localhost -p 5432 -U postgres -d %%G -c "CREATE EXTENSION postgis;"
psql -h localhost -p 5432 -U postgres -d %%G -c "DROP ROLE IF EXISTS %%G;"
psql -h localhost -p 5432 -U postgres -d %%G -c "CREATE ROLE %%G SUPERUSER CREATEDB CREATEROLE LOGIN PASSWORD '%%G';" 
)

pause
```

#### 1c: 
```
1)	cd c:\Program Files\PostgreSQL\15\bin
	psql -h localhost -p 5432 -U postgres
2)	\l
3)	DROP DATABASE rmdg;
4)	\c gdd
5)	\dt
```

#### 1d: 
Die ogr2ogr.exe im PostgreSQL-Ordner hat das Format "postgresql" nicht ünterstützt weshalb die im QGIS-Ordner alternativ benutzt wurde
```
cd c:\Program Files\QGIS\bin
ogr2ogr -f postgresql PG:"dbname=gdd user=gdd password=gdd" "C:\Users\geomedien\Downloads\data\countries.shp" -nln "laender" -nlt PROMOTE_TO_MULTI -a_srs "EPSG:4326"

pause
```

### 1e:

Bei der Erstellung der Tabelle kann im CREATE Statement hinter dem gewünschten Primary Key dieser VOR dem Einladen der Daten festgelegt werden:
```
CREATE TABLE wgi(landid int PRIMARY KEY, jahr int PRIMARY KEY, wgi float);
```

Oder auch: 
```
CREATE TABLE wgi(landid int, jahr int, wgi float, PRIMARY KEY(landid, jahr));
```

Um einen Primary Key NACH dem Erstellen einer Tabelle festzulegen kann der ALTER Befehl verwendet werden:
```
ALTER TABLE wgi ADD PRIMARY KEY (landid, jahr);
```

Oder die zweite Möglichkeit:
```
ALTER TABLE wgi ADD CONSTRAINT pk_landid PRIMARY KEY (landid, jahr);
```

### 1f:
```
psql -h localhost -p 5432 -U gdd -d gdd -c "CREATE TABLE wgi(landid int, jahr int, wgi float, PRIMARY KEY(landid, jahr));"
psql -h localhost -p 5432 -U gdd -d gdd -c "\COPY wgi(landid, jahr, wgi) FROM 'C:\Users\geomedien\Downloads\data\wgi.csv' DELIMITER ';' CSV HEADER;"

psql -h localhost -p 5432 -U gdd -d gdd -c "CREATE TABLE exports_percent_gdp(fid int, iso3 char(3), jahr int, value float, unit_id int, PRIMARY KEY(fid, iso3, jahr, unit_id));"
psql -h localhost -p 5432 -U gdd -d gdd -c "\COPY exports_percent_gdp(fid, iso3, jahr, value, unit_id) FROM 'C:\Users\geomedien\Downloads\data\exports_percent_gdp.csv' DELIMITER ';' CSV HEADER;"
```


## 2 -> fehlt
```
SELECT a.value, a.iso3, a.jahr, a.value, b.wkb_geometry AS geom 
FROM exports_percent_gdp AS a, laender AS b, wgi AS c 
WHERE a.iso3 = b.iso3 and b.id = c.landid 
AND a.jahr = c.jahr AND b.wkb_geometry IS NOT null

AND a.jahr = (SELECT DISTINCT ON (landid) jahr FROM wgi ORDER BY landid, jahr DESC);
```

```
SELECT
    wgi.landid,
    MAX(wgi.jahr) AS aktuellstes_jahr,
    (
        SELECT wgi
        FROM wgi AS inner_wgi
        WHERE inner_wgi.landid = wgi.landid
        AND inner_wgi.jahr = MAX(wgi.jahr)
    ) AS aktuellstes_wgi
FROM wgi
GROUP BY landid
ORDER BY landid, aktuellstes_jahr;
```
Eventuell Richtig? -> 187 Zeilen
```
SELECT a.fid, a.iso3, a.jahr, a.value, 
	(ROUND(a.value))::TEXT || '%' AS value_text, 
	laender.id::INT, 
	laender.land, laender.wkb_geometry, wgi.wgi
FROM exports_percent_gdp AS a
LEFT JOIN laender ON a.iso3 = laender.iso3
LEFT JOIN wgi ON laender.id = wgi.landid AND a.jahr = wgi.jahr
WHERE laender.wkb_geometry IS NOT NULL
AND (laender.id, wgi.jahr) IN (
    SELECT laender.id, MAX(wgi.jahr)
    FROM exports_percent_gdp AS b
    LEFT JOIN laender ON b.iso3 = laender.iso3
    LEFT JOIN wgi ON laender.id = wgi.landid AND b.jahr = wgi.jahr
    WHERE laender.wkb_geometry IS NOT NULL
    AND landid IS NOT NULL
    GROUP BY laender.id)
ORDER BY laender.id, wgi.jahr
```
