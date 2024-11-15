<template>
  <div style="width: 100%; height: 100vh;" id="container"></div>
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue';
import * as THREE from "three";
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';

let scene: THREE.Scene;
let renderer: THREE.WebGLRenderer;
let camera: THREE.PerspectiveCamera;
let controls: OrbitControls;

const dom = ref();

onMounted(() => {
  init();
  renderScene();
  window.addEventListener('resize', onWindowResize);
});

const init = () => {
  dom.value = document.getElementById('container')!;
  
  // Scene
  scene = new THREE.Scene();
  
  // Renderer
  renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(dom.value.offsetWidth, dom.value.offsetHeight);
  dom.value.appendChild(renderer.domElement);

  // Camera
  camera = new THREE.PerspectiveCamera(45, dom.value.offsetWidth / dom.value.offsetHeight, 0.1, 1000);
  camera.position.set(0, 1, 5); // Default camera position
  
  // OrbitControls
  controls = new OrbitControls(camera, renderer.domElement);
};

const renderScene = () => {
  requestAnimationFrame(renderScene);
  renderer.render(scene, camera);
  controls.update();
};

const onWindowResize = () => {
  camera.aspect = dom.value.offsetWidth / dom.value.offsetHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(dom.value.offsetWidth, dom.value.offsetHeight);
};
</script>

<style></style>
