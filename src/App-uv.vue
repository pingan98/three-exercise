<template>
	<div id="container">
	</div>
</template>

<script setup>



import * as THREE from 'three';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
// 钩子函数
import { onMounted } from 'vue'


// 创建场景
// 场景能够让你在什么地方，摆放什么东西来交给three.js来渲染，这是你放置物体，灯光和摄像机的地方
const scene = new THREE.Scene()


// 创建相机（视角）
const camera = new THREE.PerspectiveCamera()
camera.position.y = 0 // Y轴
camera.position.z = 2  // X轴

// 创建矩形
const geometry = new THREE.PlaneGeometry(1,1)

// 创建纹理对象
const texture = new  THREE.TextureLoader().load('/textures/texture.jpg');

// 定义uv像素取值范围  左上 右上 左下 右下
const uv = new Float32Array([
	// 0,1,
	// 1,1,
	// 0,0,
	// 1,0
	// 左下
	// 0,0.5,
	// 0.5,0.5,
	// 0,0,
	// 0.5,0
	// 右上
	0.5,1,
	1,1,
	0.5,0.5,
	1,0.5
])
geometry.attributes.uv = new THREE.BufferAttribute(uv,2)

// 创建基础网格材质
const material = new THREE.MeshBasicMaterial({
  map: texture
})

// 创建网格
const cube = new THREE.Mesh(geometry, material)
scene.add(cube)

// 添加坐标辅助线
const axesHelper = new THREE.AxesHelper( 5 );
scene.add( axesHelper );
onMounted(() => {

	// 创建渲染器
	const renderer = new THREE.WebGLRenderer()
	//
	// 调整渲染器窗口大小
	renderer.setSize(window.innerWidth, window.innerHeight)

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
		// 轨道控制器更新
		// controls.update();
		// 进行渲染
		renderer.render(scene, camera)
	}
	animate()
})


</script>

