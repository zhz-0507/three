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
// 目标：运用数学知识设计特定形状的星系

// 1、创建场景
const scene = new THREE.Scene();
const gui = new dat.GUI();
// 2、创建相机
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  30
);

// 设置相机位置
camera.position.set(0, 0, 10);
scene.add(camera);

// ******三维空间中计算出鼠标移过了什么物体*******
// 步骤1：先渲染1000个立方体
// 步骤2：设置材质
// 步骤3：创建光线对象
// 步骤4：创建鼠标位置对象
// 步骤5：创建监听对象
// 通过setFromCamera方法去更新射线




const cubeGeometry = new THREE.BoxGeometry(1,1,1)
const material = new THREE.MeshBasicMaterial({
  wireframe: true
})
const redMaterial  = new THREE.MeshBasicMaterial({
  color: '#ff0000'
})

for(let i=-5;i<5;i++) {
  for(let j=-5;j<5;j++) {
    for(let k=-5;k<5;k++){
      const cube = new THREE.Mesh(cubeGeometry,material)
      cube.position.set(i,j,k)
      scene.add(cube)
    }
  }
}

// 创建投影光线对象
const raycaster = new THREE.Raycaster();
// 鼠标的位置对象
const pointer = new THREE.Vector2();
function onMousemove(event) {
  pointer.x = ( event.clientX / window.innerWidth ) * 2 - 1;
	pointer.y = - ( event.clientY / window.innerHeight ) * 2 + 1;

  // 通过摄像机和鼠标位置更新射线
  raycaster.setFromCamera(pointer, camera)
  // 计算物体和射线的焦点
  const children = scene.children
  let result = raycaster.intersectObjects(children)
  result.forEach((item) => {
    item.object.material = redMaterial
  })
}

window.addEventListener( 'mousemove', onMousemove );

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
