---
layout: tutorial
title: Quick Start Guide
---

## Leaflet快速开始

本教程将一步一步的指导你快速开始Leaflet的基础开发，包括设置一个Leaflet地图、新建一个标志点、绘制线、弹出框以及事件处理。


<div id="mapid" class="mapclass" style="height: 180px"></div>

[单独打开页面查看这个例子 &rarr;](quick-start-example.html)


### 准备你的页面

在开始写代码之前，你需要完成以下几个步骤来准备你的页面：

 * 在页面的head标签中添加以下代码来引入CSS文件：

		<link rel="stylesheet" href="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.css" />

 * 添加Leaflet JavaScript文件：

		<script src="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.js"></script>

 * 将id为mapid的div标签放到地图显示的地方：

		<div id="mapid"></div>

 * 确保地图容器有定义好的高度，例如在css文件中添加如下代码：

	<pre><code class="css">#mapid { height: 180px; }</code></pre>

现在，你已经完成初始化地图，继续做一些东西吧！


### 设置地图

<div id="mapid1" class="mapclass" style="height: 180px"></div>

让我们创建一个中心点在伦敦，使用Mapbox街道瓦片的地图。首先，我们初始化地图，并且设置地图在视窗的中心点和缩放级别：

	var mymap = L.map('mapid').setView([51.505, -0.09], 13);

默认情况下（我们创建一个地图实例不使用任何选项），所有的鼠标和触摸事件在地图中都是可用的，并且有缩放级别和属性控制。

注意，setView函数返回一个地图实例－－大多数Leaflet方法都不返回详细的值，而是像jQuery一样进行链式调用。

