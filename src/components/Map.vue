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
      return {
        myMap: {},
        prisms: []
      }
    },
    mounted() {
      this.drawLine();
    },
    methods: {
      updateMeshColor(prism, color) {
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
      },
      updateMesh(obj) {
        let prism = this.prisms.find(function (prism) {
          return prism == obj;
        });

        // 重置 Mesh 颜色
        this.prisms.forEach(function (prism) {
          this.updateMeshColor(prism, color);
        });

        // 更新选中 Mesh 的 vertexColors
        if (prism) {
          this.updateMeshColor(prism, selectColor);
        }

      },
      drawLine() {
        let color = [100 / 255, 150 / 255, 230 / 255, 0.9];
        let selectColor = [255 / 255, 245 / 255, 47 / 255, 0.9];
        // 基于准备好的dom，初始化echarts实例
        this.myMap = new AMap.Map('myMap', {
          center: [117.492534, 27.340327],
          pitch: 45, // 地图俯仰角度，有效范围 0 度- 83 度
          viewMode: '3D', // 地图模式
          zoom: 10,
          mapStyle: 'amap://styles/4f59cb5ce9a257218d40dac1c33a6674'
        });
        this.myMap.AmbientLight = new AMap.Lights.AmbientLight([1, 1, 1], 0.5);
        this.myMap.DirectionLight = new AMap.Lights.DirectionLight([0, 0, 1], [1, 1, 1], 1);

        let object3Dlayer = new AMap.Object3DLayer();
        this.myMap.add(object3Dlayer);
        let boundsArr = shaowulines;
        let height = 16000;
        for(let bounds of boundsArr){
            let prism = new AMap.Object3D.Prism({
                path: bounds,
                height: height,
                color: color
            });
            this.prisms.push(prism);
            prism.transparent = true;
            // 绘制图表
            object3Dlayer.add(prism);
        }
      }
    }
  }
</script>

