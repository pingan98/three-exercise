<template>
	<div id="container">
		<button @click="moveCamera">移动相机</button>
		<button @click="moveCube">移动物体</button>
	</div>
</template>

<script setup>
// import * as THREE from 'three';

// const width = window.innerWidth, height = window.innerHeight;

// // init
//   // 创建相机（视角）
// const camera = new THREE.PerspectiveCamera( 70, width / height, 0.01, 10 );
// camera.position.z = 1;
// // 创建场景
// const scene = new THREE.Scene();
//  // 创建立方体
// const geometry = new THREE.BoxGeometry( 0.2, 0.2, 0.2 );
// // 创建基础网格材质(MeshBasicMaterial)
// const material = new THREE.MeshNormalMaterial();
// // 网格
// const mesh = new THREE.Mesh( geometry, material );
// scene.add( mesh );
// // 创建渲染器
// const renderer = new THREE.WebGLRenderer( { antialias: true } );
// // 调整渲染器窗口大小
// renderer.setSize( width, height );
// renderer.setAnimationLoop( animation );
// document.body.appendChild( renderer.domElement );

// // animation
//  // 让立方体动起来
// function animation( time ) {

// 	mesh.rotation.x = time / 2000;
// 	mesh.rotation.y = time / 1000;

// 	renderer.render( scene, camera );

// }


import * as THREE from 'three';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
// 钩子函数
import { onMounted } from 'vue'
//  dat-gui
import * as dat from 'dat.gui';

// 创建一个控制对象
const controlData = {
	rotationSpeed: 0.01,
	color: '#66ccff',
	wireframe: false,
}
// 创建实例
const gui = new dat.GUI()
// 创建类目
const f = gui.addFolder('配置')
//  数据 key  初始值 最大值 步骤长
// f.add(controlData, 'rotationSpeed', 0.01, 0.1, 0.01)
f.add(controlData, 'rotationSpeed').min(0.01).max(0.1).step(0.01)
// 颜色选择器
f.addColor(controlData, 'color')
// checkbox
f.add(controlData, 'wireframe')
f.domElement.id = 'gui'
f.open()
// 创建场景
// 场景能够让你在什么地方，摆放什么东西来交给three.js来渲染，这是你放置物体，灯光和摄像机的地方
const scene = new THREE.Scene()

// 添加背景颜色
// scene.background = new THREE.Color(0x666666)

// 添加背景图片
// scene.background = new THREE.CubeTextureLoader().setPath('/').load(['11.jpg','11.jpg','11.jpg','11.jpg','11.jpg','11.jpg'])

// 添加雾
// scene.fog = new THREE.Fog( 0xcccccc, 10, 15 );

// 创建相机（视角）
const camera = new THREE.PerspectiveCamera()
camera.position.y = 5 // Y轴
camera.position.z = 10  // X轴

// 创建纹理
// const texture = new THREE.TextureLoader().load( "textures/01-map.jpg" );

// 创建立体纹理
const cubeTexture = new THREE.CubeTextureLoader().setPath('/textures/').load([
	'04.jpg', '01.jpg',
	'05.jpg', '02.jpg',
	'06.jpg', '03.jpg',
]);
scene.background = cubeTexture // 添加背景立体纹理


// // 创建立方体
// const geometry = new THREE.BoxGeometry(1, 1, 1);

// // 创建基础网格材质(MeshBasicMaterial)
// const material = new THREE.MeshBasicMaterial(
// 	{
// 		// color: 0x00ff00,
// 		map: texture,
// 		envMap: cubeTexture
// 	}
// 	);


// 创建一个球体
// const sphere = new THREE.SphereGeometry(1);
// const material = new THREE.MeshBasicMaterial(
// 	{
// 		color: 0xffff00,
// 		envMap: cubeTexture
// 	}
// );
// 平面几何体
// const geometry = new THREE.PlaneGeometry( 1, 1 );
// const material = new THREE.MeshBasicMaterial(
// 	{
// 		color: 0xffff00,
// 		side: THREE.DoubleSide,
// 		wireframe: true
// 	}
// );


// BufferGeometry 自定义几何体
const geometry = new THREE.BufferGeometry();
// 创建一个简单的矩形. 在这里我们左上和右下顶点被复制了两次。
// 因为在两个三角面片里，这两个顶点都需要被用到。
const vertices = new Float32Array([
	-1.0, -1.0, 1.0,
	1.0, -1.0, 1.0,
	1.0, 1.0, 1.0,

	// 1.0, 1.0, 1.0,
	-1.0, 1.0, 1.0,
	// -1.0, -1.0, 1.0
]);

// itemSize = 3 因为每个顶点都是一个三元组。
geometry.setAttribute('position', new THREE.BufferAttribute(vertices, 3));

// 另一种绘图模式
// ①确认几何体的顶点
// 利用索引来确认几何体的绘制方式

const indexs = new Uint16Array([
	0, 1, 2, 2, 3, 0
])
geometry.index = new THREE.BufferAttribute(indexs, 1)


const material = new THREE.MeshBasicMaterial(
	{
		color: 0xff0000,
		side: THREE.DoubleSide,
		wireframe: true
	}
	);


// 网格
const cube = new THREE.Mesh(geometry, material)
// const cube = new THREE.Mesh(sphere, material)
cube.position.set(0, 3, 0)
scene.add(cube)



// 添加网格地面
const gridHelper = new THREE.GridHelper(10, 10)
scene.add(gridHelper);

const moveCamera = () => {
	camera.position.y = 15
	camera.position.z = 10
	camera.lookAt(0, 3, 0)
}
const moveCube = () => {
	cube.position.set(3, 5, 0)
	camera.lookAt(cube.position) // 动态看向物体
}
onMounted(() => {


	// 创建渲染器
	const renderer = new THREE.WebGLRenderer()
	//
	// 调整渲染器窗口大小
	renderer.setSize(window.innerWidth, window.innerHeight)

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

	// 添加坐标轴
	const axesHelper = new THREE.AxesHelper(5);
	axesHelper.position.y = 3
	scene.add(axesHelper);


	// renderer.render(scene, camera)

	// 让立方体动起来
	function animate() {
		requestAnimationFrame(animate)
		// cube.rotation.x += 0.01
		// cube.rotation.y += 0.01
		// cube.rotation.x += controlData.rotationSpeed
		// cube.rotation.y += controlData.rotationSpeed
		// cube.material.color = new THREE.Color(controlData.color)
		// material.wireframe = controlData.wireframe
		// 轨道控制器更新
		// controls.update();
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
}
</style>

