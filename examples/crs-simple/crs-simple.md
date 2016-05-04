---
layout: tutorial_v2
title: Non-geographical maps
---

<style>
iframe {
    border: 1px solid #ccc;
    border-radius: 5px;
}
</style>

## 没有地理信息的地图

有时，地图并不只是呈现地球表面上的东西，例如呈现一些没有经纬度概念的东西。这些情况中的大多数是呈现一个很大的图片，例如游戏地图。

在这个教程中，我们使用星际2的游戏地图，这个游戏在[开源项目Ur-Quan Masters](https://en.wikipedia.org/wiki/Star_Control_II#The_Ur-Quan_Masters)中仍然是可玩的。这些游戏地图通过[开源数据文件阅读工具](http://www.highprogrammer.com/alan/games/video/uqm/index.html)制作，看起来就像这样：

<center>
<img src="uqm_map_400px.png" style="border: 1px solid #ccc; border-radius: 5px" /><br/>
</center>

这个游戏构建在一个方形的坐标系统中，这将允许我们为它创建一个坐标系。

<center>
<img src="uqm_map_detail.png" style="border: 1px solid #ccc; border-radius: 5px" /><br/>
</center>


## CRS.Simple

**CRS**是[coordinate reference system](https://en.wikipedia.org/wiki/Spatial_reference_system)的缩写，一个由地理学家组成团队，解释了坐标系在矢量坐标中的含义。例如，如果使用经纬度的话，`[15, 60]`代表了在地球上印度洋中的一个点, 或者在我们星系中太阳系中的Krueger-Z。

Leaflet地图之友一个坐标系，在创建地图的时候可以改变它。我们在游戏地图中将使用`CRS.Simple`，它代表一个方形网格：

	var map = L.map('map', {
		crs: L.CRS.Simple
	});

然后我们仅仅使用`L.ImageOverlay`增加游戏星系图，并设置它的*大致*的边界：

	var bounds = [[0,0], [1000,1000]];
	var image = L.imageOverlay('uqm_map_full.png', bounds).addTo(map);

然后显示整个地图：

	map.fitBounds(bounds);

{% include frame.html url="crs-simple-example1.html" %}

这个例子不能很好的工作，因为我们在使用了`fitBounds()`之后不能很好的看全整个地图。


## 在CRS.Simple地图中的常见问题

Leaflet坐标系默认是`CRS.Earth`，360度经度在地图上被划分为256个水平像素（在缩放级别为0的时候）, 大约170度纬度在地图上被划分为256个垂直像素（在缩放级别为0的时候）。 

在`CRS.Simple`中，一个水平的地图单位被划分为一个水平的像素，垂直的情况也一样。这意味着整个地图被划分为1000x1000像素的大小，并超出外部的HTML容器。幸运的是，我们可以设置`minZoom`为比0更小的值：

	var map = L.map('map', {
		crs: L.CRS.Simple,
		minZoom: -5
	});

### 像素 VS 地图单位

一个常见的错误是使用`CRS.Simple`的时候，假设地图单位等于图片像素。在这种情况下，地图转换为1000x1000的单位，但是图片却有2315x2315像素这么大。有些情况下，一个像素等于一个地图单位；有些情况下，64个像素等于一个地图单位。考虑在网格中的**地图单位**，然后适当的添加你的图层（`L.ImageOverlay`或者`L.Marker`等）。

事实上，我们使用的图像超过了1000个地图单位，因为有相当大的边界。通过测量在0到1000坐标中有多少像素点，我们能够在图片中正确的使用坐标边界：

	var bounds = [[-26.5,-25], [1021.5,1023]];
	var image = L.imageOverlay('uqm_map_full.png', bounds).addTo(map);

当我们完成它之后，添加几个标注点：

	var sol = L.latLng([ 145, 175.2 ]);
	L.marker(sol).addTo(map);
	map.setView( [70, 120], 1);

{% include frame.html url="crs-simple-example2.html" %}

### 这里没有`LatLng`

你将注意到Sol的坐标为`[145,175]`，而不是`[175,145]`，同样这也发生在地图中心点上。`CRS.Simple`坐标系采用`[y, x]`来代替`[x, y]`，同样在Leaflet中也采用`[lat, lng]`来代替`[lng, lat]`。

<small>(在技术方面，Leaflet倾向于使用[`[northing, easting]`](https://en.wikipedia.org/wiki/Easting_and_northing)来代替`[easting, northing]`－－第一个坐标点是"north"坐标对，第二个是"east"坐标对)</small>

关于使用`[lng, lat]`或者`[lat, lng]`或者`[y, x]`或者`[x, y]` [已经不是新问题了，并且没有一个清楚的统一意见](http://www.macwright.org/lonlat/)。这就是为什么Leaflet把类名`L.LatLng`替换为`L.Coordinate`的原因。

如果使用命名为`L.LatLng`的`[y, x]`坐标对你来说很困惑的话，那么把她包装一下会更加的容易：

	var yx = L.latLng;

	var xy = function(x, y) {
		if (L.Util.isArray(x)) {    // When doing xy([x, y]);
			return yx(x[1], x[0]);
		}
		return yx(y, x);  // When doing xy(x, y);
	};

现在我们使用`[x, y]`坐标添加一些星星和一条导航线：

	var sol      = xy(175.2, 145.0);
	var mizar    = xy( 41.6, 130.1);
	var kruegerZ = xy( 13.4,  56.5);
	var deneb    = xy(218.7,   8.3);

	L.marker(     sol).addTo(map).bindPopup(      'Sol');
	L.marker(   mizar).addTo(map).bindPopup(    'Mizar');
	L.marker(kruegerZ).addTo(map).bindPopup('Krueger-Z');
	L.marker(   deneb).addTo(map).bindPopup(    'Deneb');

	var travel = L.polyline([sol, deneb]).addTo(map);

现在地图看起来一样棒，但是代码更加容易阅读了。

{% include frame.html url="crs-simple-example3.html" %}
