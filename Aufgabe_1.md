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
```
cd c:\Program Files\QGIS\15\bin
ogr2ogr -f postgresql PG:"dbname=gdd user=gdd password=gdd" "C:\Users\geomedien\Downloads\data\countries.shp" -nln "laender" -nlt PROMOTE_TO_MULTI -a_srs "EPSG:4326"
```
