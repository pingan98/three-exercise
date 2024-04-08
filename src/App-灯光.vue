<template>
	<div id="container">
	</div>
</template>

<script setup>

import * as THREE from 'three';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
// 钩子函数
import { onMounted } from 'vue'

import dat from 'dat.gui'

// 创建控制对象
const controlData = {
  intensity: 0,
  intensityVisable: false,
  light: 0,
  lightVisable: false
}

// 创建实例
const gui = new dat.GUI()
const f = gui.addFolder('配置')

f.add(controlData, "intensityVisable").onChange(function (val) {
  // console.log(val, 'val')
  controlData.intensity = val ? 1 : 0
}).name('添加环境光')

f.add(controlData, "lightVisable").onChange(function (val) {
  // console.log(val, 'val')
  controlData.light = val ? 100 : 0
}).name('点光源')
// 光源的强度
f.add(controlData, "intensity").min(0).max(10).step(1).name('环境光源强度')
// 光源的功率
f.add(controlData, "light").min(0).max(200).step(10).name('点光源强度')


f.domElement.id = "gui"

f.open()

// 创建场景
// 场景能够让你在什么地方，摆放什么东西来交给three.js来渲染，这是你放置物体，灯光和摄像机的地方
const scene = new THREE.Scene()

// 添加背景颜色
scene.background = new THREE.Color(0x666666)

// 创建相机（视角）
const camera = new THREE.PerspectiveCamera()
camera.position.y = 3 // Y轴
camera.position.z = 10  // X轴

// 创建立方体
const geometry = new THREE.BoxGeometry(1, 1, 1)

// 创建材质
const material = new THREE.MeshPhongMaterial({
	color: 0x0099ff,
	shininess: 1000
})

// 创建网格
const cube = new THREE.Mesh(geometry,material)
// 移动物体
cube.position.set(0, 0.5, 0)
// 物体接收光源
cube.receiveShadow = true
// 物体投射光源
cube.castShadow = true




// 添加到场景
scene.add(cube)

// 添加灯光效果
// -----添加环境光
const ambientLight = new THREE.AmbientLight( 0xffffff, 1 ); // 柔和的白光
scene.add( ambientLight );
// -----添加点光源
const pointLight = new THREE.PointLight( 0xffffff, 100, 100 );
pointLight.position.set( 5, 3, 5 );
// 是否展示阴影
pointLight.castShadow = true
scene.add( pointLight );

// 创建地面
const meshFloor = new THREE.Mesh(
	new THREE.PlaneGeometry(10,10),
	new THREE.MeshPhongMaterial({
		color: 0x1b5120
	})
)
// 调整物体位置
meshFloor.rotation.x -= Math.PI / 2
// 地面设置接收光源的属性
meshFloor.receiveShadow = true

scene.add( meshFloor );



// 添加坐标辅助线
const axesHelper = new THREE.AxesHelper( 5 );
scene.add( axesHelper );


onMounted(() => {

	// 创建渲染器
	const renderer = new THREE.WebGLRenderer()
	//
	// 调整渲染器窗口大小
	renderer.setSize(window.innerWidth, window.innerHeight)
	// 阴影渲染
	renderer.shadowMap.enabled = true
	// 将gui 添加到页面
	document.getElementById('container').appendChild(f.domElement)

	document.getElementById('container').appendChild(renderer.domElement)
	// 添加轨道控制器
	const controls = new OrbitControls(camera, renderer.domElement);
	// 对轨道控制器改变视角时候进行监听
	controls.addEventListener('change', function () {
		// console.log('触发change')
	})
	// 添加阻尼效果
	controls.enableDamping = true
	controls.dampingFactor = 0.01
	// 自动旋转
	controls.autoRotate = false
	// 速率
	controls.autoRotateSpeed = 0.5

	// 让立方体动起来
	function animate() {
		requestAnimationFrame(animate)
		ambientLight.intensity = controlData.intensity // 环境光
    pointLight.intensity = controlData.light // 点光源
		// 轨道控制器更新
		controls.update();
		// 进行渲染
		renderer.render(scene, camera)
	}
	animate()
})


</script>
<style>
#gui {
    position: absolute;
    right: 0;
    width: 300px;
		top: 0;
  }
</style>