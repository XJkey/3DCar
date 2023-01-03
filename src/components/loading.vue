<template>
  <div id="loading" v-if="show">
    <ve-progress :progress="tempo" style="display: inline" />
  </div>
</template>

<script setup>
import { ref } from "vue";
import { watchEffect } from "@vue/runtime-core";
import { VeProgress } from "vue-ellipse-progress";
let show = ref(true);
let props = defineProps(["tempo"]);
watchEffect(() => {
  if (props.tempo == 100) {
    setTimeout(() => {
      show.value = false;
    }, 1000);
  }
});
</script>

<style>
#loading {
  width: 100vw;
  height: 100vh;
  position: absolute;
  top: 0;
  left: 0;
  background-color: #fff;
  opacity: 0.8;
}
</style>