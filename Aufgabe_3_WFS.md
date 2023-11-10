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
