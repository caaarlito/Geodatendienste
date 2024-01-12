# Geodatendienste WiSe 2023/24
## Aufgabe 3: WFS
*Abgabe von Luca Emden-Weinert und Carlo Persicke*

### 1. 

```
	<div id="info" class="info">
		<div id="legend" class="legend">
			<p>Legende</p>
			<img src="http://localhost:8080/geoserver/wms?REQUEST=GetLegendGraphic&VERSION=1.1.1&FORMAT=image/png&LAYER=gdd:exports_percent_gdp">
		</div>
		<p/>
		<div id="gfi" class="gfi"></div>
	</div>
```

### 2. 

GetFeature URL:

http://localhost:8080/geoserver/wfs?service=wfs&request=GetFeature&version=1.1.0&typeNames=gdd:exports_percent_gdp&featureID=43&outputFormat=json

Ergebnis:

![image](https://github.com/caaarlito/Geodatendienste/assets/134683878/564c68db-bfd5-4371-9c0b-120800042b95)


### 3. 

```
var vectorLayer = new ol.layer.Vector({
  source: new ol.source.Vector({
    format: new ol.format.GeoJSON(), // GeoJSON-Format verwenden
    url: function (extent) {
      return 'http://localhost:8080/geoserver/wfs?service=wfs&request=GetFeature&version=1.1.0&typeNames=gdd:exports_percent_gdp&outputFormat=json';
    },
  }),
  style: new ol.style.Style({
    Fill: new ol.style.Fill({
      color: 'rgba(0, 0, 255, 0)' // Blaue Füllung ohne Transparenz
    }),
  })
});

var map = new ol.Map({
  target: 'map',
  layers: [
    wmsLayer,
	vectorLayer
  ],
  view: view 
});
```

### 4.

```
map.on('pointermove', function (evt) {
  var coordinate = evt.coordinate;
  var feature = vectorLayer.getSource().getClosestFeatureToCoordinate(coordinate);
  var geometry = feature.getGeometry().clone();
  var land = feature.get('land');
  
  var sourceProj = map.getView().getProjection();
  geometry.transform(sourceProj, 'EPSG:4326');
  
  // Koordinaten der Bounding Box
  var extent = geometry.getExtent();
  var east = extent[0].toFixed(2);
  var south = extent[1].toFixed(2);
  var west = extent[2].toFixed(2);
  var north = extent[3].toFixed(2);

  var gfiDiv = document.getElementById('gfi');
  gfiDiv.innerHTML = '<table>' +
	'<tr><th>Land</th><th>' + land + '</th></tr>' +
	'<tr><td>Ostgrenze:</td><td>' + east + '</td></tr>' +
	'<tr><td>Südgrenze:</td><td>' + south + '</td></tr>' +
	'<tr><td>Westgrenze:</td><td>' + west + '</td></tr>' +
	'<tr><td>Nordgrenze:</td><td>' + north + '</td></tr>' +
	'</table>';
});
```

### 5.

```
table {
    display: inline-table; /* Nur so breit und hoch wie der enthaltene Text */
	border: 1px solid black;
}

/* Styling der Überschriftenzellen */
th {
    text-align: left;
    padding: 10px;
    background-color: #cceeff;
}

/* Styling der normalen Zellen */
td {
    padding: 10px;
}

/* Styling für gerade Zeilen */
tr:nth-child(even) {
    background-color: #f2f2f2;
}

/* Hervorheben der Zeile bei Hover */
tr:hover {
    background-color: #ffffcc;
}
```

### 6.

Webapplikation:

![image](https://github.com/caaarlito/Geodatendienste/assets/134683878/54f07a85-53da-41b1-8e03-5d6dc9f23866)

Farbänderung beim hovern über die Tabelle: 

![image](https://github.com/caaarlito/Geodatendienste/assets/134683878/61565be0-30e0-4ad4-9121-79784e913289)

### 7.

#### 7a) 

```
d:
cd D:\Programme\PostgreSQL\bin

set PGPASSWORD=gdd

psql -h localhost -p 5432 -U gdd -d gdd -c "CREATE TABLE world_cities(id serial primary key, city varchar, lat float, long float, country varchar, iso2 char(2), iso3 char(3), capital varchar, population int);"
psql -h localhost -p 5432 -U gdd -d gdd -c "\COPY world_cities(city, lat, long, country, iso2, iso3, capital, population) FROM 'D:\Dokumente\1_Studium\5_Semester\Geodatendienste\Aufgabe_3\worldcities.csv' DELIMITER ';' CSV HEADER;"
```

#### 7b)

Ein SERIAL ist ein eigenständiger Datentyp, wie man bei der Erstellung der Tabelle sehen kann. Eine SEQUENCE hingegen ist ein einfaches Objekt mit dem integer Nummern generiert werden können.

### 8.

```
<script src="https://code.jquery.com/jquery-3.7.0.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jqueryui/1.13.2/jquery-ui.min.js"></script>
<script src='https://cdnjs.cloudflare.com/ajax/libs/Turf.js/6.5.0/turf.min.js'></script>
<script src="https://api.mapbox.com/mapbox.js/plugins/arc.js/v0.1.0/arc.js"></script>
```

### 9.


#### 9a)

```
var vectorLayerCities = $.getJSON(
	"http://localhost:8080/geoserver/wfs?service=wfs&request=GetFeature&version=1.1.0&typeNames=gdd:world_cities&outputFormat=json",
	function(data) { console.log("Daten erfolgreich geladen: ", data)
})

var vectorLayerCircles = new ol.layer.Vector({
	source: new ol.source.Vector()
});
```

#### 9b)

```
var vectorLayerCities = $.getJSON(
	"http://localhost:8080/geoserver/wfs?service=wfs&request=GetFeature&version=1.1.0&typeNames=gdd:world_cities&outputFormat=json",
	function(data) {
		console.log("Daten erfolgreich geladen: ", data);
		
		var capitals = $.grep( data.features, function(stadt) {
			return stadt.properties.capital == 'primary'});
		
		console.log("gefilterte Daten grep:", capitals);
		
		var capitals2 = data.features.filter(function(stadt) {
			return stadt.properties.capital == 'primary'});
			
		console.log("gefilterte Daten filter:", capitals2);
});

```

### 10.

.js Änderungen:

```
		var stadtForLoop = [];
		for (var i = 0; i < capitals.length; i++) {
		stadtForLoop.push(capitals[i].properties.city)};
		console.log("Stadtnamen for: ", stadtForLoop);
		
		var stadtEach = [];
		$.each(capitals, function(index, feature) {
			stadtEach.push(feature.properties.city)});
		console.log("Stadtnamen each: ", stadtEach);

		$('.autocompleteFeld').autocomplete({
		source: stadtForLoop })	
```

.js Button:

```
$().ready(function(){
  $("button").click(function(){
    var x = $("form").serializeArray();
	$("#result").empty();
    $.each(x, function(i, field){
      $("#result").append(field.name + ":" + field.value + " ");
    });
  });
});
```

.html Änderungen:

```
	<form>
	 Feld 1: <input type="text" class="autocompleteFeld" name="Feld1" value=""><br>
	 Feld 2: <input type="text" class="autocompleteFeld" name="Feld2" value=""><br>
	</form>
	<button type="button">Einlesen</button>
	<div id="result"></div>
```
### 11.

HTML: 

```
<div id="map" class="map"></div>
	<div id="info" class="info">
		<div id="legend" class="legend">
			<p>Legende</p>
			<img src="http://localhost:8080/geoserver/wms?REQUEST=GetLegendGraphic&VERSION=1.1.1&FORMAT=image/png&LAYER=gdd:exports_percent_gdp">
		</div>
		<p/>
		<div id="gfi" class="gfi"></div>
	</div><br>
	<form>
	 Feld 1: <input type="text" class="autocompleteFeld" name="Feld1" value=""><br>
	 Feld 2: <input type="text" class="autocompleteFeld" name="Feld2" value=""><br>
	</form>
	<button type="button">Einlesen</button>
	<div id="result"></div>
```

JS:

```
var capitals; 

var vectorLayerCities = $.getJSON(
	"http://localhost:8080/geoserver/wfs?service=wfs&request=GetFeature&version=1.1.0&typeNames=gdd:world_cities&outputFormat=json",
	function(data) {
		console.log("Daten erfolgreich geladen: ", data);
		
		capitals = $.grep( data.features, function(stadt) {
			return stadt.properties.capital == 'primary'});
		
		console.log("gefilterte Daten:", capitals);
		
		var stadtForLoop = [];
		for (var i = 0; i < capitals.length; i++) {
		stadtForLoop.push(capitals[i].properties.city)};
		console.log("Stadtnamen for: ", stadtForLoop);
		
		var stadtEach = [];
		$.each(capitals, function(index, feature) {
			stadtEach.push(feature.properties.city)});
		console.log("Stadtnamen each: ", stadtEach);
		
		$('.autocompleteFeld').autocomplete({
		source: stadtForLoop })	
});

var vectorLayerCircles = new ol.layer.Vector({
	source: new ol.source.Vector()
});

$().ready(function(){
  $("button").click(function(){
    var x = $("form").serializeArray();
	var cfrom = [];
	var cto =[];
	$("#result").empty();
    $.each(x, function(i, field){
	  var gesuchteStadt = $.grep(capitals, function(capital) {
	  return capital.properties.city == field.value})[0];
	  if (i==0) {
		var cFromCoords = gesuchteStadt.geometry.coordinates;
		var transformedCFrom = ol.proj.transform(cFromCoords, 'EPSG:3857', 'EPSG:4326');
		cfrom.push(transformedCFrom); }
	  else {
		var cToCoords = gesuchteStadt.geometry.coordinates;
		var transformedCTo = ol.proj.transform(cToCoords, 'EPSG:3857', 'EPSG:4326');
		cto.push(transformedCTo); };
	$("#result").append(field.value + " ") 
    });
	console.log(cfrom, cto);
	
	var fromPoint = turf.point(cfrom[0]);
	var toPoint = turf.point(cto[0]);
	var entfernung = turf.distance(fromPoint, toPoint, units="kilometers");
	$("#result").append("Distanz: " + entfernung + " Kilometer");
	
	const arcGenerator = new arc.GreatCircle(
		{ x: cfrom[0][0], y: cfrom[0][1] },
		{ x: cto[0][0], y: cto[0][1] }
          );
	const arcLine = arcGenerator.Arc(100, {offset: 10});

    const features = [];
    arcLine.geometries.forEach(function (geometry) {
        const line = new ol.geom.LineString(geometry.coords);
        line.transform('EPSG:4326', 'EPSG:3857');

        const feature = new ol.Feature({
                geometry: line,
                finished: false,
            });
		features.push(feature);
	});
	
	vectorLayerCircles.getSource().clear();
	
	features.forEach(function(feature) {
    vectorLayerCircles.getSource().addFeature(feature);
	
	const extent = vectorLayerCircles.getSource().getExtent();
	const extentZoom = ol.extent.buffer(extent, 0.2 * (extent[2] - extent[0]));
	map.getView().fit(extentZoom);
    });
  });
});
```

### 12.
tE3JXZ2OVDvpJy7JyYua
