<template>
  <div>
    <div id="myMap" :style="{width: '99vw', height: '80vh'}"></div>
  </div>
</template>

<script>
  import shaowulines from '../assets/shaowulines';

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
        function updateMesh(obj) {
          let prism = prisms.find(function (prism) {
            return prism == obj;
          });
          // 重置 Mesh 颜色
          prisms.forEach(function (prism) {
            updateMeshColor(prism, color);
          });

          // 更新选中 Mesh 的 vertexColors
          if (prism) {
            updateMeshColor(prism, selectColor);
          }
        }
        function updateMeshColor(prism, color){
          let vertexColors = prism.geometry.vertexColors;
          let len = vertexColors.length;
          for (let i = 0; i < len / 4; i++) {
            let r = color[0];
            let g = color[1];
            let b = color[2];
            let a = color[3];
            // 不能重新赋值，只允许修改内容
            vertexColors.splice(i * 4, 4, r, g, b, a);
          }

          prism.needUpdate = true;
          prism.reDraw();
        }
        let prisms= [];
        let color = [100 / 255, 150 / 255, 230 / 255, 0.9];
        let selectColor = [255 / 255, 245 / 255, 47 / 255, 0.9];
        // 基于准备好的dom，初始化echarts实例
        let myMap = new AMap.Map('myMap', {
          center: [117.492534, 27.340327],
          pitch: 45, // 地图俯仰角度，有效范围 0 度- 83 度
          viewMode: '3D', // 地图模式
          zoom: 10,
          mapStyle: 'amap://styles/4f59cb5ce9a257218d40dac1c33a6674'
        });
        myMap.AmbientLight = new AMap.Lights.AmbientLight([1, 1, 1], 0.5);
        myMap.DirectionLight = new AMap.Lights.DirectionLight([0, 0, 1], [1, 1, 1], 1);

        let object3Dlayer = new AMap.Object3DLayer();
        myMap.add(object3Dlayer);
        let boundsArr = shaowulines;
        let height = 36000;
        for(let distinct of boundsArr){
            let prism = new AMap.Object3D.Prism({
                path: distinct.bounds,
                height: height,
                color: color
            });
            prisms.push(prism);
            prism.transparent = true;
            // 绘制图表
            object3Dlayer.add(prism);
        }
        myMap.on('click',function (ev) {
          let pixel = ev.pixel;
          let px = new AMap.Pixel(pixel.x, pixel.y);
          let obj = myMap.getObject3DByContainerPos(px, [object3Dlayer], false) || {};
          // 选中的 object3D 对象，这里为当前 Mesh
          let object = obj.object;
          updateMesh(object);
        });
      }
    }
  }
</script>

