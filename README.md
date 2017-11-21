# WebMap2WMS

## 解决方案

（待写）

## 已有结论

当一家互联网地图服务提供有以下的两个接口时，可以将其加密后的地图切片近似还原到加密前：

1. 提供根据指定的坐标范围返回地图切片的接口。

2. 提供将原始坐标转换为加密后的坐标的接口。

对于接口1而言，大多数互联网地图不会直接提供这样的接口，此时需要利用静态地图的接口或者通过抓包并分析来得到切片接口。

对于接口2而言，大多数互联网地图是提供这样的接口的。

## 如何使用

1. 添加高德的key

请自行注册一个能够调用高德WebAPI的key，替换到`settings.py`文件内。

2. 启动服务

```
cd servicescode
python manage.py runserver 0.0.0.0:8011
```
3. 加载图层

在`QGIS`中选择`Add WMS/WMTS Layer`,新建一个WMS连接,URL填写
```
http://127.0.0.1:8011/debug?bbox={bbox-epsg-4326}&version=1.1.1&srs=EPSG:4326
```
即可。注意端口号与启动服务的命令统一。加载后如图所示：

![加载WMS服务](image/load_WMS_services.png)

* `Debug`图层返回的是用于调试的地图切片，显示一些由`QGIS`发送给WMS的参数。
* `Amap`图层返回的是原始的高德地图切片，与官网上的相似，保留了偏移效果。
* `Amap_Convert`图层返回的是纠偏后的高德地图切片，在大比例尺下的纠偏效果明显。

4. 指定切片大小

![指定切片大小](image/tile_size.png)

此处不指定切片的大小也能够正常显示。但是由于高德接口的限制，通过提供的API获取到的每张切片的大小不超过`1024*1024`，如果在后台实现拼接算法，可以突破这一限制。

所以建议此处填写`256*256`或者`512*512`。

5. 最终效果

红色的线为`OSM`的数据，如何加载请自行解决。

* `Amap`图层与`OSM`数据：

![未纠偏](image/amap_osm.png)

* `Amap_Convert`图层与`OSM`数据：

![已纠偏](image/amap_c_osm.png)

## 存在的问题

1. 加载图层时一个一个的添加，不能多选。
2. 获取大范围的切片时，本文档的纠偏方法效果不明显。