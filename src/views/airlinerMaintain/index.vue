<template>
  <div>
    <button @click="startAnimation">Start Airplane Animation</button>
    <div id="containerEarth"></div>
  </div>
</template>

<script lang="ts" setup>
import { onMounted, inject } from "vue";
import { getFlylineMaterial, parabola, curvePlotting } from "./texture";

let Cesium: any = inject("$Cesium");
let geoCoordMap: any = {
  Delhi: [77.10002952485256, 28.558216772973513],
  Bangalore: [77.70688123260176, 13.199280935136354],
};
let viewer: any = null;
let uavEntity: any = null;
let geoCoordMapArr: any = [];
let sampledPosition: any = null;

onMounted(() => {
  init();
});

const init = async () => {
  viewer = new Cesium.Viewer("containerEarth", {
    infoBox: false,
    shouldAnimate: true,
    baseLayerPicker: false,
    homeButton: true,
    navigationHelpButton: true,
    timeline: true,
    animation: true,
    imageryProvider: new Cesium.ArcGisMapServerImageryProvider({
      url: "https://services.arcgisonline.com/arcgis/rest/services/World_Imagery/MapServer",
    }),
    sceneMode: Cesium.SceneMode.SCENE3D,
    scene3DOnly: true,
  });

  viewer.scene.sun.show = true;
  viewer.scene.moon.show = true;
  viewer.scene.skyBox.show = true;
  viewer.scene.backgroundColor = new Cesium.Color(0.0, 0.0, 0.0, 0.0);
  viewer._cesiumWidget._creditContainer.style.display = "none";
  

  viewer.camera.setView({
    destination: Cesium.Cartesian3.fromDegrees(77.10002952485256, 28.558216772973513, 12000),
    orientation: {
      heading: Cesium.Math.toRadians(0),
      pitch: Cesium.Math.toRadians(-90),
      roll: 0.0,
    },
  });

  Cesium.Camera.DEFAULT_VIEW_RECTANGLE = Cesium.Rectangle.fromDegrees(68.7, 6.5, 97.25, 37.1);

  for (const key in geoCoordMap) {
    addPointPosition(geoCoordMap[key], viewer, key);
    geoCoordMapArr.push(geoCoordMap[key]);
  }

  for (let i = 0, newi = 1; i < geoCoordMapArr.length; i++, newi++) {
    if (i % 2 === 1) continue;
    if (newi === geoCoordMapArr.length) break;
    curvePlotting([...geoCoordMapArr[i], ...geoCoordMapArr[newi]], viewer);
  }

  sampledPosition = new Cesium.SampledPositionProperty();
};

const startAnimation = () => {
  const startTime = Cesium.JulianDate.now();
  const speed = 300;

  geoCoordMapArr.forEach((coord, index) => {
    const time = Cesium.JulianDate.addSeconds(startTime, index * speed, new Cesium.JulianDate());
    const position = Cesium.Cartesian3.fromDegrees(coord[0], coord[1], 200000);
    sampledPosition.addSample(time, position);
  });

  uavEntity = viewer.entities.add({
    name: "UAV Model",
    position: sampledPosition,
    orientation: new Cesium.VelocityOrientationProperty(sampledPosition),
    model: {
      uri: "/models/Cesium_Air.glb",
      scale: 1.0,
      minimumPixelSize: 64,
    },
  });

  const stopTime = Cesium.JulianDate.addSeconds(startTime, geoCoordMapArr.length * speed, new Cesium.JulianDate());
  viewer.clock.startTime = startTime.clone();
  viewer.clock.stopTime = stopTime.clone();
  viewer.clock.currentTime = startTime.clone();
  viewer.clock.clockRange = Cesium.ClockRange.LOOP_STOP;
  viewer.clock.multiplier = 1;

  viewer.trackedEntity = uavEntity;
};

const addPointPosition = (longitudeAndLatitude: number[], viewer, name) => {
  viewer.entities.add({
    position: Cesium.Cartesian3.fromDegrees(...longitudeAndLatitude),
    point: {
      color: Cesium.Color.WHITE,
      pixelSize: 15,
    },
    label: {
      text: name,
      font: '20pt Source Han Sans CN',
      fillColor: Cesium.Color.WHITE,
      showBackground: false,
      style: Cesium.LabelStyle.FILL,
      verticalOrigin: Cesium.VerticalOrigin.CENTER,
      horizontalOrigin: Cesium.HorizontalOrigin.LEFT,
      pixelOffset: new Cesium.Cartesian2(20, 0),
    },
  });
};
</script>

<style>
#containerEarth {
  width: 100%;
  height: 90vh;
  margin: 0;
  padding: 0;
}
button {
  position: absolute;
  top: 150px;
  left: 10px;
  z-index: 1;
  background-color: #0078D4;
  color: white;
  border: none;
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
}
button:hover {
  background-color: #005a9e;
}
</style>
