---
layout: tutorial
title: Interactive Choropleth Map
css: "#map {
		height: 420px;
	}

	.info {
		padding: 6px 8px;
		font: 14px/18px Arial, Helvetica, sans-serif;
		background: white;
		background: rgba(255,255,255,0.8);
		box-shadow: 0 0 15px rgba(0,0,0,0.2);
		border-radius: 5px;
	}
	.info h4 {
		margin: 0 0 5px;
		color: #777;
	}

	.legend {
		text-align: left;
		line-height: 18px;
		color: #555;
	}
	.legend i {
		width: 18px;
		height: 18px;
		float: left;
		margin-right: 8px;
		opacity: 0.7;
	}"
---

## 可交互的分级统计地图

这个教程讲创建一个色彩丰富可交互的[分级统计地图](http://en.wikipedia.org/wiki/Choropleth_map)，数据使用美国人口密度图（GeoJSON格式），并在网页中使用了一些[自定义的地图控件](../reference.html#icontrol)（这些地图对一些新闻和政府网站是非常有帮助的）。

这个教程的灵感来自于[Texas Tribune US Senate Runoff Results map](http://www.texastribune.org/library/data/us-senate-runoff-results-map/) （也是Leaflet应用），由[Ryan Murphy](http://www.texastribune.org/about/staff/ryan-murphy/)创建。

<div id="map" class="map"></div>

[单独打开页面查看这个例子 &rarr;](choropleth-example.html)

### 数据源

我们将创建一个美国人口密度分布图。数据量并不是很大，用来存储和显示它们最方便和简单方式就是GeoJSON。

GeoJSON数据([us-states.js](us-states.js))中的每个特征看起来与下面类似：

	{
		"type": "Feature",
		"properties": {
			"name": "Alabama",
			"density": 94.65
		},
		"geometry": ...
		...
	}

各州的GeoJSON数据是由[D3](http://d3js.org/)的[Mike Bostock](http://bost.ocks.org/mike)所分享。从[这篇Wikipedia文章](http://en.wikipedia.org/wiki/List_of_U.S._states_by_population_density)扩展了人口密度值，通过将[US Census Bureau](http://www.census.gov/)2011年7月1日的数据赋予了statesData变量。

### 基本的州地图

让我们通过自定义的Mapbox样式在地图上显示各种的数据，底图采用灰色的瓦片地图，这样非常适合作为一个可视化的背景图：

	var mapboxAccessToken = {your access token here};
	var map = L.map('map').setView([37.8, -96], 4);

	L.tileLayer('https://api.tiles.mapbox.com/v4/{id}/{z}/{x}/{y}.png?access_token=' + mapboxAccessToken, {
		id: 'mapbox.light',
		attribution: ...
	}).addTo(map);

	L.geoJson(statesData).addTo(map);

<div id="map2" style="height: 300px"></div>


### 增加一些颜色

现在我们需要根据各个州的人口密度来添加颜色。在地图上选择一系列好看的颜色是需要技巧的，但是这里有个很棒的工具帮助我们选择颜色－－[ColorBrewer](http://colorbrewer2.org/)。我们使用它得到颜色值，并创建一个根据人口密度返回颜色的函数：

	function getColor(d) {
		return d > 1000 ? '#800026' :
		       d > 500  ? '#BD0026' :
		       d > 200  ? '#E31A1C' :
		       d > 100  ? '#FC4E2A' :
		       d > 50   ? '#FD8D3C' :
		       d > 20   ? '#FEB24C' :
		       d > 10   ? '#FED976' :
		                  '#FFEDA0';
	}

下一步我们为GeoJSON图层定义一个样式函数，所以它的`fillColor`依赖于`feature.properties.density`属性。并且可以调整显示样式以及在触摸时增加一个带有虚线边框。

	function style(feature) {
		return {
			fillColor: getColor(feature.properties.density),
			weight: 2,
			opacity: 1,
			color: 'white',
			dashArray: '3',
			fillOpacity: 0.7
		};
	}

	L.geoJson(statesData, {style: style}).addTo(map);

它看起来更加好看了！

<div id="map3" style="height: 300px"></div>


### 增加交互

现在让我们把鼠标悬浮在各州上面时高亮它们。首先，我们定义一个`mouseover`事件的监听函数：

	function highlightFeature(e) {
		var layer = e.target;

		layer.setStyle({
			weight: 5,
			color: '#666',
			dashArray: '',
			fillOpacity: 0.7
		});

		if (!L.Browser.ie && !L.Browser.opera) {
			layer.bringToFront();
		}
	}

这里我们通过`e.target`来获取悬浮的图层信息，在图层中设置一个宽的灰色边界作为高亮的效果，同时把它放置到最前面，以至于边界不会被附近的州所影响（但是在IE好玩Opera中不可以，因为它们处理`mouseover`事件中的`bringToFront`会带来问题）。

下一步，我们将定义`mouseout`事件发生的事情：

	function resetHighlight(e) {
		geojson.resetStyle(e.target);
	}

`geojson.resetStyle`事件处理函数将重置图层样式为默认样式（通过在`style`函数中定义）。 为了让这个函数起效，要确保GeoJSON图层是可用的，就要保证在监听之前和把图层赋给`geojson`之前，定义好`geojson`变量：

	var geojson;
	// ... our listeners
	geojson = L.geoJson(...);

作为可选的触摸事件，我们定义`click`监听器来监听各州缩放的改变：

	function zoomToFeature(e) {
		map.fitBounds(e.target.getBounds());
	}

现在，我们使用`onEachFeature`选项来增加我们各州图层的监听器：

	function onEachFeature(feature, layer) {
		layer.on({
			mouseover: highlightFeature,
			mouseout: resetHighlight,
			click: zoomToFeature
		});
	}

	geojson = L.geoJson(statesData, {
		style: style,
		onEachFeature: onEachFeature
	}).addTo(map);

这样可以使鼠标悬浮时，各州可以高亮显示，从而可以让监听器增加交互的能力。

### 自定义信息控件
我们可以在显示不同州信息的时候使用常规的弹出框，但是也可以选择不同的方式：通过悬浮的[自定义控件](../reference.html#icontrol)上显示不同的信息。

这里是我们控件的代码：

	var info = L.control();

	info.onAdd = function (map) {
		this._div = L.DomUtil.create('div', 'info'); // create a div with a class "info"
		this.update();
		return this._div;
	};

	// method that we will use to update the control based on feature properties passed
	info.update = function (props) {
		this._div.innerHTML = '<h4>US Population Density</h4>' +  (props ?
			'<b>' + props.name + '</b><br />' + props.density + ' people / mi<sup>2</sup>'
			: 'Hover over a state');
	};

	info.addTo(map);

当用户悬浮到一个州上的时候，我们需要更新控件，所以我们也需要如下所示修改监听器：

	function highlightFeature(e) {
		...
		info.update(layer.feature.properties);
	}

	function resetHighlight(e) {
		...
		info.update();
	}

这个控件也需要一些CSS样式，这样看起来更加炫酷：

{: .css}
	.info {
		padding: 6px 8px;
		font: 14px/16px Arial, Helvetica, sans-serif;
		background: white;
		background: rgba(255,255,255,0.8);
		box-shadow: 0 0 15px rgba(0,0,0,0.2);
		border-radius: 5px;
	}
	.info h4 {
		margin: 0 0 5px;
		color: #777;
	}

### 自定义图例控件

创建一个图例控件是非常容易的，因为它的静态的并且不会改变。JavaScript代码：

	var legend = L.control({position: 'bottomright'});

	legend.onAdd = function (map) {

		var div = L.DomUtil.create('div', 'info legend'),
			grades = [0, 10, 20, 50, 100, 200, 500, 1000],
			labels = [];

		// loop through our density intervals and generate a label with a colored square for each interval
		for (var i = 0; i < grades.length; i++) {
			div.innerHTML +=
				'<i style="background:' + getColor(grades[i] + 1) + '"></i> ' +
				grades[i] + (grades[i + 1] ? '&ndash;' + grades[i + 1] + '<br>' : '+');
		}

		return div;
	};

	legend.addTo(map);

控件的CSS样式（我们再次使用`info`类来定义）：

{: .css}
	.legend {
		line-height: 18px;
		color: #555;
	}
	.legend i {
		width: 18px;
		height: 18px;
		float: left;
		margin-right: 8px;
		opacity: 0.7;
	}

享受这个教程的结果[回到页面顶部](#map)或者打开[单独的页面](choropleth-example.html).

<script src="us-states.js"></script>
<script>
	var map2 = L.map('map2').setView([37.8, -96], 4);
	L.tileLayer(MB_URL, {attribution: MB_ATTR, id: 'mapbox.light'}).addTo(map2);
	L.geoJson(statesData).addTo(map2);


	var map3 = L.map('map3').setView([37.8, -96], 4);
	L.tileLayer(MB_URL, {attribution: MB_ATTR, id: 'mapbox.light'}).addTo(map3);
	L.geoJson(statesData, {style: style}).addTo(map3);


	var map = L.map('map').setView([37.8, -96], 4);

	L.tileLayer(MB_URL, {attribution: MB_ATTR, id: 'mapbox.light'}).addTo(map);

	// control that shows state info on hover
	var InfoControl = L.Control.extend({

		onAdd: function (map) {
			this._div = L.DomUtil.create('div', 'info');
			this.update();
			return this._div;
		},

		update: function (props) {
			this._div.innerHTML = '<h4>US Population Density</h4>' +  (props ?
				'<b>' + props.name + '</b><br />' + props.density + ' people / mi<sup>2</sup>'
				: 'Hover over a state');
		}
	});

	var info = new InfoControl();

	info.addTo(map);


	// get color depending on population density value
	function getColor(d) {
		return d > 1000 ? '#800026' :
		       d > 500  ? '#BD0026' :
		       d > 200  ? '#E31A1C' :
		       d > 100  ? '#FC4E2A' :
		       d > 50   ? '#FD8D3C' :
		       d > 20   ? '#FEB24C' :
		       d > 10   ? '#FED976' :
		                  '#FFEDA0';
	}

	function style(feature) {
		return {
			weight: 2,
			opacity: 1,
			color: 'white',
			dashArray: '3',
			fillOpacity: 0.7,
			fillColor: getColor(feature.properties.density)
		};
	}

	function highlightFeature(e) {
		var layer = e.target;

		layer.setStyle({
			weight: 5,
			color: '#666',
			dashArray: '',
			fillOpacity: 0.7
		});

		if (!L.Browser.ie && !L.Browser.opera) {
			layer.bringToFront();
		}

		info.update(layer.feature.properties);
	}

	var geojson;

	function resetHighlight(e) {
		geojson.resetStyle(e.target);
		info.update();
	}

	function zoomToFeature(e) {
		map.fitBounds(e.target.getBounds());
	}

	function onEachFeature(feature, layer) {
		layer.on({
			mouseover: highlightFeature,
			mouseout: resetHighlight,
			click: zoomToFeature
		});
	}

	geojson = L.geoJson(statesData, {
		style: style,
		onEachFeature: onEachFeature
	}).addTo(map);

	map.attributionControl.addAttribution('Population data &copy; <a href="http://census.gov/">US Census Bureau</a>');


	var legend = L.control({position: 'bottomright'});

	legend.onAdd = function (map) {

		var div = L.DomUtil.create('div', 'info legend'),
			grades = [0, 10, 20, 50, 100, 200, 500, 1000],
			labels = [],
			from, to;

		for (var i = 0; i < grades.length; i++) {
			from = grades[i];
			to = grades[i + 1];

			labels.push(
				'<i style="background:' + getColor(from + 1) + '"></i> ' +
				from + (to ? '&ndash;' + to : '+'));
		}

		div.innerHTML = labels.join('<br>');
		return div;
	};

	legend.addTo(map);

</script>
