##  使用ionic2接入百度地图
##### 在index.html中引入百度地图的js类库

```
<script type="text/javascript" src="http://api.map.baidu.com/api?v=2.0&ak=你的密钥"></script>
```
##### 密钥需要到百度地图API官方文档中申请
http://lbsyun.baidu.com/index.php?title=jspopular
##### html页面内容
```
<ion-content>
	<div id="map" #map></div>
</ion-content>
```
##### scss样式文件
```
#map{
    width: 100%;
    height: 100%; 
    display: block;
    overflow: hidden;
    margin:0;
    font-family:"微软雅黑";

}
.scroll{
    height: 100%;
}
```
##### 需要导入的声明文件
```
import { Component, ViewChild, ElementRef } from '@angular/core';
import { NavController, Platform} from 'ionic-angular';
```
##### 在typescript中接入百度地图类库
```
declare var BMap;
```

##### 使用注解在页面创建视图

```
export class HomePage {

  @ViewChild('map') mapElement: ElementRef;
  constructor(
    private navCtrl: NavController,
    private platform: Platform,
  ) 
```
##### 定义全局变量，调用初始化方法

```
 ionViewWillEnter() {
    let map = this.map = new BMap.Map(this.mapElement.nativeElement, { enableMapClick: true });//创建地图实例
    map.enableScrollWheelZoom();//启动滚轮放大缩小，默认禁用
    map.enableContinuousZoom();//连续缩放效果，默认禁用
    let point = new BMap.Point(116.06827, 22.549284);//坐标可以通过百度地图坐标拾取器获取
    map.centerAndZoom(point, 16);//设置中心和地图显示级别
```
##### 插入百度的一些控件，展示在地图中
```
//地图放大缩小控件
let sizeMap = new BMap.Size(10, 80);//显示位置
map.addControl(new BMap.NavigationControl({
  anchor: BMAP_ANCHOR_BOTTOM_RIGHT,//显示方向
  offset: sizeMap
}));

//3D效果矢量图控件
let size3D = new BMap.Size(10, 10);
map.addControl(new BMap.MapTypeControl({
  anchor: BMAP_ANCHOR_TOP_RIGHT,
  offset: size3D
}));
map.setCurrentCity("深圳");//3D效果需要设置城市

//城市列表控件
let sizeCity = new BMap.Size(10, 10);
map.addControl(new BMap.CityListControl({
  anchor: BMAP_ANCHOR_TOP_LEFT,
  offset: sizeCity
}));
```
##### 添加自定义的控件展示地图中
```
 function showAttractionControl() {
      //定义显示位置
      this.defaultAnchor = BMAP_ANCHOR_TOP_RIGHT;
      this.defaultOffset = new BMap.Size(10, 50);
    }
    //初始化控件
    showAttractionControl.prototype = new BMap.Control();
    showAttractionControl.prototype.initialize = function (map) {
      let div = document.createElement("button");// 创建一个按钮
      div.appendChild(document.createTextNode("附近景点"));
      div.style.width = "135px";
      div.style.height = "35px";
      div.style.borderRadius = "15px";
      div.onclick = function (e) {
        let local = new BMap.LocalSearch(map, {
          renderOptions: { map: map, autoViewport: true }
        });
        local.search("景点");
      }
      map.getContainer().appendChild(div);// 添加DOM元素到地图中
      return div;
    }
    let showAttraction = new showAttractionControl();
    map.addControl(showAttraction);//添加控件
```
##### 添加自定义标注在地图中

```
div.onclick = function (e) {//地图上可以添加自定义标注
    let icon = new BMap.Icon('http://pic002.cnblogs.com/images/2011/308287/2011091516161618.png', new BMap.Size(20, 32), {
      anchor: new BMap.Size(10, 30),
    })//设置标注图片和位置
    var mkr = new BMap.Marker(new BMap.Point(116.06827, 22.549284), {
      icon: icon,
      enableDragging: true,
      raiseOnDrag: true
    });//设置起始坐标点
    map.addOverlay(mkr);//添加标注在地图中并实现拖拽
}
```








