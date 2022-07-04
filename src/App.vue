
<template>
  <ColorPicker valueMode="rgb" :onChange="changeValue" />
</template>



<script setup lang="ts">

import ColorPicker from './components/ColorPicker.vue'
const changeValue = (value: string) => {
  console.log(value)
}
</script>

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
