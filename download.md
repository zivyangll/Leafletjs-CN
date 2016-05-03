---
layout: v2
title: Download
bodyclass: download-page
---

## 下载Leaflet

<table>
	<tr>
		<th>版本</th>
		<th>描述</th>
	</tr>
	<tr>
		<td class="width100"><a href="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.zip">Leaflet 0.7.7</a></td>
		<td>稳定版，发布在2013年11月18日，最新更新在2015年10月26日。</td>
	</tr>
	<tr>
		<td><a href="http://cdn.leafletjs.com/leaflet/v1.0.0-rc.1/leaflet.zip">Leaflet 1.0.0-rc1</a></td>
		<td>最新的1.0候选版本，发布在2016年4月18日。</td>
	</tr>
	<tr>
		<td><a href="https://leafletjs-cdn.s3.amazonaws.com/content/leaflet/master/leaflet.zip">Leaflet 1.0-dev</a></td>
		<td>开发中的版本，开发在 <code>master</code> 分支上.</td>
	</tr>
</table>

[查看版本历史](https://github.com/Leaflet/Leaflet/blob/master/CHANGELOG.md)

注意：master版本可能包括一些不兼容旧版本的变化，请在更新版本之前仔细阅读更新文档。

### 使用在线的Leaflet版本

最新的版本版本发布在CDN上 &mdash; 把下面代码放到HTML文件的<head>标签中即可：

    <link rel="stylesheet" href="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.css" />
    <script src="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.js"></script>

或者

    <link rel="stylesheet" href="http://cdn.leafletjs.com/leaflet/v1.0.0-rc.1/leaflet.css" />
    <script src="http://cdn.leafletjs.com/leaflet/v1.0.0-rc.1/leaflet.js"></script>

### 使用离线下载的Leaflet版本

将代码下载下来，你将看到下面四个文件：

- `leaflet.js` - 经过压缩的JavaScript代码。
- `leaflet-src.js` - 未经过压缩的，用于调试和阅读的代码。
- `leaflet.css` - Leaflet的样式文件。
- `images` - 包括了`leaflet.css`中引用的文件. 它必须与 `leaflet.css` 在同一个目录下.

下载并解压到网站的根目录，并在<head>标签中添加以下HTML代码：

    <link rel="stylesheet" href="/path/to/leaflet.css" />
    <script src="/path/to/leaflet.js"></script> <!-- 或者使用 leaflet-src.js --!>

### Leaflet源码

上面下载的文件只包括Leaflet库本身，如果你想下载完整的源码：包括单元测试，调试文件，构建脚本等，你可以在 <a href="https://github.com/Leaflet/Leaflet">GitHub仓库</a><a href="https://github.com/Leaflet/Leaflet/releases">下载它</a>。

### 从源码编译Leaflet

Leaflet构建系统由[Node.js](http://nodejs.org)驱动，NodeJS在大多数系统中都很容易安装和使用。下面是安装步骤：

 1. [下载和安装Node](http://nodejs.org)
 2. 在命令行中运行以下命令：

 <pre><code>npm install -g jake
npm install</code></pre>

当安装结束后，在Leaflet根目录下运行 `jake build`，这将合并和压缩Leaflet源码， 并保存在 `dist` 目录中。


### 构建一个自定义的Leaflef版本

构建一个自定义的库，你只需要做一件事：打开Leaflet源码中的 `build/build.html` 页面，选择组件（它将自动计算依赖），并在命令行中生成它。
