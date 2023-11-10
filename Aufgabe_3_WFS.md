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

### 3. 
