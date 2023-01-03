<template>
  <div id="colorSelect">
    <span class="select" v-for="(item, index) in colors" :key="index" :style="{ backgroundColor: item }" @click="changeColor(item)"></span>
  </div>
</template>
<script setup>
let colors = ["brown", "gray", "cadetblue"];
const props = defineProps(["car"]);
const changeColor = (color) => {
  let car = props.car;
  for (let item of car) {
    item.color.set(color);
  }
};
</script>
<style scoped>
#colorSelect {
  position: absolute;
  bottom: 150px;
  left: -135px;
  width: 270px;
  margin-left: 50%;
}

.select {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  /* background-color:cadetblue; */
  display: inline-block;
  margin: 0 30px;
  cursor: pointer;
}
</style>
