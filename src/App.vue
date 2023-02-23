<template>
  <div></div>
</template>

<script setup>
import * as THREE from 'three';
// 导入轨道控制器
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'

// 1.创建场景
const scene = new THREE.Scene();

// 2.创建相机
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
)

// 设置相机位置
camera.position.set(0,0,10)
scene.add(camera)

// 添加物体
// 创建几何体
const cubeGeometry = new THREE.BoxGeometry(1,1,1)
const cubeMaterial = new THREE.MeshBasicMaterial({color: 0xffff00})
// 根据几何体和材质创建物体
const cube = new THREE.Mesh(cubeGeometry, cubeMaterial)
// 把几何体添加在场景中
scene.add(cube)

// 初始化渲染器
const renderer = new THREE.WebGLRenderer()
// 设置渲染的尺寸大小
renderer.setSize(window.innerWidth, window.innerHeight)
// 把webgl渲染的canvas添加到body中
document.body.appendChild(renderer.domElement)
// 使用渲染器，通过相机将场景渲染出来
renderer.render(scene, camera)

// 创建轨道控制器
const controls = new OrbitControls(camera, renderer.domElement)
function render() {
  renderer.render(scene, camera)
  requestAnimationFrame(render)
}

render()

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
