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
const url1 = require('./assets/textures/door/color.jpg')
const url2 = require('./assets/textures/door/alpha.jpg')
const url3 = require('./assets/textures/door/ambientOcclusion.jpg')
const doorColorTexture = textureLoader.load(url1);
const doorAplhaTexture  = textureLoader.load(url2);
const doorAoTexture   = textureLoader.load(url3);

//导入置换贴图
const url4 = require('./assets/textures/door/height.jpg')
const doorHeightTexture = textureLoader.load(url4);

// 导入粗糙度贴图
const url5 = require('./assets/textures/door/roughness.jpg')
const roughnessTexture  = textureLoader.load(url5);
// 导入金属贴图
const url6 = require('./assets/textures/door/metalness.jpg')
const metalnessTexture = textureLoader.load(url6);

// 导入法线贴图
const url7 = require('./assets/textures/door/normal.jpg')
const normalTexture = textureLoader.load(url7);

// const url2 = require('./assets/textures/minecraft.png')
// const texture = textureLoader.load(url2)
// const texture = textureLoader.load("./textures/minecraft.png");
// 设置纹理偏移
// doorColorTexture.offset.set(0.5, 0.5)
// 纹理旋转
// doorColorTexture.rotation = Math.PI / 4
// 纹理重复
// 水平2次 垂直3次
// doorColorTexture.repeat.set(2, 3)
// // 设置重复的模式
// doorColorTexture.wrapS = THREE.MirroredRepeatWrapping
// doorColorTexture.wrapT = THREE.RepeatWrapping
// texture纹理显示设置
// texture.minFilter = THREE.NearestFilter;
// texture.magFilter = THREE.NearestFilter;
// texture.minFilter = THREE.LinearFilter;
// texture.magFilter = THREE.LinearFilter;
// 添加物体
const cubeGeometry = new THREE.BoxGeometry(1, 1, 1, 100, 100, 100);
// 材质
const material  = new THREE.MeshStandardMaterial({
  color: "#ffff00",
  map: doorColorTexture,
  alphaMap: doorAplhaTexture,
  transparent: true,
  aoMap: doorAoTexture,
  aoMapIntensity: 1,
  displacementMap: doorHeightTexture,
  displacementScale: 0.1,
  roughness: 1,
  roughnessMap: roughnessTexture,
  metalness: 1,
  metalnessMap: metalnessTexture,
  normalMap: normalTexture,
  // opacity: 0.2
});
material .side = THREE.DoubleSide;


// 添加平面
const planeGeometry = new THREE.PlaneBufferGeometry(1, 1, 200, 200);
const plane = new THREE.Mesh(planeGeometry, material);
plane.position.set(1.5, 0, 0);

scene.add(plane);

planeGeometry.setAttribute(
  "uv2",
  new THREE.BufferAttribute(planeGeometry.attributes.uv.array, 2)
);

const cube = new THREE.Mesh(cubeGeometry, material);
// 给cube添加第二组uv
cubeGeometry.setAttribute(
  "uv2",
  new THREE.BufferAttribute(cubeGeometry.attributes.uv.array, 2)
);
console.log('cube', cube)
scene.add(cube);
// 灯光
// 环境光
const light = new THREE.AmbientLight(0xffffff, 0.5); // soft white light
scene.add(light);
//直线光源
const directionalLight = new THREE.DirectionalLight(0xffffff, 0.5);
directionalLight.position.set(10, 10, 10);
scene.add(directionalLight);

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
