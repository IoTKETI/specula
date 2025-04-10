<script setup>
import { ref, onMounted } from 'vue';

import {
  Cartesian3,
  Math as CesiumMath,
  Terrain,
  Viewer,
  createOsmBuildingsAsync,
  Color, BillboardGraphics, PolylineGraphics, VerticalOrigin
} from "cesium";
import 'cesium/Build/Cesium/Widgets/widgets.css';
import * as Cesium from "cesium"; // Cesium 위젯 스타일

import eventBus from '@/utils/eventBus';

let viewer;
let entity;
//====================== 카메라 시점 변경 부분==========================
// 상공에서 바라보기
function lookSkyview(){
  var transform = Cesium.Transforms.eastNorthUpToFixedFrame(Cesium.Cartesian3.fromDegrees(126.924403, 37.524624));
  var camera = viewer.camera;

  camera.constraintedAxis = Cesium.Cartesian3.UNIT_Z;
  camera.lookAtTransform(transform, new Cesium.Cartesian3(-10000.0, -10000.0, 25000.0));

  viewer.trackedEntity = undefined;
  viewer.zoomTo(viewer.entities, new Cesium.HeadingPitchRange(0, Cesium.Math.toRadians(-90)));
}

// 측면에서 바라보기
function lookSideview(){
  viewer.trackedEntity = undefined;
  viewer.zoomTo(viewer.entities, new Cesium.HeadingPitchRange(Cesium.Math.toRadians(-90),
    Cesium.Math.toRadians(-15), 8000));
}

// 항공기 바라보기
function lookAirport(){
  viewer.trackedEntity = entity;
}

