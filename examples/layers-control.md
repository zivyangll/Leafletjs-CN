---
layout: tutorial
title: Layer Groups and Layers Control
---

## 图层分组和图层控制

这个教程将告诉你如何管理地图图层，并且如何使用图层切换控件轻松的切换地图上的不同图层。

<div id="map" class="map" style="height: 250px"></div>

[单独打开页面查看这个例子 &rarr;](layers-control-example.html)


### 图层组

将一系列的图层合并到一个图层组中，代码如下所示：

	var littleton = L.marker([39.61, -105.02]).bindPopup('This is Littleton, CO.'),
		denver    = L.marker([39.74, -104.99]).bindPopup('This is Denver, CO.'),
		aurora    = L.marker([39.73, -104.8]).bindPopup('This is Aurora, CO.'),
	    golden    = L.marker([39.77, -105.23]).bindPopup('This is Golden, CO.');

与将它们直接放入地图中相反，通过使用<a href="http://leafletjs.com/reference.html#layergroup">LayerGroup</a>类来实现图层类：

	var cities = L.layerGroup([littleton, denver, aurora, golden]);

非常简单吧！现在合并城市标注点到一个`cities`图层，从而可以每次在地图中添加或者移除该图层了。

### 图层控制

Leaflet有一个非常好用的小控件，这个控件能让用户控制图层以便在地图上看到。除了如何使用，我们还示范另一个便捷的图层组功能。

一共有两类图层。底图：是有交互的封闭性图层（只有该图层在用户的地图上能被看到），例如瓦片图层。另一种是覆盖物图层：放置在底图上用户的东西。在这个例子中，我们使用两类底图（灰色底图和深色风格底图）来彼此转换，使用一个覆盖物作为城市标记（我们之前创建的）的开关。我们创建这些图层，并添加默认一个到地图中：

<pre><code>var grayscale = L.tileLayer(mapboxUrl, {id: '<a href="https://mapbox.com">MapID</a>', attribution: mapboxAttribution}),
	streets   = L.tileLayer(mapboxUrl, {id: '<a href="https://mapbox.com">MapID</a>', attribution: mapboxAttribution});

var map = L.map('map', {
	center: [39.73, -104.99],
	zoom: 10,
	layers: [grayscale, cities]
});</code></pre>

接下来，我们将创建两个对象。一个将包括我们的底图，一个将包括我们的覆盖物图层。这些对象里是一些简单的键值对。键是控件中显示的文本信息，例如"Streets"。相关的值是反映在图层中的值，例如`streets`。

<pre><code>var baseMaps = {
	"Grayscale": grayscale,
	"Streets": streets
};

var overlayMaps = {
    "Cities": cities
};</code></pre>

现在，就剩下创建一个[图层控制器](../reference.html#control-layers)并把它添加到地图中。创建图层控制器的第一个参数是底图图层对象，第二个参数是覆盖物图层对象。两个参数都是可选的，例如，你可以缺省第二个参数，只填第一个参数；也可以只填覆盖物对象，把第一个参数填为null。

<pre><code>L.control.layers(baseMaps, overlayMaps).addTo(map);</code></pre>

注意我们把`grayscale`和`cities`图层添加到地图中，并没有添加`streets`图层。图层控制器非常的聪明，能够探测到我们已经在地图上加载的图层以及相关的选项集。

还需要注意的是当使用多个底图图层的时候，仅仅只有一个底图能够作为实例添加到地图中，但是在创建地图控制器的时候，这些所有的底图都需要作为底图图层对象这个参数。

现在，让我们[单独打开页面查看这个例子 &rarr;](layers-control-example.html)。

<script>
	var cities = new L.LayerGroup();

    L.marker([39.61, -105.02]).bindPopup('This is Littleton, CO.').addTo(cities),
	L.marker([39.74, -104.99]).bindPopup('This is Denver, CO.').addTo(cities),
	L.marker([39.73, -104.8]).bindPopup('This is Aurora, CO.').addTo(cities),
	L.marker([39.77, -105.23]).bindPopup('This is Golden, CO.').addTo(cities);

    var grayscale = L.tileLayer(MB_URL, {attribution: MB_ATTR, id: 'mapbox.light'}),
	    streets = L.tileLayer(MB_URL, {attribution: MB_ATTR, id: 'mapbox.streets'});

	var map = L.map('map', {
		center: [39.73, -104.99],
		zoom: 10,
		layers: [grayscale, cities]
	});

	var baseLayers = {
		"Grayscale": grayscale,
		"Streets": streets
	};

	var overlays = {
		"Cities": cities
	};

	L.control.layers(baseLayers, overlays).addTo(map);
</script>
