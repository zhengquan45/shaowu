<template>
  <div>
    <div id="map_container" :style="{width: '99vw', height: '80vh'}"></div>
  </div>
</template>

<script>
  import shaowuLine from "../assets/shaowuLine";

  export default {
    name: 'hello',
    data() {
      return {}
    },
    mounted() {
      this.drawMap();
    },
    methods: {
      drawMap() {
        let data = shaowuLine;
        // 1. 创建地图实例
        // let bmapgl = new BMapGL.Map('myMap');
        // let point = new BMapGL.Point(116.403748, 39.915055);
        // bmapgl.centerAndZoom(point, 17);
        // bmapgl.enableScrollWheelZoom(true);
        // bmapgl.setHeading(64.5);
        // bmapgl.setTilt(73);
        let bmapgl = initMap({
          tilt: 50,
          heading: 0,
          center: [117.49, 27.34],
          zoom: 13,
          style: purpleStyle
        });
        // 2. 创建MapVGL图层管理器
        let view = new mapvgl.View({
          map: bmapgl
        });
        debugger
        // 3. 创建可视化图层，并添加到图层管理器中
        let layer = new mapvgl.ShapeLineLayer({
          color: 'rgba(255, 71, 26, 0.9)',
          data: data
        });
        view.addLayer(layer);

        // // 4. 准备好规范化坐标数据
        // let data =
        //         [{
        //           geometry: {
        //             type: 'Point',
        //             coordinates: []
        //           }
        //         }];

        // 5. 关联图层与数据，享受震撼的可视化效果
        // layer.setData(data);

      }
    }
  }
</script>

