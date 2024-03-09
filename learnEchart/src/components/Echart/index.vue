<template>
  <div>
    <div class="mapContainer" id="mapContainer" ref="mapContainerRef"></div>
  </div>
</template>

<script lang="ts" setup>
import { nextTick, onMounted, ref } from "vue";
import * as echarts from "echarts";
const mapContainerRef = ref(null);
let myChart, option;

const init = async () => {
  myChart = echarts.init(mapContainerRef.value);
  const mapName = "china";
  const chinaGeoJson = await getMapData(mapName);
  echarts.registerMap(mapName, chinaGeoJson); // 注册可用地图，名称需要一致
  option = {
    geo: {
      show: true,
      map: mapName, // 展示的哪种地图 某国、某省市区【前提需要有地图文件】
      roam: true,
      // center: [116.413387, 39.910924], //当前视角的中心点，用经纬度表示
      /**
       * 地图的长宽比
       * geoBoundingRect.width / geoBoundingRect.height * aspectScale
       */
      aspectScale: 0.75,
      // 二维数组，定义定位的左上角以及右下角分别所对应的经纬度
      //   boundingCoords: [
      //     [118.802422, 32.064652], // 南京
      //     [121.48054, 31.235929], // 上海
      //   ],
      //   zoom: 2,
      //   scaleLimit: {
      //     min: 1,
      //     max: 15,
      //   },
      //   nameMap: "", // 自定义地区的名称映射【单独控制某个省份样式】
      selectedMode: "multiple",
      label: {
        show: true,
        // position: "top", // position至formatter 官方复制的，不会生效
        // rotate: 45, // 生效
        color: "red",
        fontSize: 16,
      },
      itemStyle: {
        areaColor: "gray",
        borderColor: "#fff",
        borderWidth: 2,
      },
      emphasis: {
        // 高亮
        label: {
          color: "blue",
          fontWeight: "bold",
          fontSize: 30,
        },
        itemStyle: {
          areaColor: "teal",
          borderColor: "drakBlue",
        },
      },
      layoutCenter: ["50%", "50%"],
      layoutSize: "100%",
      regions: [
        //在地图中对特定的区域配置样式。
        {
          name: "陕西省",
          selected: false,
          itemStyle: {
            areaColor: "brown",
          },
          label: {
            color: "black",
          },
          emphasis: {
            // 高亮
            itemStyle: {
              areaColor: "green",
              borderColor: "gold",
            },
            label: {
              color: "#fff",
              fontWeight: "bold",
              fontSize: 20,
            },
          },
        },
      ],
    },
  };
  myChart.setOption(option);
};
const getMapData = async (mapName) => {
  const response = await fetch(`./public/data/${mapName}.geojson`); // 请将'china.json'替换为实际的地图数据文件路径
  return await response.json();
};
onMounted(() => {
  init();
});
</script>

<style scoped>
.mapContainer {
  width: 1200px;
  height: 800px;
  border: 0.0625rem solid #000;
}
</style>
