<template>
  <div></div>
</template>

<script setup>
import * as THREE from "three";
// 导入轨道控制器
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
// 导入动画库
import gsap from "gsap";
// 导入dat.gui
import * as dat from "dat.gui";
import { RGBELoader } from "three/examples/jsm/loaders/RGBELoader";
// 目标：掌握轻量级图形界面

// 1、创建场景
const scene = new THREE.Scene();
const gui = new dat.GUI();
// 2、创建相机
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  40
);

// 设置相机位置
camera.position.set(0, 0, 10);
scene.add(camera);


function createPoint(url, size = 0.5) {
  const particlesGeometry = new THREE.BufferGeometry();
  const count = 5000;

  // 设置缓冲区数组
  const positions = new Float32Array(count * 3);
  // 设置粒子顶点颜色
  const colors = new Float32Array(count * 3);
  // 设置顶点
  for (let i = 0; i < count * 3; i++) {
    positions[i] = (Math.random() - 0.5) * 100;
    colors[i] = Math.random();
  }
  particlesGeometry.setAttribute(
    "position",
    new THREE.BufferAttribute(positions, 3)
  );
  particlesGeometry.setAttribute("color", new THREE.BufferAttribute(colors, 3));

  // 设置点材质
  const pointsMaterial = new THREE.PointsMaterial();
  pointsMaterial.size = size;
  pointsMaterial.color.set(0xfff000);
  // 相机深度而衰减
  pointsMaterial.sizeAttenuation = true;

  // 载入纹理
  const textureLoader = new THREE.TextureLoader();

  const texture = textureLoader.load(url);
  // 设置点材质纹理
  pointsMaterial.map = texture;
  pointsMaterial.alphaMap = texture;
  pointsMaterial.transparent = true;
  pointsMaterial.depthWrite = false;
  pointsMaterial.blending = THREE.AdditiveBlending;
  // 设置启动顶点颜色
  pointsMaterial.vertexColors = true;

  const points = new THREE.Points(particlesGeometry, pointsMaterial);

  scene.add(points);
  return points
}

const url1 = require('./assets/textures/particles/11.png')
const url2 = require('./assets/textures/particles/3.png')
const url3 = require('./assets/textures/particles/4.png')
const point1 = createPoint(url1, 1.5)
const point2 = createPoint(url2, 1.5)
const point3 = createPoint(url3, 1.5)

// 初始化渲染器
const renderer = new THREE.WebGLRenderer();
// 设置渲染的尺寸大小
renderer.setSize(window.innerWidth, window.innerHeight);
// 开启场景中的阴影贴图
renderer.shadowMap.enabled = true
// console.log(renderer);
// 将webgl渲染的canvas内容添加到body
document.body.appendChild(renderer.domElement);

// // 使用渲染器，通过相机将场景渲染进来
// renderer.render(scene, camera);

// 创建轨道控制器
const controls = new OrbitControls(camera, renderer.domElement);
// 设置控制器阻尼，让控制器更有真实效果,必须在动画循环里调用.update()。
controls.enableDamping = true;

// 添加坐标轴辅助器
const axesHelper = new THREE.AxesHelper(5)
scene.add(axesHelper);

const clock = new THREE.Clock();
function render() {
  let time = clock.getElapsedTime();
  point1.rotation.x = time * 0.3;
  point2.rotation.x = time * 0.3;
  point3.rotation.x = time * 0.3;
  controls.update();
  renderer.render(scene, camera);
  //   渲染下一帧的时候就会调用render函数
  requestAnimationFrame(render);
}

render();

// 监听画面变化，更新渲染画面
window.addEventListener("resize", () => {
  //   console.log("画面变化了");
  // 更新摄像头
  camera.aspect = window.innerWidth / window.innerHeight;
  //   更新摄像机的投影矩阵
  camera.updateProjectionMatrix();

  //   更新渲染器
  renderer.setSize(window.innerWidth, window.innerHeight);
  //   设置渲染器的像素比
  renderer.setPixelRatio(window.devicePixelRatio);
});




</script>

<style>
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
canvas{
  width: 100vw;
  height: 100vh;
  position: fixed;
  top: 0;
  left: 0;
}
</style>
