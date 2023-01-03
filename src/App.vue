<template>
  <div id="container" ref="container"></div>
  <colorSelect :car="carMaterial" > </colorSelect>
  <loading :tempo="progress"/>
</template>

<script setup>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import { DRACOLoader } from "three/examples/jsm/loaders/DRACOLoader";
import colorSelect from "./components/colorSelect.vue";
import loading from "./components/loading.vue"

import { ref, onMounted, reactive } from "vue";
const container = ref(null);
let progress = ref(0);
const scene = new THREE.Scene();
scene.background = new THREE.Color("#ccc");
scene.environment = new THREE.Color("#ccc");
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 2000);
camera.position.set(-5, 3, 3);
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setClearColor("#000");
let control = null;

const light1 = new THREE.DirectionalLight("#fff", 1);
light1.position.set(0, 0, 10);
scene.add(light1);
const light2 = new THREE.DirectionalLight("#fff", 1);
light2.position.set(0, 0, -10);
scene.add(light2);
const light3 = new THREE.DirectionalLight("#fff", 1);
light3.position.set(10, 0, 0);
scene.add(light3);
const light4 = new THREE.DirectionalLight("#fff", 1);
light4.position.set(-10, 0, 0);
scene.add(light4);
const light5 = new THREE.DirectionalLight("#fff", 1);
light5.position.set(0, 10, 0);
scene.add(light5);
const light6 = new THREE.DirectionalLight("#fff", 1);
light6.position.set(0, 5, 0);
scene.add(light6);

const grid = new THREE.GridHelper(10, 10);
grid.material.opacity = 0.2;
grid.material.transparent = true;
scene.add(grid);

let event = {};
// 单张纹理图的加载
event.onLoad = function () {
  console.log("图片加载完成");
};
event.onProgress = function (url, num, total) {
  console.log("图片加载完成:", url);
  console.log("图片加载进度:", num);
  console.log("图片总数:", total);
  progress.value = ((num / total) * 100).toFixed(2);
  console.log("加载进度的百分比：", progress.value);
  //div.innerHTML = value;
};
event.onError = function (e) {
  console.log("图片加载出现错误");
  console.log(e);
};

// 设置加载管理器
const loadingManager = new THREE.LoadingManager(event.onLoad, event.onProgress, event.onError);

const gltfLoader = new GLTFLoader(loadingManager);
const dracoLoader = new DRACOLoader();
dracoLoader.setDecoderPath("./draco/gltf/");
gltfLoader.setDRACOLoader(dracoLoader);
let wheels = [];
let carBody, fontCar, hoodCar, glassCar;
const bodyMaterial = new THREE.MeshPhysicalMaterial({
  color: "gray",
  metalness: 1,
  roughness: 0.5,
  clearcoat: 1,
  clearcoatRoughness: 0,
});

const fontMaterial = new THREE.MeshPhysicalMaterial({
  color: "gray",
  metalness: 1,
  roughness: 0.5,
  clearcoat: 1,
  clearcoatRoughness: 0,
});

const hoodMaterial = new THREE.MeshPhysicalMaterial({
  color: "gray",
  metalness: 1,
  roughness: 0.5,
  clearcoat: 1,
  clearcoatRoughness: 0,
});

const wheelsMaterial = new THREE.MeshPhysicalMaterial({
  color: "gray",
  metalness: 1,
  roughness: 0.1,
});

const glassMaterial = new THREE.MeshPhysicalMaterial({
  color: 0xffffff,
  metalness: 0,
  roughness: 0,
  transmission: 1,
  transparent: true,
});
let carMaterial = [bodyMaterial, fontMaterial, hoodMaterial, wheelsMaterial];

gltfLoader.load("./model/bmw01.glb", (gltf) => {
  let bmw = gltf.scene;
  bmw.scale.set(2, 2, 2);
  //console.log(bmw);
  bmw.traverse((child) => {
    if (child.isMesh) {
      //console.log(child.name);
      if (child.name.includes("轮毂")) {
        child.material = wheelsMaterial;
        wheels.push(child);
      }
      if (child.name.includes("Mesh002")) {
        carBody = child;
        carBody.material = bodyMaterial;
      }
      if (child.name.includes("前脸")) {
        fontCar = child;
        fontCar.material = fontMaterial;
      }
      if (child.name.includes("引擎盖_1")) {
        hoodCar = child;
        hoodCar.material = hoodMaterial;
      }
      if (child.name.includes("挡风玻璃")) {
        glassCar = child;
        glassCar.material = glassMaterial;
      }
    }
  });
  scene.add(bmw);
});
const clock = new THREE.Clock();
const render = () => {
  renderer.render(scene, camera);
  control.update();
  let elapsedTim = clock.getElapsedTime() / 2;
  camera.position.x = 8 * Math.sin(elapsedTim);
  camera.position.z = 8 * Math.cos(elapsedTim);
  requestAnimationFrame(render);
};

onMounted(() => {
  control = new OrbitControls(camera, renderer.domElement);
  //control.update();
  control.enabled = false;
  container.value.appendChild(renderer.domElement);
  render();
});

window.addEventListener("resize", () => {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
});
</script>


<style scoped>
#container {
  width: 100vw;
  height: 100vh;
  background-color: aqua;
}
</style>