下一步我们将在地图上增加一个瓦片图层，在这个例子中我们将使用Mapbox街道瓦片图层。创建一个瓦片图层通常涉及到设置[URL template](./reference.html#url-template)来使用瓦片图（获得你的瓦片图在[Mapbox](http://mapbox.com)），设置的属性还包括属性文本和图层的最大比例尺：

<pre><code class="javascript">L.tileLayer('https://api.tiles.mapbox.com/v4/{id}/{z}/{x}/{y}.png?access_token={accessToken}', {
	attribution: 'Map data &amp;copy; <span class="text-cut" data-cut="[&hellip;]">&lt;a href="http://openstreetmap.org"&gt;OpenStreetMap&lt;/a&gt; contributors, &lt;a href="http://creativecommons.org/licenses/by-sa/2.0/"&gt;CC-BY-SA&lt;/a&gt;, Imagery &copy; &lt;a href="http://mapbox.com"&gt;Mapbox&lt;/a&gt;</span>',
	maxZoom: 18,
	id: '<a href="https://www.mapbox.com/projects/">your.mapbox.project.id</a>',
	accessToken: '<a href="https://www.mapbox.com/account/apps/">your.mapbox.public.access.token</a>'
}).addTo(mymap);</code></pre>

确保所有的代码都在创建div标签和引用leaflet.js之前。这样应该没有什么问题了，你现在应用有一个可以使用的Leaflet地图了。

值得注意的是，Leaflet底图功能与底图供应商无关，意味着你可以选择你喜欢的瓦片地图供应商。它甚至不包括单一的专有瓦片地图提供商代码，所以你可以自由的使用其他瓦片地图提供商的底图(我们推荐使用Mapbox，它看起来特别漂亮！)。


### 标注点，圆和多边形

<div id="mapid2" class="mapclass" style="height: 180px"></div>

除了瓦片图，你应该轻松的在地图上添加其他东西，包括标注点、线、面、圆和弹出框。让我们添加一个标注点：

	var marker = L.marker([51.5, -0.09]).addTo(mymap);

添加一个圆也是通过同样的方式（除了实质半径作为第二个参数），通过最后一个参数来控制圆显示的方式：

	var circle = L.circle([51.508, -0.11], 500, {
		color: 'red',
		fillColor: '#f03',
		fillOpacity: 0.5
	}).addTo(mymap);

添加一个多边形也是如此的容易：

	var polygon = L.polygon([
		[51.509, -0.08],
		[51.503, -0.06],
		[51.51, -0.047]
	]).addTo(mymap);


### 使用弹出框

<div id="mapid3" class="mapclass" style="height: 180px"></div>

当你想给地图中某些特殊的要素贴上标签的时候，通常会使用弹出框。Leaflet有一个非常便捷的方法来处理弹出框：

	marker.bindPopup("<b>Hello world!</b><br>I am a popup.").openPopup();
	circle.bindPopup("I am a circle.");
	polygon.bindPopup("I am a polygon.");

试试点击这个对象，bindPopup方法将绑定把HTML内容和弹出框绑定到一起。当你点击这个对象之后，bindPopup将马上打开一个弹出框。

你也可以使用图层的弹出框（当你需要在超过一个对象上使用弹出框的时候）：

	var popup = L.popup()
		.setLatLng([51.5, -0.09])
		.setContent("I am a standalone popup.")
		.openOn(mymap);

在这里，我们使用openOn代替了addTo方法，因为之前打开的弹出框会自动关闭，新的方法将更加的好用。


### 处理事件

每次在Leaflet发生事件，相应的对象都会发送一个事件，例如点击标注点、地图缩放。你可以订阅这个事件，它允许你对用户交互作出反应：

	function onMapClick(e) {
		alert("You clicked the map at " + e.latlng);
	}

	mymap.on('click', onMapClick);

每个对象都有自己一系列的事件－－详细请看[文档](../reference.html)。监听函数的第一个参数是一个事件对象，它包括有关事件发生的有用信息。例如，地图点击事件对象包括latlng属性，它代表鼠标所点击地图的经纬度。

让我们使用弹出框代替alert来改善这个例子。

	var popup = L.popup();

	function onMapClick(e) {
		popup
			.setLatLng(e.latlng)
			.setContent("You clicked the map at " + e.latlng.toString())
			.openOn(mymap);
	}

	mymap.on('click', onMapClick);

试图在地图在点击，你将会在弹出框中看到经纬度。<a target="_blank" href="quick-start-example.html">查看整个实例 &rarr;</a>

现在你已经学会的Leaflet的基本用法，并且能够创建一个地图应用。不要忘记查看详细的<a href="../reference.html">文档</a>或者<a href="../examples.html">其他例子</a>。

<script>

	var mymap = L.map('mapid').setView([51.505, -0.09], 13);

	L.tileLayer(MB_URL, {attribution: MB_ATTR, id: 'mapbox.streets'}).addTo(mymap);

	L.marker([51.5, -0.09]).addTo(mymap)
		.bindPopup("<b>Hello world!</b><br />I am a popup.").openPopup();

	L.circle([51.508, -0.11], 500, {
		color: 'red',
		fillColor: '#f03',
		fillOpacity: 0.5
	}).addTo(mymap).bindPopup("I am a circle.");

	L.polygon([
		[51.509, -0.08],
		[51.503, -0.06],
		[51.51, -0.047]
	]).addTo(mymap).bindPopup("I am a polygon.");

	var mymap1 = L.map('mapid1').setView([51.505, -0.09], 13);
	L.tileLayer(MB_URL, {attribution: MB_ATTR, id: 'mapbox.streets'}).addTo(mymap1);

	var mymap2 = L.map('mapid2').setView([51.505, -0.09], 13);
	L.tileLayer(MB_URL, {attribution: MB_ATTR, id: 'mapbox.streets'}).addTo(mymap2);

	L.marker([51.5, -0.09]).addTo(mymap2);

	L.circle([51.508, -0.11], 500, {
		color: 'red',
		fillColor: '#f03',
		fillOpacity: 0.5
	}).addTo(mymap2);

	L.polygon([
		[51.509, -0.08],
		[51.503, -0.06],
		[51.51, -0.047]
	]).addTo(mymap2);



	var mymap3 = L.map('mapid3').setView([51.505, -0.09], 13);
	L.tileLayer(MB_URL, {attribution: MB_ATTR, id: 'mapbox.streets'}).addTo(mymap3);

	L.marker([51.5, -0.09]).addTo(mymap3)
		.bindPopup("<b>Hello world!</b><br />I am a popup.").openPopup();

	L.circle([51.508, -0.11], 500, {
		color: 'red',
		fillColor: '#f03',
		fillOpacity: 0.5
	}).addTo(mymap3).bindPopup("I am a circle.");

	L.polygon([
		[51.509, -0.08],
		[51.503, -0.06],
		[51.51, -0.047]
	]).addTo(mymap3).bindPopup("I am a polygon.");

</script>
