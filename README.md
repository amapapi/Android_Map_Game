Android_Map_Game
===============
本工程主要介绍了 高德地图Android SDK 3D版本 在基于地图的游戏方面的应用。

##前述：

- [高德官方网站申请key](http://id.amap.com/?ref=http%3A%2F%2Fapi.amap.com%2Fkey%2F).
- 阅读[参考手册](http://a.amap.com/lbs/static/unzip/Android_Map_Doc/index.html).
- 如果有任何疑问也可以发问题到[官方论坛](http://lbsbbs.amap.com/portal.php).

##效果图如下:

<iframe height=640 width=340 src="demo.gif">


##使用方法：


**1.搭建高德地图 AndroidSDK 工程方法见**
[地图SDK使用方法](http://developer.amap.com/api/android-sdk/summary/)

**2.OPENGL接口回调以及缩放比例**
    地图SDK使用OPENGL实现,可通过AMap.setCustomRenderer(CustomRenderer render)获取OPENGL渲染时的回调接口。
    
    ```java
     ...
         /**
          * 初始化AMap对象
          */
         private void init() {
             if (aMap == null) {
                 aMap = mapView.getMap();
      
                 //关闭文字
                 aMap.showMapText(false);
                 //关闭3d楼块
                 aMap.showBuildings(false);
                 //注1：设置opengl Renderer
                 aMap.setCustomRenderer(new MapRenderer(aMap));
             }
         }
         ...
      
         class MapRenderer implements CustomRenderer{
         //平移位置
         private float[] translate_vector = new float[4];
         //缩放比例
         public static float SCALE = 0.005F;
         private LatLng center = new LatLng(39.90403, 116.407525);// 北京市经纬度
         private AMap aMap;
         public MapRenderer(AMap aMap) {
             this.aMap = aMap;
             aMap.moveCamera(CameraUpdateFactory.newLatLngZoom(center,15));
         }
      
         @Override
         public void onDrawFrame(GL10 gl) {
             // 注2：绘制各种图形的opengl代码
      
         }
         ...
      
         @Override
         public void OnMapReferencechanged() {
             //注3：回调这个时，缩放比例发生改变，需要重新计算缩放比例
             calScaleAndTranslate();
      
         }
      
         private void calScaleAndTranslate() {
             // 坐标会变化，重新计算计算偏移，供参考，可以自行定义
             PointF pointF = aMap.getProjection().toOpenGLLocation(center);
      
             translate_vector[0] = pointF.x;
             translate_vector[1] = pointF.y;
             translate_vector[2] = 0;
      
             //重新计算缩放比例
             LatLng latLng2 = new LatLng(center.latitude + 0.001, center.longitude + 0.001);
             PointF pointF2 = aMap.getProjection().toOpenGLLocation(latLng2);
             double _x = Math.abs((pointF.x - pointF2.x));
             double _y = Math.abs((pointF.y - pointF2.y));
             SCALE = (float) Math.sqrt((_x * _x + _y * _y));
         }
     }
    ```