<script setup>
import { ref } from 'vue';
import ChangesDetails from './components/ChangesDetails.vue';
import HeatmapChart from './components/HeatmapChart.vue';

const selectDate = ref(null)


const onSelectDate = (date) => {
  selectDate.value = date
}
</script>

<template>
  <HeatmapChart @onselect-date="onSelectDate" />
  <ChangesDetails :selectDate="selectDate" />
</template>



<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
