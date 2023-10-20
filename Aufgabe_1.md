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
SQL View für Geoserver: 
```
SELECT a.fid, a.iso3, a.jahr, a.value, 
	(ROUND(a.value))::TEXT || '%' AS value_text, 
	laender.id::INT, 
	laender.land, laender.wkb_geometry, wgi.wgi
FROM exports_percent_gdp AS a
LEFT JOIN laender ON a.iso3 = laender.iso3
LEFT JOIN wgi ON laender.id = wgi.landid AND a.jahr = wgi.jahr
WHERE laender.wkb_geometry IS NOT NULL
AND a.jahr = %year%
ORDER BY laender.id, wgi.jahr
```

## 3.

Das Dokument ist ein HTML-Code mit diesem Inhalt:
```
<html>
  <head>
    <title>Geoserver GetFeatureInfo output</title>
  </head>
  <style type="text/css">
	table.featureInfo, table.featureInfo td, table.featureInfo th {
		border:1px solid #ddd;
		border-collapse:collapse;
		margin:0;
		padding:0;
		font-size: 90%;
		padding:.2em .1em;
	}
	table.featureInfo th {
	    padding:.2em .2em;
		font-weight:bold;
		background:#eee;
	}
	table.featureInfo td{
		background:#fff;
	}
	table.featureInfo tr.odd td{
		background:#eee;
	}
	table.featureInfo caption{
		text-align:left;
		font-size:100%;
		font-weight:bold;
		padding:.2em .2em;
	}
  </style>
  <body>
  
<table class="featureInfo">
  <caption class="featureInfo">exports_percent_gdp</caption>
  <tr>
  <th>fid</th>
    <th >fid</th>
    <th >iso3</th>
    <th >jahr</th>
    <th >value</th>
    <th >value_text</th>
    <th >land</th>
    <th >wgi</th>
  </tr>

    <tr>

  <td>exports_percent_gdp.43</td>    
      <td>5297</td>
      <td>DEU</td>
      <td>2019</td>
      <td>46.89291</td>
      <td>47%</td>
      <td>Deutschland</td>
      <td>1.45686573783557</td>
  </tr>
</table>
<br/>

  </body>
</html>
```

## 4.

``` SELECT ST_AsText(ST_Envelope(ST_Transform(ST_SetSRID(wkb_geometry,4326), 3857))) FROM laender WHERE iso3='DEU'; ```

## 5.

Koordinaten der Bounding Box:

652888.8293406211 5987030.898794127

652888.8293406211 7372844.607748471

1673556.9874144716 7372844.607748471

652888.8293406211 5987030.898794127

## 6.

URL: http://localhost:8080/geoserver/gdd/wms?SERVICE=WMS&VERSION=1.1.1&REQUEST=GetFeatureInfo&FORMAT=image%2Fpng&TRANSPARENT=true&QUERY_LAYERS=gdd%3Aexports_percent_gdp&STYLES&LAYERS=gdd%3Aexports_percent_gdp&exceptions=application%2Fvnd.ogc.se_inimage&INFO_FORMAT=text%2Fhtml&FEATURE_COUNT=50&X=50&Y=50&SRS=EPSG%3A3857&WIDTH=101&HEIGHT=101&BBOX=652888.8293406211%2C5987030.898794127%2C1673556.9874144716%2C7372844.607748471

## 7. 

### 7a)

Um den WMS bei QGIS einzuladen muss man unter Arbeitsbereiche, den gdd Arbeitsbereich bearbeiten und ein Häkchen bei "WMS" unter Services setzen.

![image](https://github.com/caaarlito/Geodatendienste/assets/134683878/be4c0ece-754b-4e11-a757-8894e6a1d7d2)

Auf der Geoserver Startseite kann man nun unter "Geo Server Web Map Service" mit einem Klick auf 1.3.0 einen Link für den WMS kopieren, der dann bei QGIS eingetragen werden kann.

![image](https://github.com/caaarlito/Geodatendienste/assets/134683878/260671e0-15ca-4de1-8307-493809f68c35)

Bei QGIS kann dann der WMS Service hinzugefügt werden.

![image](https://github.com/caaarlito/Geodatendienste/assets/134683878/2934e7ba-efbb-403e-9a2a-c2298a10b787)

BEim Einladen des Layers muss nun noch beachtet werden, dass das CRS auf EPSG: 3857 gestellt wird.

### 7b)

![image](https://github.com/caaarlito/Geodatendienste/assets/134683878/8d71f0e4-b38f-4f8b-9247-415aaa47f119)

## 8. 



