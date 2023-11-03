# Geodatendienste WiSe 2023/24
## Aufgabe 2: Open Layers
*Abgabe von Luca Emden-Weinert und Carlo Persicke*

### 1. 

**wms.html:**

```
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/openlayers/openlayers.github.io@master/en/v6.15.1/css/ol.css" type="text/css">
    <link rel="stylesheet" href="wms.css">
    <script src="https://cdn.jsdelivr.net/gh/openlayers/openlayers.github.io@master/en/v6.15.1/build/ol.js"></script>
    <title>My first viewer</title>
  </head>
  <body>
    <h2>Geodatendienste</h2>
    <div id="map" class="map"></div>
    <script type="text/javascript" src="wms.js"></script>
  </body>
</html>
```

**wms.css:**

```
.map {
  height: 400px;
  width:100%;
}
```

**wms.js:**

```
var map = new ol.Map({
  target: 'map',
  layers: [
    new ol.layer.Tile({
      source: new ol.source.OSM()
    })
  ],
  view: new ol.View({
    center: ol.proj.fromLonLat([13, 52]),
    zoom: 4
  })
});
```

### 2.

#### 2a)

Die `box-sizing`-Eigenschaft `border-box` ermöglicht es, dass Padding und Rahmen in die Breite des Elements mit einbezogen werden.

#### 2b)

**Änderung in wms.html:**

```
	<body>
		<div id="heading" class="heading">
			<span class="title">Geodatendienste</span>
			<span class="student">powered by: <a href="wms.html">Luca und Carlo</a></span>
		</div>
		<p/>
		<div id="map" class="map"></div>
		<script src="wms.js" type="text/javascript"></script>
	</body>
```

**Änderung in wms.css:**

```
.heading {
	box-sizing: border-box; /* Inkludiert Padding und Rahmen in die Gesamtbreite */
	width: 100%; /* Breite */
	font-family: Arial; /* Schriftart */
	text-align: center; /* Textausrichtung */
	padding: 20px 0px 20px 0px; /* Padding oben, rechts, unten und links */
	background-color: #FEB24C; /* Hintergrundfarbe */
	border: 3px solid #F03B20; /* Rahmen */
}

.title {
	color: white; /* Farbe */
	font-weight: bold; /* Fontgewicht */
	font-size: 250%; /* Fontgröße */
	padding-right: 50px; /* Abstand zum rechten Text */
}

.student {
	color: black; /* Farbe */
	padding-left: 50px; /* Padding */
	font-size: 100%; /* Fontgröße */
}
```

#### 2c)

```
/* Definiert die Animation mit dem Namen "myAnimation" */
@keyframes myAnimation {
  0% {
    background-color: red; /* Hintergrundfarbe zu Beginn der Animation */
    text-shadow: 1px 1px 1px darkgrey; /* Textschatten zu Beginn der Animation */
  }
  25% {
    background-color: orange; /* Hintergrundfarbe bei 25% der Animation */
    transform: scaleX(-1); /* Horizontale Spiegelung bei 25% der Animation */
  }
  50% {
    background-color: yellow; /* Hintergrundfarbe bei 50% der Animation */
    text-shadow: 5px 5px 5px black; /* Textschatten bei 50% der Animation */
  }
  100% {
    background-color: red; /* Hintergrundfarbe am Ende der Animation */
  }
}

.heading {
  /* Vorherige Eigenschaften beibehalten */
  animation: myAnimation 4s ease-in-out 2; /* Fügt die Animation hinzu */
  /* Erklärung der animation-Eigenschaft: */
  /* myAnimation = Name der Animation */
  /* 4s = Dauer der Animation */
  /* ease-in-out = Beschleunigungsart der Animation */
  /* 2 = Anzahl der Wiederholungen der Animation */
}
```


#### 2d)
### Erweiterung wms.html
```
<span class="student" onmouseover="hover()" onmouseout="unhover()">powered by: <a href="wms.html">Luca und Carlo</a></span>
```
### Erweiterung wms.js
```
// Neue Funktionen für das Hover-Event
function hover() {
	// Zugriff auf das Element mit der Klasse "student"
	var element = document.querySelector('.student');

	// Änderung der Schriftgröße und des Schriftgewichts
	element.style.fontSize = '200%'; // Verdoppelt die Schriftgröße
	element.style.fontWeight = 'bold'; // Setzt den Schriftstil auf "bold"
}

function unhover() {
	// Zugriff auf das Element mit der Klasse "student"
	var element = document.querySelector('.student');

	// Zurücksetzen der Schriftgröße und des Schriftgewichts
	element.style.fontSize = '100%'; // Setzt die Schriftgröße zurück
	element.style.fontWeight = 'normal'; // Setzt den Schriftstil zurück auf "normal"
}
```


### 3. 

```
var map = new ol.Map({
        target: 'map',
        layers: [
		  new ol.layer.Tile({
			title: 'WMS Layer',
			source: new ol.source.TileWMS({
			url: 'http://localhost:8080/geoserver/gdd/wms', // URL des WMS
			params: {
			'LAYERS': 'gdd:exports_percent_gdp', // Layer Name
			'TILED': true,
			'Version': '1.1.1'
			},
			serverType: 'geoserver'
			})
			}),
        ],
        view: new ol.View({
          center: ol.proj.fromLonLat([13, 52]),
          zoom: 4
        })
      });
	  
```

### 4. 

Ergänzung der wms.html unter div Map:

```
<div id="legend" class="legend">
  <p>Legende</p>
  <img src="http://localhost:8080/geoserver/wms?REQUEST=GetLegendGraphic&VERSION=1.1.1&FORMAT=image/png&LAYER=gdd:exports_percent_gdp" alt="Kartenlegende">
</div>
```

### 5.

Damit die Legende rechts neben der Karte auftauchen kann, bekommt die `Display` Eigenschaft der Map den Wert `inline-block`.

### 6.
```
.map {
  height: 400px;
  width: 85%;
  box-sizing: border-box;
  display: inline-block; /* Fügt die inline-block Eigenschaft hinzu */
}

.legend {
  height: 100%;
  width: 12%;
  float: right; /* Lässt die Legende rechts schweben */
  padding-right: 30px; /* Rechtes Padding */
}

p {
  font-family: Arial;
  font-weight: bold;
}
```

### 7.

```
code
```

### 8. 

´´´
code
´´´
