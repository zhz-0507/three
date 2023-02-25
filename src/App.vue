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
// 目标：掌握轻量级图形界面

// 1、创建场景
const scene = new THREE.Scene();

// 2、创建相机
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
);

// 设置相机位置
camera.position.set(0, 0, 10);
scene.add(camera);

// 导入纹理
const textureLoader = new THREE.TextureLoader();
const url = require('./assets/textures/door/color.jpg')
const doorColorTexture = textureLoader.load(url);
// 设置纹理偏移
// doorColorTexture.offset.set(0.5, 0.5)
// 纹理旋转
// doorColorTexture.rotation = Math.PI / 4
// 纹理重复
// 水平2次 垂直3次
doorColorTexture.repeat.set(2, 3)
// 设置重复的模式
doorColorTexture.wrapS = THREE.MirroredRepeatWrapping
doorColorTexture.wrapT = THREE.RepeatWrapping
// 添加物体
const cubeGeometry = new THREE.BoxGeometry(1, 1, 1);
// 材质
const basicMaterial = new THREE.MeshBasicMaterial({
  color: "#ffff00",
  map: doorColorTexture,
});
const cube = new THREE.Mesh(cubeGeometry, basicMaterial);
scene.add(cube);


// 初始化渲染器
const renderer = new THREE.WebGLRenderer();
// 设置渲染的尺寸大小
renderer.setSize(window.innerWidth, window.innerHeight);
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

function render() {
  
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
