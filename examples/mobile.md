---
layout: tutorial
title: Leaflet on Mobile
---

## 在移动端使用Leaflet

在这个例子中，你将学会如何创建一个全屏地图，从而在移动设备中使用，例如iPhone、iPad或者Android手机。同时也将学会如何获得和使用用户当前的地理位置。

[单独打开页面查看这个例子 &rarr;](mobile-example.html)

### 准备你的页面

首先，我们准备页面中的HTML和CSS代码。确保地图div元素扩展到整个屏幕（全屏），我们将使用下面的CSS代码：

{: .css}
	body {
		padding: 0;
		margin: 0;
	}
	html, body, #map {
		height: 100%;
	}

同时，我们需要告诉移动端浏览器禁用页面缩放，并且设置准确的大小。在html页面中的head标签添加以下代码：

	<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />

### 初始化地图

我们使用以下JavaScript代码来初始化地图，这与我们在[快速开始](quick-start.html)中类似，但是我们不设置地图的中心点：

<pre><code class="javascript">var map = L.map('map');

L.tileLayer('https://api.tiles.mapbox.com/v4/<a href="https://mapbox.com">MapID</a>/997/256/{z}/{x}/{y}.png?access_token={accessToken}', {
	attribution: 'Map data &amp;copy; <span class="text-cut" data-cut="[&hellip;]">&lt;a href="http://openstreetmap.org"&gt;OpenStreetMap&lt;/a&gt; contributors, &lt;a href="http://creativecommons.org/licenses/by-sa/2.0/"&gt;CC-BY-SA&lt;/a&gt;, Imagery &copy; &lt;a href="http://mapbox.com"&gt;Mapbox&lt;/a&gt;</span>',
	maxZoom: 18
}).addTo(map);</code></pre>

### 定位

Leaflet有一个非常巧妙的方法来处理地图的缩放级别和探测用户地理位置－－带有setView选项的locate方法。使用常用的setView方法在下面的代码中：

	map.locate({setView: true, maxZoom: 16});

这里我们设置最大的缩放级别为16，并设置地图根据位置自动调整窗口。当用户同意浏览器分享用户位置后，地图将自动调整视窗中心为该位置。现在我们有了一个全屏的移动端地图！但是如果我们要在定位完成之后做一些事情呢？这里有locationfound和locationerror事件来处理这些事情。让我们在这个例子中增加一个标注点用来探测位置，并在一个弹出框中显示定位精度，通过在locateAndSetView函数调用之前监听locationfound事件即可：

	function onLocationFound(e) {
		var radius = e.accuracy / 2;

		L.marker(e.latlng).addTo(map)
			.bindPopup("You are within " + radius + " meters from this point").openPopup();

		L.circle(e.latlng, radius).addTo(map);
	}

	map.on('locationfound', onLocationFound);

完美！但是如果在定位失败后添加一些提示信息将会更好：

	function onLocationError(e) {
		alert(e.message);
	}

	map.on('locationerror', onLocationError);

如果你将setView选项设置为true并且定位失败了，它将会把地图视窗设置为世界地图。

现在这个例子讲完了－－尝试它在你的移动手机中：
Now the example is complete --- try it on your mobile phone: [单独打开页面查看这个例子 &rarr;](mobile-example.html)

下一步你应该查看详细的<a href="../reference.html">文档</a>或者<a href="../examples.html">其他例子</a>。
