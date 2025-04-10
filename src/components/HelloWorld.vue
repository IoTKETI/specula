<script setup>
defineProps({
  msg: {
    type: String,
    required: true,
  },
})

import eventBus from '@/utils/eventBus'; // eventBus.js 파일 경로에 따라 수정

const emitLookSkyview = () => {
  eventBus.emit('do-lookSkyview', { text: 'lookSkyview' });
};

const emitLookSideview = () => {
  eventBus.emit('do-lookSideview', { text: 'lookSideview' });
};

const emitLookAirport = () => {
  eventBus.emit('do-lookAirport', { text: 'lookAirport' });
};

</script>

<template>
  <div id="toolbar">
    <h5>항공기 관점 변경하기</h5>
    <div id="camcontrol">
      <button @click="emitLookSkyview">상공에서 바라보기</button>
      <button @click="emitLookSideview">측면에서 바라보기</button>
      <button @click="emitLookAirport">항공기 바라보기</button>
    </div>
  </div>
</template>

<style scoped>
#toolbar {
  padding: 1px;
  border-redius: 4px;
}

#toolbar input{
  vertical-align:middle;
  padding-top:2px;
  padding-bottom:2px;
}

#toolbar.header{
  font-weight:bold;
}
</style>
