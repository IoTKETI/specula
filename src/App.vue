<script setup>
import CesiumMap from './components/CesiumMap.vue';
import HelloWorld from "@/components/HelloWorld.vue";
</script>

<template>
  <div class="container">
    <div class="left-panel">
      <HelloWorld msg="Hello"/>
    </div>
    <div class="right-panel">
      <CesiumMap />
    </div>
  </div>
</template>


<style scoped>
.container {
  display: grid;
  grid-template-columns: 0.4fr 0.6fr;
  //grid-template-columns: repeat(auto-fill, minmax(25%, auto));
  //grid-template-rows: repeat(5, minmax(50px,auto));
  grid-auto-flow: dense;
}

.left-panel {
  background-color: yellowgreen;
  margin: -8px;
}

.right-panel {
  background-color: dodgerblue;
  margin: -8px;
}
</style>
