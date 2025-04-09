<script setup>
import { ref, onMounted } from 'vue';
import {
  Cartesian3,
  Math as CesiumMath,
  Terrain,
  Viewer,
  createOsmBuildingsAsync,
} from "cesium";
import 'cesium/Build/Cesium/Widgets/widgets.css'; // Cesium 위젯 스타일

onMounted(() => {
    const viewer = new Viewer("cesiumContainer", {
      terrain: Terrain.fromWorldTerrain(),
      // 기본 UI 버튼 비활성화
      geocoder: false, // 지오코더 (검색창)
      homeButton: false, // 홈 버튼
      sceneModePicker: false, // 2D/3D 모드 선택
      baseLayerPicker: false, // 기본 레이어 선택
      navigationHelpButton: false, // 도움말 버튼
      animation: false, // 애니메이션 컨트롤러
      timeline: false, // 타임라인
      fullscreenButton: false, // 전체 화면 버튼
      vrButton: false, // VR 버튼 (있는 경우)
      infoBox: false, // 정보 박스 (엔티티 클릭 시)
      selectionIndicator: false, // 선택 표시
      projectionPicker: false, // 투영 방식 선택

      // Cesium 로고 비활성화
      creditContainer: undefined, // 또는 null
    });

    viewer.camera.flyTo({
      destination: Cartesian3.fromDegrees(127.161047, 37.398102, 400),
      orientation: {
        heading: CesiumMath.toRadians(0.0),
        pitch: CesiumMath.toRadians(-15.0),
      },
    });

// Add Cesium OSM Buildings, a global 3D buildings layer.
    createOsmBuildingsAsync().then((buildingTileset) => {
      viewer.scene.primitives.add(buildingTileset);
    });

    //const viewer = new Viewer(cesiumContainer.value, {
      // Cesium Viewer 옵션 설정 (선택 사항)
      // 예를 들어, 기본 imageryProvider를 변경하거나 특정 카메라 위치를 설정할 수 있습니다.
      // imageryProvider: new Cesium.TileMapServiceImageryProvider({
      //   url: Cesium.buildModuleUrl('Assets/Textures/NaturalEarthII'),
      // }),
      // camera: {
      //   position: Cesium.Cartesian3.fromDegrees(127.024612, 37.532600, 1500.0),
      //   heading: Cesium.Math.toRadians(0.0),
      //   pitch: Cesium.Math.toRadians(-90.0),
      //   roll: 0.0,
      // },
    //});

    // 필요에 따라 Cesium Viewer 인스턴스를 사용하여 추가적인 지도 조작을 수행할 수 있습니다.
    // 예: viewer.entities.add({ ... });
});
</script>

<template>
  <div id="cesiumContainer" class="no-margin-padding"></div>
</template>

<style scoped>

#cesiumContainer {
  background: gray;
  height: 100vh;
  //width: 100vw;
}
</style>