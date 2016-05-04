---
layout: tutorial
title: Working with map panes
---

## 什么是地图分层？

在Leaflet中，地图分层是隐式的，不需要开发者了解它。分层允许浏览器在多个图层上表现的比单独处理各个图层要高效的多。

地图分层使用[CSS属性z-index](https://developer.mozilla.org/docs/Web/CSS/z-index) 来允许一些图层位于其他图层的顶部。[默认顺序](http://leafletjs.com/reference.html#map-panes)是:

* 瓦片图层和网格图层
* 路径，类似于直线、折线、圆或者`GeoJSON`图层。
* 标志点阴影
* 标志点图标
* 弹出框

这就是为什么在Leaflet地图中，弹出框总是位于其他图层的顶部，标注点总是在瓦片图层的上面。

在**Leaflet 1.0.0**版本中有一个新的特性就是自定义地图分层顺序。

## 默认并不总是正确的

在一些特别的案例中，默认地图分层顺序并不总是正确的。我们可以[CartoDB底图](https://cartodb.com/basemaps/)和标签来证明：

<style>
.tiles img {
    border: 1px solid #ccc;
    border-radius: 5px;
}
</style>

<div class='tiles'>
<div style='display: inline-block'>
<img src="http://a.basemaps.cartocdn.com/light_nolabels/0/0/0.png" class="bordered-img" /><br/>
Basemap tile with no labels
</div>

<div style='display: inline-block'>
<img src="http://a.basemaps.cartocdn.com/light_only_labels/0/0/0.png" class="bordered-img" /><br/>
Transparent labels-only tile
</div>

<div style='display: inline-block; position:relative;'>
<img src="http://a.basemaps.cartocdn.com/light_nolabels/0/0/0.png" class="bordered-img" />
<img src="http://a.basemaps.cartocdn.com/light_only_labels/0/0/0.png"  style='position:absolute; left:0; top:0;'/><br/>
Labels on top of basemap
</div>
</div>

如果我们使用两个瓦片图创建了Leaflet地图，任何的多边形和标签都应该显示在顶部，但是只有标签显示在顶部[看起来会更好](http://blog.cartodb.com/let-your-labels-shine/)我们怎么做到的？


<div id="map" class="map" style="height: 250px"></div>

<link rel="stylesheet" href="https://leafletjs-cdn.s3.amazonaws.com/content/leaflet/master/leaflet.css" />
<script src="https://leafletjs-cdn.s3.amazonaws.com/content/leaflet/master/leaflet.js"></script>
<script type="text/javascript" src="eu-countries.js"></script>
<script>
var map = L.map('map');

map.createPane('labels');    

// This pane is above markers but below popups
map.getPane('labels').style.zIndex = 650;	

// Layers in this pane are non-interactive and do not obscure mouse/touch events
map.getPane('labels').style.pointerEvents = 'none';	


var cartodbAttribution = '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, &copy; <a href="http://cartodb.com/attributions">CartoDB</a>';

var positron = L.tileLayer('http://{s}.basemaps.cartocdn.com/light_nolabels/{z}/{x}/{y}.png', {
        attribution: cartodbAttribution
}).addTo(map);

var positronLabels = L.tileLayer('http://{s}.basemaps.cartocdn.com/light_only_labels/{z}/{x}/{y}.png', {
        attribution: cartodbAttribution,
        pane: 'labels'
}).addTo(map);

geojson = L.geoJson(euCountries).addTo(map);

geojson.eachLayer(function (layer) {
    layer.bindPopup(layer.feature.properties.name);
});

map.setView({ lat: 47.040182144806664, lng: 9.667968750000002 }, 4);
</script>

## 自定义分层

我们可以使用默认的瓦片底图和一些覆盖物图层，类似与GeoJSON图层。但是我们不得不为标注图层定义地图分层顺序，从而使它可以显示在GeoJSON数据的顶部。

自定义地图分层使用一个per-map创建，所以首先应该创建一个`L.Map`实例和pane：


    var map = L.map('map');
    map.createPane('labels');

下一步就是设置分层的z-index。查看[默认值](https://github.com/Leaflet/Leaflet/blob/master/dist/leaflet.css#L73)，650的z-index值将会带有注记的`TileLayer`图层位于所有标注点的顶部，但是在弹出框的下面。通过使用`getPane()`函数，我们引用[`HTMLElement`](https://developer.mozilla.org/docs/Web/API/HTMLElement)代表分层，从而通过改变z-index来改变顺序：


    map.getPane('labels').style.zIndex = 650;

对于瓦片图层位于其他图层的顶部所带来的问题就是，瓦片图层将捕获点击和触摸事件。如果用户在地图上点击的话，浏览器将默认用户点击的是注记图层，而不是GeoJSON或者标注点。这个问题可以通过[CSS属性`pointer-events`](https://developer.mozilla.org/en-US/docs/Web/CSS/pointer-events)来解决：


    map.getPane('labels').style.pointerEvents = 'none';


现在分层已经准备好了，我们可以添加图层了，注意在注记图层中使用的`pane`选项：


    var positron = L.tileLayer('http://{s}.basemaps.cartocdn.com/light_nolabels/{z}/{x}/{y}.png', {
            attribution: '©OpenStreetMap, ©CartoDB'
    }).addTo(map);

    var positronLabels = L.tileLayer('http://{s}.basemaps.cartocdn.com/light_only_labels/{z}/{x}/{y}.png', {
            attribution: '©OpenStreetMap, ©CartoDB',
            pane: 'labels'
    }).addTo(map);

    var geojson = L.geoJson(GeoJsonData, geoJsonOptions).addTo(map);

最终，在GeoJSON图层的每个特征上添加一些交互：

    geojson.eachLayer(function (layer) {
        layer.bindPopup(layer.feature.properties.name);
    });

    map.fitBounds(geojson.getBounds());


现在，这个[教程](map-panes-example.html)完成了！