onMounted(() => {
  eventBus.on('do-lookSkyview', lookSkyview);
  eventBus.on('do-lookSideview', lookSideview);
  eventBus.on('do-lookAirport', lookAirport);


//   let extent = Cesium.Rectangle.fromDegrees(117.896284, 31.499028,139.597380,43.311528);
//
//   Cesium.Camera.DEFAULT_VIEW_RECTANGLE=extent;
//   Cesium.Camera.DEFAULT_VIEW_FACTOR=0;
//
//   var viewer = new Cesium.Viewer('cesiumContainer',{
//     timeline:false,
//     selectionIndicator:false,
//     navigationHelpButton:false,
//     infoBox:false,
//     navigationInstructionInitiallyVisible:false,
//     baseLayerPicker:true,
//     shouldAnimate:true,//animation가능
//     terrainProvider:Cesium.createWorldTerrain()//기본지도를지형지도로셋팅
//   });
//
//   viewer.scene.globe.enableLighting=true;
//
//   viewer.scene.globe.depthTestAgainstTerrain=true;//지형생성관련
//
//   Cesium.Math.setRandomNumberSeed(3);
//
// // 시작, 종료 날짜 설정
//   var start = Cesium.JulianDate.fromDate(new Date(2019, 2, 8, 10));
//   var stop = Cesium.JulianDate.addSeconds(start, 360, new Cesium.JulianDate());
//
// // viewer 작동 시간 확인
//   viewer.clock.startTime = start.clone();
//   viewer.clock.stopTime = stop.clone();
//   viewer.clock.currentTime = start.clone();
//   viewer.clock.clockRange = Cesium.ClockRange.LOOP_STOP; // loop 종료
//   viewer.clock.multiplier = 10;
//
// // 항공기 비행 구역 설정 
//   function computeCircularFlight(lon, lat, radius){
//     var property = new Cesium.SampledPositionProperty();
//
//     for(var i=0; i<=360; i+=45){
//       var radians = Cesium.Math.toRadians(i); // 범위 설정
//       var time = Cesium.JulianDate.addSeconds(start, i, new Cesium.JulianDate()); // 시간 설정
//       var position = Cesium.Cartesian3.fromDegrees(lon + (radius * 1.5 * Math.cos(radians)), // 비행구역 위치 설정 
//         lat + (radius * Math.sin(radians)),
//         Cesium.Math.nextRandomNumber() * 500+1000);
//       property.addSample(time, position);
//       // 비행구역 구분점 표시
//       viewer.entities.add({
//         position : position, // position = 비행구역 위치
//         point : {
//           pixelSize : 8,
//           color : Cesium.Color.YELLOW,
//           outlinecolor : Cesium.Color.YELLOW,
//           outlineWidth : 3
//         }
//       });
//     }
//     return property;
//   }
//
//   var position = computeCircularFlight(126.924403, 37.524624, 0.03); // 비행구역 중간점 설정
//
//   var entity = viewer.entities.add({availability : new Cesium.TimeIntervalCollection([new Cesium.TimeInterval({
//       start : start,
//       stop : stop
//     })]),
//
//     position : position,
//
//     orientation : new Cesium.VelocityOrientationProperty(position),
//     model : {
//       uri : "js/Cesium-1.53/Apps/SampleData/models/CesiumAir/Cesium_Air.gltf",
//       minimumPixelSize : 64
//     },
//
//     path : {
//       resolution : 1,
//       material : new Cesium.PolylineGlowMaterialProperty({
//         glowPower : 0.1,
//         color : Cesium.Color.YELLOW
//       }),
//       width : 10
//     }
//   });
//
// // ====================== 카메라 시점 변경 부분========================== 
// // 상공에서 바라보기
//   function lookSkyview(){
//     var transform = Cesium.Transforms.eastNorthUpToFixedFrame(Cesium.Cartesian3.fromDegrees(126.924403, 37.524624));
//     var camera = viewer.camera;
//
//     camera.constraintedAxis = Cesium.Cartesian3.UNIT_Z;
//     camera.lookAtTransform(transform, new Cesium.Cartesian3(-10000.0, -10000.0, 25000.0));
//
//     viewer.trackedEntity = undefined;
//     viewer.zoomTo(viewer.entities, new Cesium.HeadingPitchRange(0, Cesium.Math.toRadians(-90)));
//   }
//
// // 측면에서 바라보기
//   function lookSideview(){
//     viewer.trackedEntity = undefined;
//     viewer.zoomTo(viewer.entities, new Cesium.HeadingPitchRange(Cesium.Math.toRadians(-90),
//       Cesium.Math.toRadians(-15), 8000));
//   }
//
// // 항공기 바라보기
//   function lookAirport(){
//     viewer.trackedEntity = entity;
//   }

  var extent = Cesium.Rectangle.fromDegrees(117.896284, 31.499028, 139.597380, 43.311528);

  Cesium.Camera.DEFAULT_VIEW_RECTANGLE=extent;
  Cesium.Camera.DEFAULT_VIEW_FACTOR=0;

  viewer = new Viewer("cesiumContainer", {
    terrain: Terrain.fromWorldTerrain(),
    // 기본 UI 버튼 비활성화
    geocoder: false, // 지오코더 (검색창)
    homeButton: false, // 홈 버튼
    sceneModePicker: false, // 2D/3D 모드 선택
    baseLayerPicker: true, // 기본 레이어 선택
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

  viewer.scene.globe.enableLighting=true;
  viewer.scene.globe.depthTestAgainstTerrain=true;//지형생성관련

  Cesium.Math.setRandomNumberSeed(3);

  var start = Cesium.JulianDate.fromDate(new Date(2025, 4, 9, 10));
  var stop = Cesium.JulianDate.addSeconds(start, 360, new Cesium.JulianDate());

  viewer.clock.startTime = start.clone();
  viewer.clock.stopTime = stop.clone();
  viewer.clock.currentTime = start.clone();
  viewer.clock.clockRange = Cesium.ClockRange.LOOP_STOP; // loop 종료
  viewer.clock.multiplier = 10;

  function computeCircularFlight(lon, lat, radius){
    var property = new Cesium.SampledPositionProperty();

    for(var i=0; i<=360; i+=45){
      var radians = Cesium.Math.toRadians(i); // 범위 설정
      var time = Cesium.JulianDate.addSeconds(start, i, new Cesium.JulianDate()); // 시간 설정
      var position = Cesium.Cartesian3.fromDegrees(lon + (radius * 1.5 * Math.cos(radians)), // 비행구역 위치 설정
        lat + (radius * Math.sin(radians)),
        Cesium.Math.nextRandomNumber() * 500+1000);
      property.addSample(time, position);
      // 비행구역 구분점 표시
      viewer.entities.add({
        position : position, // position = 비행구역 위치
        point : {
          pixelSize : 8,
          color : Cesium.Color.YELLOW,
          outlinecolor : Cesium.Color.YELLOW,
          outlineWidth : 3
        }
      });
    }
    return property;
  }

  var position = computeCircularFlight(126.924403, 37.524624, 0.03); // 비행구역 중간점 설정

  entity = viewer.entities.add({availability : new Cesium.TimeIntervalCollection([new Cesium.TimeInterval({
      start : start,
      stop : stop
    })]),

    position : position,

    orientation : new Cesium.VelocityOrientationProperty(position),
    model : {
      uri : "/node_modules/cesium/Build/Cesium/Assets/Models/CesiumAir/Cesium_Air.gltf",
      minimumPixelSize : 64
    },

    path : {
      resolution : 1,
      material : new Cesium.PolylineGlowMaterialProperty({
        glowPower : 0.1,
        color : Cesium.Color.YELLOW
      }),
      width : 10
    }
  });



  // 마커를 표시할 위도, 경도, 지상 높이 (예시: 서울 N서울타워)
  const latitude = 37.5512;
  const longitude = 126.9882;
  const groundAltitude = 0; // 초기 지상 높이 (정확한 높이는 TerrainProvider에 따라 다름)
  const markerHeightAboveGround = 50; // 마커를 지상에서 50미터 위에 표시

  // 마커의 최종 위치 (지상 높이 + 50미터)
  const markerPosition = Cartesian3.fromDegrees(
    longitude,
    latitude,
    groundAltitude + markerHeightAboveGround
  );

  // // 지면에 닿는 위치 (같은 위도, 경도, 고도 0)
  // const groundPosition = Cartesian3.fromDegrees(
  //   longitude,
  //   latitude,
  //   groundAltitude
  // );

  // 마커 엔티티 생성
  // viewer.entities.add({
  //   position: markerPosition,
  //   billboard: new BillboardGraphics({
  //     image: require('./assets/marker-icon.png'), // 사용자 정의 마커 이미지 경로 (assets 폴더에 marker-icon.png 파일이 있다고 가정)
  //     verticalOrigin: VerticalOrigin.BOTTOM, // 마커 하단이 위치에 오도록 설정
  //     heightReference: 1, // HeightReference.RELATIVE_TO_GROUND (지면 기준 상대 높이)
  //     heightOffset: markerHeightAboveGround, // 지면에서 50미터 위
  //   }),
  // });

  // 수직선 엔티티 생성
  // viewer.entities.add({
  //   polyline: new PolylineGraphics({
  //     positions: [markerPosition, groundPosition],
  //     material: Color.RED,
  //     width: 2,
  //     clampToGround: true, // 선이 지면을 따라가도록 설정
  //   }),
  // });

  // 카메라 이동 (선택 사항)
  // viewer.camera.flyTo({
  //   destination: Cartesian3.fromDegrees(longitude + 0.005, latitude + 0.005, 200),
  //   orientation: {
  //     heading: Math.toRadians(0.0),
  //     pitch: Math.toRadians(-90.0),
  //     roll: 0.0,
  //   },
  // });

  // viewer.camera.flyTo({
  //   // destination: Cartesian3.fromDegrees(127.161047, 37.398102, 400),
  //   destination: Cartesian3.fromDegrees(longitude + 0.005, latitude + 0.005, 200),
  //   orientation: {
  //     heading: CesiumMath.toRadians(0.0),
  //     pitch: CesiumMath.toRadians(-15.0),
  //   },
  // });

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