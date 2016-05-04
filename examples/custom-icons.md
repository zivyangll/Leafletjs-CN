---
layout: tutorial
title: Markers With Custom Icons
---

## 使用自定义图标的标注点

在这个教程中，你将学会如何使用自定义的图标作为地图上的标注点。

<div id="map" class="map" style="height: 220px"></div>

[单独打开页面查看这个例子 &rarr;](custom-icons-example.html)


### 准备你的图片

我们通常使用两个图片来制作自定义的图标：实际上的图标图片和它的阴影图片。在这个教程中，我们采用Leaflet logo并且创建四个图片－－三个不同颜色的叶子图片和阴影图片：

<p>
	<img style="border: 1px solid #ccc" src="../docs/images/leaf-green.png" />
	<img style="border: 1px solid #ccc" src="../docs/images/leaf-red.png" />
	<img style="border: 1px solid #ccc" src="../docs/images/leaf-orange.png" />
	<img style="border: 1px solid #ccc" src="../docs/images/leaf-shadow.png" />
</p>

注意白色部分实际上是透明的。

### 创建一个图标

Leaflet的标注点图标定义在[L.Icon](../reference.html#icon)对象中，它在创建标志点时作为一个参数。让我们创建一个绿色的叶子图标：

	var greenIcon = L.icon({
		iconUrl: 'leaf-green.png',
		shadowUrl: 'leaf-shadow.png',

		iconSize:     [38, 95], // size of the icon
		shadowSize:   [50, 64], // size of the shadow
		iconAnchor:   [22, 94], // point of the icon which will correspond to marker's location
		shadowAnchor: [4, 62],  // the same for the shadow
		popupAnchor:  [-3, -76] // point from which the popup should open relative to the iconAnchor
	});

现在我们把这个标志点放到地图中：

	L.marker([51.5, -0.09], {icon: greenIcon}).addTo(map);

<div id="map2" class="map" style="height: 220px"></div>

### 定义一个图标类

如果我们想创造大量类似的图标呢？让我们定义一个图标类，其中包括共享的参数，这个类继承自`L.Icon`！它在Leaflet中相当容易实现：

	var LeafIcon = L.Icon.extend({
		options: {
			shadowUrl: 'leaf-shadow.png',
			iconSize:     [38, 95],
			shadowSize:   [50, 64],
			iconAnchor:   [22, 94],
			shadowAnchor: [4, 62],
			popupAnchor:  [-3, -76]
		}
	});

现在我们使用这个类来创建三个页面图标：

	var greenIcon = new LeafIcon({iconUrl: 'leaf-green.png'}),
		redIcon = new LeafIcon({iconUrl: 'leaf-red.png'}),
		orangeIcon = new LeafIcon({iconUrl: 'leaf-orange.png'});

你可能会注意到我们使用了new关键字来创建LeafIcon实例。那为什么所有的Leaflet类创建时都不使用new？答案时非常简单的：实际上Leaflet类命名为首字母大写的单词，例如`L.Icon`，同时它们被创建也需要使用new关键字。但是它们采用了首字母小写的单词来进行转换，例如创建一个转换可能会像下面这样：｀

	L.icon = function (options) {
		return new L.Icon(options);
	};

你可以采用相同的方式来处理你自己的类。在最后，让我我们把这些图标放到地图中：

	L.marker([51.5, -0.09], {icon: greenIcon}).addTo(map).bindPopup("I am a green leaf.");
	L.marker([51.495, -0.083], {icon: redIcon}).addTo(map).bindPopup("I am a red leaf.");
	L.marker([51.49, -0.1], {icon: orangeIcon}).addTo(map).bindPopup("I am an orange leaf.");

下一步你应该查看详细的<a href="../reference.html">文档</a>或者<a href="../examples.html">其他例子</a>。

<script>
	var map = L.map('map').setView([51.5, -0.09], 13);

	L.tileLayer(MB_URL, {attribution: MB_ATTR, id: 'mapbox.light'}).addTo(map);

	var LeafIcon = L.Icon.extend({
		options: {
			iconUrl: '../docs/images/leaf-green.png',
			shadowUrl: '../docs/images/leaf-shadow.png',
			iconSize: [38, 95],
			shadowSize: [50, 64],
			iconAnchor: [22, 94],
			shadowAnchor: [4, 62],
			popupAnchor: [-3, -76]
		}
	});

	var greenIcon = new LeafIcon(),
		redIcon = new LeafIcon({iconUrl: '../docs/images/leaf-red.png'}),
		orangeIcon = new LeafIcon({iconUrl: '../docs/images/leaf-orange.png'});

	var marker1 = new L.Marker(new L.LatLng(51.5, -0.09), {icon: greenIcon}),
		marker2 = new L.Marker(new L.LatLng(51.495, -0.083), {icon: redIcon}),
		marker3 = new L.Marker(new L.LatLng(51.49, -0.1), {icon: orangeIcon});

	marker1.bindPopup("I am a green leaf.");
	marker2.bindPopup("I am a red leaf.");
	marker3.bindPopup("I am an orange leaf.");

	map.addLayer(marker1).addLayer(marker2).addLayer(marker3);



	var map2 = L.map('map2').setView([51.505, -0.09], 13);

	L.tileLayer(MB_URL, {attribution: MB_ATTR, id: 'mapbox.light'}).addTo(map2);

	var greenIcon2 = L.icon({
		iconUrl: '../docs/images/leaf-green.png',
		shadowUrl: '../docs/images/leaf-shadow.png',
		iconSize: [38, 95],
		shadowSize: [50, 64],
		iconAnchor: [22, 94],
		shadowAnchor: [4, 62],
		popupAnchor: [-3, -76]
	});

	L.marker([51.5, -0.09], {icon: greenIcon2}).addTo(map2);

</script>
