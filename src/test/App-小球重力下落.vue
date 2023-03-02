<template>
  <div>
  
  </div>
</template>

<script setup>
import * as THREE from "three";
// 导入轨道控制器
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
// 导入动画库
import gsap from "gsap";
// 导入dat.gui
import * as dat from "dat.gui";
 // 引入cannon-es
 import * as CANNON from 'cannon-es'
// 目标：raycaster

// const gui = new dat.GUI();
// 1、创建场景
const scene = new THREE.Scene();

// 2、创建相机
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  300
);

// 设置相机位置
camera.position.set(0, 0, 18);
scene.add(camera);

// 创建小球
const sphereGeometry = new THREE.SphereGeometry(1, 20, 20)
const material = new THREE.MeshStandardMaterial()
const sphere= new THREE.Mesh(sphereGeometry, material)
sphere.castShadow = true
scene.add(sphere)

// 创建平面
const floor = new THREE.Mesh(
  new THREE.PlaneBufferGeometry(30, 30), 
  new THREE.MeshStandardMaterial()
)
floor.position.set(0, -5, 0);
floor.rotation.x = -Math.PI / 2;
floor.receiveShadow = true
scene.add(floor)

// ****开始创建物理世界****
const world = new CANNON.World();
world.gravity.set(0, -9.8, 0);
// 创建物理小球形状
const sphereShape = new CANNON.Sphere(1);

//设置物体材质
const sphereWorldMaterial = new CANNON.Material('sphere');

// 创建物理世界的物体
const sphereBody = new CANNON.Body({
  shape: sphereShape,
  position: new CANNON.Vec3(0, 0, 0),
  //   小球质量
  mass: 1,
  //   物体材质
  material: sphereWorldMaterial,
});

// 将物体添加至物理世界
world.addBody(sphereBody);

// 创建物理世界的地面
const floorShape = new CANNON.Plane()
const floorBody = new CANNON.Body()
const floorMaterial = new CANNON.Material("floor")
floorBody.material = floorMaterial;
// 当质量为0时让物体保持不动
floorBody.mess = 0
floorBody.addShape(floorShape)
// 地面位置
floorBody.position.set(0, -5, 0);
// 旋转地面的位置
floorBody.quaternion.setFromAxisAngle(new CANNON.Vec3(1, 0, 0), -Math.PI / 2);
world.addBody(floorBody);

// 设置两种材质碰撞的参数
const defaultContactMaterial = new CANNON.ContactMaterial(
  sphereWorldMaterial,
  floorMaterial,
  {
    // 摩擦力
    friction: 0.1,
    // 弹性
    restitution: 0.7
  }
)
// 将材料的关联设置添加到物理世界
world.addContactMaterial(defaultContactMaterial)

// 监听碰撞事件
function HitEvent(e) {
  // 获取碰撞的强度
  const impactStrength = e.contact.getImpactVelocityAlongNormal();
  console.log("碰撞的强度", impactStrength)
}
sphereBody.addEventListener("collide", HitEvent);
// 添加环境光和平行光
const ambientLight = new THREE.AmbientLight(0xffffff, 0.5)
scene.add(ambientLight)
const directionalLight = new THREE.DirectionalLight(0xffffff, 0.5)
directionalLight.castShadow = true
scene.add(directionalLight)


// 初始化渲染器
// 渲染器透明
const renderer = new THREE.WebGLRenderer({ alpha: true });
// 设置渲染的尺寸大小
renderer.setSize(window.innerWidth, window.innerHeight);
// 开启场景中的阴影贴图
renderer.shadowMap.enabled = true;
// renderer.physicallyCorrectLights = true;

// console.log(renderer);
// 将webgl渲染的canvas内容添加到body
document.body.appendChild(renderer.domElement);

// // 使用渲染器，通过相机将场景渲染进来
// renderer.render(scene, camera);

// 创建轨道控制器
const controls = new OrbitControls(camera, renderer.domElement);
// // 设置控制器阻尼，让控制器更有真实效果,必须在动画循环里调用.update()。
controls.enableDamping = true;

// 添加坐标轴辅助器
const axesHelper = new THREE.AxesHelper(5);
scene.add(axesHelper);
// 设置时钟
const clock = new THREE.Clock();

function render() {
  // let time = clock.getElapsedTime();
  let deltaTime = clock.getDelta();
  // 更新物理引擎里世界的物体
  world.step(1 / 120, deltaTime);

  sphere.position.copy(sphereBody.position);
  // controls.update();
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
* {
  margin: 0;
  padding: 0;
}
body {
  background-color: rgb(36, 58, 66);
}
canvas {
  position: fixed;
  left: 0;
  top: 0;
}
.page {
  height: 100vh;
  display: flex;
  color: #fff;
  flex-direction: column;
  /* justify-content: center; */
  align-items: center;
  position: relative;
  z-index: 10;
}
.page h1 {
  margin: 60px;
  font-size: 40px;
}

.page h3 {
  font-size: 30px;
}
/* ::-webkit-scrollbar {
  display: none;
} */
</style>



