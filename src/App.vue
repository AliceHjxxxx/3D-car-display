<template>
  <div class="home">
    <div class="container" ref="containerRef"></div>
    <div class="content">
      <div class="content-title">
        <h2>3D Car Model Display</h2>
      </div>
      <h4>Choose The Exterior Color</h4>
      <div class="select">
        <div class="select-item" v-for="(item, index) in color" :key="index" @click="selectColor(item)">
          <div class="select-item-color" :style="{ background: item }"></div>
        </div>

      </div>

      <!-- <h4>选择车衣材质</h4> -->
    </div>

  </div>
</template>
<script setup>
import * as Three from 'three'
import { onMounted, ref } from 'vue';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader'
import { DRACOLoader } from 'three/examples/jsm/loaders/DRACOLoader'

const containerRef = ref(null)
let controls = null
let carBody = null
let frontCar = null
let hoodCar = null
let glassCar = null

const renderer = new Three.WebGLRenderer({
  // 抗锯齿效果
  antialias: true
})
// 创建渲染器
renderer.setSize(window.innerWidth, window.innerHeight)
// 创建镜头

const camera = new Three.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)
// camera.position.z = 0.1
camera.position.set(0, 2, 6)

const scene = new Three.Scene()

// const geometry = new Three.BoxGeometry(1, 1, 1)
// const material = new Three.MeshBasicMaterial({
//   color: 'green'
// })
// const cube = new Three.Mesh(geometry, material)
// scene.add(cube)


let wheels = []
const render = () => {
  renderer.render(scene, camera)
  requestAnimationFrame(render)
}
// 材质
const wheelsMaterial = new Three.MeshPhysicalMaterial({
  color: 0xFFB6C1,
  matalness: 1,
  roughness: 0.5,
  // clearcoat: 1,
  // clearcoatRoughness: 0
})

// 车身的材质

const bodyMaterial = new Three.MeshPhysicalMaterial({
  color: 0xFFB6C1,
  matalness: 1,
  roughness: 0.5,
  clearcoat: 1,
  clearcoatRoughness: 0
})

// 前脸
const frontMaterial = new Three.MeshPhysicalMaterial({
  color: 0xFFB6C1,
  matalness: 1,
  roughness: 0.5,
  clearcoat: 1,
  clearcoatRoughness: 0
})
// 引擎盖
const hoodMaterial = new Three.MeshPhysicalMaterial({
  color: 0xFFB6C1,
  matalness: 1,
  roughness: 0.5,
  clearcoat: 1,
  clearcoatRoughness: 0
})
// 挡风玻璃
const glassMaterial = new Three.MeshPhysicalMaterial({
  color: 0xffffff,
  metalness: 0,
  roughness: 0,
  transmission: 1,
  transparent: true
})

let color = ['#FFB6C1', 'red', 'blue', 'green', 'gray', 'orange', 'purple', 'yellow', 'white']
onMounted(() => {
  containerRef.value.appendChild(renderer.domElement)
  renderer.setClearColor('#000')
  scene.background = new Three.Color('#ddd')
  scene.environment = new Three.Color('#ddd')
  render()

  controls = new OrbitControls(camera, renderer.domElement)
  controls.update()
  // controls.enableDamping = true

  // 添加地面网格
  const gridHelper = new Three.GridHelper(10, 10)
  gridHelper.material.opacity = 0.2
  gridHelper.material.transparent = true
  scene.add(gridHelper)

  // 加载汽车模型
  const loader = new GLTFLoader()
  // 加载参数器
  const dracoLoader = new DRACOLoader()
  dracoLoader.setDecoderPath('./draco/gltf/')
  // 挂在参数
  loader.setDRACOLoader(dracoLoader)
  loader.load('./model/bmw01.glb', (gltf) => {
    const bmw = gltf.scene
    // console.log(bmw)
    // 模型的多个面
    bmw.traverse((child) => {

      if (child.isMesh) {
        console.log(child.name);
      }
      // 轮毂
      if (child.isMesh && child.name.includes('轮毂')) {
        child.material = wheelsMaterial
        wheels.push(child)
      }
      // 车身
      if (child.isMesh && child.name.includes('Mesh002')) {
        carBody = child
        carBody.material = bodyMaterial
      }
      // 前脸
      if (child.isMesh && child.name.includes('前脸')) {
        frontCar = child
        frontCar.material = frontMaterial
      }
      // 引擎盖
      if (child.isMesh && child.name.includes('引擎')) {
        hoodCar = child
        hoodCar.material = hoodMaterial
      }
      if (child.isMesh && child.name.includes('挡风玻璃')) {
        glassCar = child
        glassCar.material = glassMaterial
      }

    })
    scene.add(bmw)
  })

  // 灯光
  const light1 = new Three.DirectionalLight(0xffffff, 1)
  const light2 = new Three.DirectionalLight(0xffffff, 1)
  const light3 = new Three.DirectionalLight(0xffffff, 1)
  const light4 = new Three.DirectionalLight(0xffffff, 1)
  const light5 = new Three.DirectionalLight(0xffffff, 1)
  const light6 = new Three.DirectionalLight(0xffffff, 1)

  const light7 = new Three.DirectionalLight(0xffffff, 1)

  const light8 = new Three.DirectionalLight(0xffffff, 1)

  const light9 = new Three.DirectionalLight(0xffffff, 1)

  // const light10 = new Three.DirectionalLight(0xffffff, 1)



  light1.position.set(0, 0, 10)
  light2.position.set(0, 0, -10)
  light3.position.set(10, 0, 0)
  light4.position.set(-10, 0, 0)
  light5.position.set(0, 10, 0)
  light6.position.set(5, 10, 0)

  light7.position.set(0, 10, 5)

  light8.position.set(0, 10, -5)

  light9.position.set(-5, 10, 0)



  scene.add(light1, light2, light3, light4, light9)

})

const selectColor = (color) => {
  wheelsMaterial.color.set(color)
  frontMaterial.color.set(color)
  bodyMaterial.color.set(color)
  hoodMaterial.color.set(color)


}

</script>
<style>
* {
  margin: 0;
  padding: 0;
}

h2,
h4 {
  color: #fff;
}

.content {
  text-align: center;
  line-height: 40px;
  position: fixed;
  top: 0;
  left: 50%;
  transform: translateX(-50%);
}

.select {
  display: flex;
}

.select-item {
  margin: 10px;
  cursor: pointer;
}

.select-item-color {
  width: 15px;
  height: 15px;
  border: 2px solid #fff;
  border-radius: 10px;
  opacity: 0.8;
}

.select-item-color:hover {
  transform: scale(1.5, 1.5);
}
</style>