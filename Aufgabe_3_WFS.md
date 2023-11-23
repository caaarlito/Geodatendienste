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

## 4.
```
map.on('pointermove', function(browserEvent) {
	// the mousemove event sends a browser event object that contains
	// the geographic coordinate the event happened at
	var coordinate = browserEvent.coordinate;
	// we can get the closest feature from the source
	var feature = vectorLayer.getSource().getClosestFeatureToCoordinate(coordinate);
	// to compute the area of a feature, we need to get it's geometry and do
	// something a little different depeneding on the geometry type.
	
	var geometry = feature.getGeometry().clone();
	var sourceProj = map.getView().getProjection();
	var targetProj = 'EPSG:4326'; // EPSG:4326 für Grad

	// Transformation der Geometrie in Grad (EPSG:4326)
	geometry.transform(sourceProj, targetProj);

	// Extrahieren des Landnamens
	var land = feature.get('land'); // Annahme: 'land' ist die Eigenschaft für das Land

	// Extrahieren der Koordinaten der Bounding Box
	var extent = geometry.getExtent();
	var east = extent[0].toFixed(2);
	var south = extent[1].toFixed(2);
	var west = extent[2].toFixed(2);
	var north = extent[3].toFixed(2);

	// Rendern der HTML-Tabelle im gfi-DIV
	var gfiDiv = document.getElementById('gfi');
	gfiDiv.innerHTML = '<table>' +
		'<tr><th>Land</th><th>' + land + '</th></tr>' +
		'<tr><td>Ostgrenze</td><td>' + east + '</td></tr>' +
		'<tr><td>Südgrenze</td><td>' + south + '</td></tr>' +
		'<tr><td>Westgrenze</td><td>' + west + '</td></tr>' +
		'<tr><td>Nordgrenze</td><td>' + north + '</td></tr>' +
		'</table>';
});
```
