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
// Erstelle den Vektorlayer
var vectorLayer = new ol.layer.Vector({
  source: new ol.source.Vector({
    format: new ol.format.GeoJSON(), // GeoJSON-Format verwenden
    url: function (extent) {
      // Ersetze die URL mit der gewünschten GetFeature-Abfrage
      return 'http://localhost:8080/geoserver/wfs?service=wfs&request=GetFeature&version=1.1.0&typeNames=gdd:exports_percent_gdp&outputFormat=json';
    },
    strategy: ol.loadingstrategy.bbox
  }),
  style: new ol.style.Style({
    fill: new ol.style.Fill({
      color: 'rgba(0, 0, 255, 1)' // Blaue Füllung ohne Transparenz
    }),
    stroke: new ol.style.Stroke({
      color: 'rgba(255, 0, 0, 1)', // Rote Umrandung
      width: 2
    })
  })
});

// Füge den Vektorlayer zur Karte hinzu
map.addLayer(vectorLayer);
```
