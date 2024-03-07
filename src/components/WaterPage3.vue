<template>
  <div class="app">
    <div ref="waterRef" @click="showCamera"></div>

    <img class="logo" src="../assets/logo.png" alt="" />

    <div class="loading" v-if="isLoading">
      <div class="sk-circle">
        <div class="sk-circle1 sk-child"></div>
        <div class="sk-circle2 sk-child"></div>
        <div class="sk-circle3 sk-child"></div>
        <div class="sk-circle4 sk-child"></div>
        <div class="sk-circle5 sk-child"></div>
        <div class="sk-circle6 sk-child"></div>
        <div class="sk-circle7 sk-child"></div>
        <div class="sk-circle8 sk-child"></div>
        <div class="sk-circle9 sk-child"></div>
        <div class="sk-circle10 sk-child"></div>
        <div class="sk-circle11 sk-child"></div>
        <div class="sk-circle12 sk-child"></div>
      </div>
    </div>
    <!-- 功能按钮 -->
    <div class="menu-part">
      <ul>
        <li
          @click="
            showAreaList = !showAreaList;
            showColorList = false;
          "
        >
          <img src="../assets/icon1.png" alt="" />

          <span>部件</span>
        </li>
        <li @click="changeCamera">
          <img src="../assets/icon2.png" alt="" />
          <span>视图</span>
        </li>
        <li @click="handleEffect">
          <img src="../assets/icon3.png" alt="" />
          <span>旋转</span>
        </li>
        <li @click="handleReset">
          <img src="../assets/icon4.png" alt="" />
          <span>重置</span>
        </li>
        <li @click="createImage">
          <img src="../assets/icon5.png" alt="" />
          <span>分享</span>
        </li>
      </ul>
    </div>
    <!-- 分享图片 -->
    <div class="share-wrap" v-show="shareImage !== null">
      <div class="share-inner">
        <img
          src="../assets/close.png"
          alt=""
          class="close-icon"
          @click="shareImage = null"
        />
        <div class="share-header">分享图片</div>
        <div class="share-body">
          <a :href="shareImage" download="kettle.png">
            <img class="share-img" :src="shareImage" alt="" />
          </a>
        </div>
        <div class="share-footer">
          <img src="../assets/qq.png" alt="" />
          <img src="../assets/wechat.png" alt="" />
          <img src="../assets/dingtalk.png" alt="" />
          <img src="../assets/facebook.png" alt="" />
        </div>
      </div>
    </div>
    <!-- 选择部位 -->
    <transition>
      <div class="area-menu area-menu1" v-show="showAreaList">
        <div>
          <div class="area-name">部件</div>
          <div class="color-list" v-if="isMobile == false">
            <div
              class="color-item"
              v-for="(areaInfo, index) in areaList"
              :key="index"
              @click="() => handleSelectArea(areaInfo.areaName, index)"
            >
              <div class="color-info">
                <div class="color-name">{{ areaInfo.areaName }}</div>
                <div class="text">{{ areaInfo.colorName }}</div>
              </div>
              <div
                class="color"
                :style="{ backgroundColor: areaInfo.color }"
              ></div>
            </div>
          </div>
          <!-- 选择部位——手机端 -->
          <div class="color-list" v-else-if="isMobile == true">
            <div
              class="color-item"
              v-for="(areaInfo, index) in areaList"
              :key="index"
              @click="() => handleSelectArea(areaInfo.areaName, index)"
            >
              <div class="color-name font-bg">{{ areaInfo.areaName }}</div>
              <div class="color-info">
                <div
                  class="color"
                  :style="{ backgroundColor: areaInfo.color }"
                ></div>
                <div class="text">{{ areaInfo.colorName }}</div>
              </div>
            </div>
          </div>
        </div>
      </div></transition
    >
    <!-- 选择颜色 -->
    <transition>
      <div class="area-menu area-menu2" v-show="showColorList">
        <!-- <div class="area-menu area-menu2"> -->
        <div>
          <div class="sp-relative">
            <img
              src="../assets/return1.png"
              alt=""
              class="return-btn"
              @click="
                showAreaList = !showAreaList;
                showColorList = false;
              "
            />
            <div class="area-name">{{ currentArea }}</div>
          </div>
          <div class="color-list" v-if="isMobile == false">
            <div
              class="color-item"
              v-for="colorInfo in colorList"
              :key="colorInfo.color"
              @click="() => switchColor(currentAreaIndex, colorInfo)"
            >
              <div class="color-info">
                <div class="color-name">{{ colorInfo.name }}</div>
                <div class="text">默认颜色</div>
              </div>
              <div
                class="color"
                :style="{ backgroundColor: colorInfo.color }"
              ></div>
            </div>
          </div>
          <!-- 选择颜色——手机端 -->
          <div class="color-list" v-else-if="isMobile == true">
            <div
              class="color-item"
              v-for="colorInfo in colorList"
              :key="colorInfo.color"
              @click="() => switchColor(currentAreaIndex, colorInfo)"
            >
              <div
                class="color"
                :style="{ backgroundColor: colorInfo.color }"
              ></div>

              <div class="color-name">{{ colorInfo.name }}</div>
            </div>
          </div>
        </div>
      </div></transition
    >
  </div>
</template>
<script setup>
import { ref, onMounted, onUnmounted, watchEffect } from "vue";
import * as THREE from "three";
import { FBXLoader } from "three/addons/loaders/FBXLoader.js";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import { RGBELoader } from "three/addons/loaders/RGBELoader.js";
// import { GUI } from "three/addons/libs/lil-gui.module.min.js";
import * as TWEEN from "@tweenjs/tween.js";
import "../style/style.css";

const isMobile = ref(false);
const isLoading = ref(true);
const showAreaList = ref(false);
const showColorList = ref(false);

// 当前选择的部件
const currentArea = ref("水壶盖");
const currentAreaIndex = ref(null);
// 部位
const areaList = [
  {
    areaName: "壶体", // 对称_1
    colorName: "默认颜色",
    color: "#fff",
    coords: { x: 2500, y: 1000, z: 0 },
    material: null,
  },
  {
    areaName: "水壶盖", // Retopo_布尔_4_1
    colorName: "默认颜色",
    color: "#fff",
    // coords: { x: 770, y: 1500, z: -390 },
    coords: { x: 2500, y: 1000, z: 0 },
    material: null,
  },
  {
    areaName: "底座", // 挤压
    colorName: "默认颜色",
    color: "#fff",
    coords: { x: 2500, y: 1000, z: 100 },
    material: null,
  },
  {
    areaName: "把手", // Retopo_布尔_4
    colorName: "默认颜色",
    color: "#fff",
    coords: { x: 1100, y: 830, z: -2200 },
    material: null,
  },

  {
    areaName: "按钮", // Retopo_细分曲面_3
    colorName: "默认颜色",
    color: "#fff",
    coords: { x: 1100, y: 830, z: -2200 },
    material: null,
  },
  {
    areaName: "指示灯", // Retopo_布尔_4_1_1
    colorName: "默认颜色",
    color: "#fff",
    coords: { x: 1100, y: 830, z: -2200 },
    material: null,
  },
  {
    areaName: "上开关",
    colorName: "默认颜色",
    color: "#fff",
    coords: { x: 1100, y: 830, z: -2200 },
    material: null,
  },
];
// 颜色
const colorList = [
  {
    name: "黑色",
    color: "#000",
  },
  {
    name: "青褐色",
    color: "#0c2342",
  },
  {
    name: "海军蓝",
    color: "#003a77",
  },
  {
    name: "奶油黄",
    color: "#dfd6c7",
  },
  {
    name: "绯红色",
    color: "#b90216",
  },
  {
    name: "赤褐色",
    color: "#450d10",
  },
  {
    name: "黄色",
    color: "#feee75",
  },
  {
    name: "淡青色",
    color: "#b8fce5",
  },
  {
    name: "玫瑰棕色",
    color: "#ba6e8a",
  },
  {
    name: "靛青色",
    color: "#36155a",
  },
  {
    name: "海洋绿",
    color: "#0f6212",
  },
  {
    name: "橙色",
    color: "#e57802",
  },
  {
    name: "淡蓝色",
    color: "#55a7bf",
  },
  {
    name: "沙棕色",
    color: "#846535",
  },
  {
    name: "默认颜色",
    color: "#f7f7f7",
  },
];

let scene;
let camera;
let renderer;
const waterRef = ref(null);
let frameId = 0;
let controls;
const controlsRef = ref(null);
let tween;
let plane;
// 切换颜色
function switchColor(index, colorInfo) {
  areaList[index].color = colorInfo.color;
  areaList[index].colorName = colorInfo.name;
  areaList[index].material.color.set(colorInfo.color);
}

// 选择区域
function handleSelectArea(info, index) {
  controls.autoRotate = false;

  currentArea.value = info;
  currentAreaIndex.value = index;
  showAreaList.value = false;
  showColorList.value = true;

  // let coords = areaList[index].coords;
  // camera.position.set(coords.x, coords.y, coords.z);

  // tween = new TWEEN.Tween(camera.position)
  //   .to(
  //     {
  //       x: coords.x ? coords.x : camera.position.x,
  //       y: coords.y,
  //       z: coords.z,
  //     },
  //     2500
  //   )
  //   .easing(TWEEN.Easing.Quadratic.InOut)
  //   .start();
}
for (let i of areaList) {
  i.material = new THREE.MeshPhysicalMaterial({
    color: 0xffffff,
    metalness: 0.8, // 金属度
    roughness: 0.9, // 粗糙度
    clearcoat: 0.3, // 轻漆
    clearcoatRoughness: 0.4, // 轻漆粗糙度
  });
}

function init() {
  if (waterRef.value) {
    scene = new THREE.Scene();
    scene.background = new THREE.Color(0xfefefe);
    camera = new THREE.PerspectiveCamera(
      45,
      window.innerWidth / window.innerHeight,
      1,
      1200
    );
    camera.position.set(250, 100, 10);

    renderer = new THREE.WebGLRenderer({
      antialias: true,
      // alpha: true,
      preserveDrawingBuffer: true,
      logarithmicDepthBuffer: true,
    });

    renderer.setPixelRatio(window.devicePixelRatio);
    renderer.shadowMap.enabled = true;
    renderer.shadowMap.type = THREE.PCFSoftShadowMap; // 默认的阴影类型
    renderer.setClearColor("#ffffff");
    renderer.setSize(window.innerWidth, window.innerHeight);
    waterRef.value.append(renderer.domElement);

    // 创建灯光
    // 环境光
    const ambientLight = new THREE.AmbientLight(0xffffff, 2);
    scene.add(ambientLight);

    const light1 = new THREE.DirectionalLight(0xffffff, 0.7);
    light1.position.set(0, 0, 100); // 壶嘴
    scene.add(light1);
    // const light2 = new THREE.DirectionalLight(0xffffff, 0.7);
    // light2.position.set(0, 0, -100); // 壶把
    // scene.add(light2);

    const light3 = new THREE.DirectionalLight(0xffffff, 0.9);
    light3.position.set(200, 0, 40); // 正面
    scene.add(light3);

    const light4 = new THREE.DirectionalLight(0xffffff, 1.9);
    light4.position.set(-200, 200, 40); // 背面
    scene.add(light4);

    const light5 = new THREE.DirectionalLight(0xffffff, 1);
    light5.position.set(0, 100, 0); // 顶光
    scene.add(light5);

    const light6 = new THREE.DirectionalLight(0xffffff, 1);
    light6.position.set(1000, 100, 1000); // 壶嘴
    scene.add(light6);
    // const light7 = new THREE.DirectionalLight(0xffffff, 1);
    // light7.position.set(-1000, -100, -1000); // 壶把
    // scene.add(light7);
    const light8 = new THREE.DirectionalLight(0xffffff, 1.5);
    light8.position.set(-500, 0, 0); // 背面+ 底部
    scene.add(light8);
    const light9 = new THREE.DirectionalLight(0xffffff, 1);
    light9.position.set(500, 200, 0); // 正面+顶部
    scene.add(light9);

    // 引入并渲染模型
    const loader = new FBXLoader();
    loader.load(`./model/4.fbx`, function (object) {
      isLoading.value = false;
      object.position.set(0, -15, 0);
      scene.add(object);
      // object.castShadow = true;
      object.traverse(function (child) {
        // child.castShadow = true;
        // child.receiveShadow = true;
        if (child.isMesh) {
          // 壶盖
          if (child.name == "对称_1" || child.name == "把手内侧") {
            child.material = areaList[0].material;
          } else if (child.name == "盖子") {
            // 壶把外侧
            child.material = areaList[1].material;
          } else if (
            child.name == "挤压" ||
            child.name == "克隆" ||
            child.name == "路径_9" ||
            child.name == "路径_6" ||
            child.name == "圆柱体_1"
          ) {
            child.material = areaList[2].material;
          } else if (child.name == "把手外侧") {
            child.material = areaList[3].material;
          } else if (child.name == "电源开盖") {
            child.material = areaList[4].material;
          } else if (child.name == "指示灯") {
            child.material = areaList[5].material;
          } else if (child.name == "布尔" || child.name == "不锈钢金属") {
            child.material = matter;
          } else if (child.name == "开盖按钮") {
            // 壶把外侧
            child.material = areaList[6].material;
          }
        }
        // child.material.shading = THREE.SmoothShading;
      });
    });
    // 管道_2 顶部半圆
    // 布尔 不锈钢金属
    // 克隆 底部三角
    // 挤压 底座
    // 圆柱体_1 底部内芯 路径_9——底部小点
    // 管道_2_2 把手外侧
    // 路径_6 底座
    // 电源开盖 按钮
    // 对称_1 壶体
    // 把手外侧 把手内侧 把手

    // 加入控制器
    controls = new OrbitControls(camera, renderer.domElement);
    controls.update();
    controls.minDistance = 50;
    controls.maxDistance = 100;
    controls.maxPolarAngle = Math.PI / 2;
    controlsRef.value = controls;
    controls.autoRotate = true;
    let matter = new THREE.MeshPhysicalMaterial({
      color: "silver",
      metalness: 0.6, // 金属度
      roughness: 0.5, // 粗糙度
      clearcoat: 0.3, // 轻漆
      clearcoatRoughness: 0.6, // 轻漆粗糙度
    });
    // let gui = new GUI();
    // gui.add(matter, "metalness", 0.5, 2);
    // gui.add(matter, "roughness", 0, 1);
    // gui.add(matter, "clearcoat", 0, 2);
    // gui.add(matter, "clearcoatRoughness", 0.2, 0.8);

    // const whiteMaterial = new THREE.MeshBasicMaterial({ color: 0xffffff });
    // const sphere = new THREE.Mesh(sphereGeometry, whiteMaterial);
    // sphere.position.set(0, 0, 0);
    // scene.add(sphere);

    new RGBELoader().load("./model/room3.hdr", function (texture) {
      const pmremGenerator = new THREE.PMREMGenerator(renderer);
      pmremGenerator.compileEquirectangularShader();
      const envMap = pmremGenerator.fromEquirectangular(texture).texture;
      scene.environment = envMap;
      texture.dispose();
      pmremGenerator.dispose();

      // scene.environment = texture;
      // scene.environment.mapping = THREE.EquirectangularReflectionMapping;
      // scene.background = texture;
      // renderer.outputEncoding = THREE.sRGBEncoding;
      // renderer.render(scene, camera);

      //  child.material.envMap=textureCube;
    });

    createEffect();
    scene.add(plane);
    console.log(plane);
    render();
  }
}

// 是否展示平面
// let textureLoader, texture;
// let planeGeometry, planeMaterial;
// textureLoader = new THREE.TextureLoader();
// texture = textureLoader.load(`./material1.jpg`);
// planeGeometry = new THREE.PlaneGeometry(2500, 2500); // 骨架
// planeMaterial = new THREE.MeshLambertMaterial({
//   map: texture,
//   flatShading: true,
//   color: "#ffffff",
// });
// plane = new THREE.Mesh(planeGeometry, planeMaterial); // 网格
// plane.receiveShadow = true;
// plane.position.set(2, -436, 0);
// plane.rotation.x = (-90 * Math.PI) / 180;

function createEffect() {
  // 添加纹理

  const geometry = new THREE.CircleGeometry(100, 100);
  const canvas = document.createElement("canvas");
  const context = canvas.getContext("2d");
  const size = 400;
  canvas.width = size;
  canvas.height = size;
  const gradient = context.createRadialGradient(
    size / 2,
    size / 2,
    0,
    size / 2,
    size / 2,
    size / 2
  );
  gradient.addColorStop(0, "rgba(32,32,32,0.5)"); // 中间浅黑色
  gradient.addColorStop(1, "rgba(255,255,255,0)"); // 边缘透明

  context.fillStyle = gradient;
  context.fillRect(0, 0, size, size);
  const texture = new THREE.CanvasTexture(canvas);
  const material = new THREE.MeshBasicMaterial({
    map: texture,
    transparent: true,
  });
  plane = new THREE.Mesh(geometry, material);
  plane.position.set(0, -15, 0);
  plane.rotation.x = (-90 * Math.PI) / 180;
  scene.add(plane);
  // plane.receiveShadow = true;
}
function handleEffect() {
  controls.autoRotate = !controls.autoRotate;
}

// 切换相机视角
// function animateCamera(current1, target1, current2, target2) {
//   tween = new TWEEN.Tween({
//     x1: current1.x, //  相机当前位置
//     y1: current1.y,
//     z1: current1.z,
//     x2: current2.x, // 控制点当前中心
//     y2: current2.y,
//     z2: current2.z,
//   });
//   tween.to({
//     x1: target1.x, // 新相机位置
//     y1: target1.y,
//     z1: target1.z,
//     x2: target2.x, // 新的控制点中心位置
//     y2: target2.y,
//     z2: target2.z,
//   });
//   tween.onUpdate(function (object) {
//     camera.position.x = object.x1;
//     camera.position.y = object.y1;
//     camera.position.z = object.z1;
//     controls.target.x = object.x2;
//     controls.target.y = object.y2;
//     controls.target.z = object.z2;
//   });
//   // tween.easing(TWEEN.Easing.Cubic.Inout);
//   tween.start();
// }
// animateCamera(camera.position, pos, controls.target, pos2);
// 点击模型事件
// function onDocumentMouseDown(event) {
//   // 点击屏幕创建一个向量
//   var vector = new THREE.Vector3(
//     (event.clientX / window.innerWidth) * 2 - 1,
//     -(event.clientY / window.innerHeight) * 2 + 1,
//     0.5
//   );
//   vector = vector.unproject(camera); // 将屏幕的坐标转换成三维场景中的坐标
//   var raycaster = new THREE.Raycaster(
//     camera.position,
//     vector.sub(camera.position).normalize()
//   );
//   var intersects = raycaster.intersectObjects(scene.children, true);

//   if (intersects.length > 0) {
//     // 随机坐标
//     var x = Math.round(Math.random() * 100);
//     var y = Math.round(Math.random() * 100);
//     var z = 50;

//     var x2 = 0;
//     var y2 = 200;
//     var z2 = 100;

//     var pos = new THREE.Vector3(x, y, z);
//     var pos2 = new THREE.Vector3(x2, y2, z2);

//     // console.log(camera.position, pos, controls.target, pos2);
//     // intersects[0].object.material.color.set("#55a7bf");
//     animateCamera(camera.position, pos, controls.target, pos2);
//   }
// }

// animateCamera();
// let cameraList = [
//   {
//     x: 800,
//     y: 1100,
//     z: -1500,
//   },
//   {
//     x: 0,
//     y: 2100,
//     z: 0.00016,
//   },
//   {
//     x: 200,
//     y: 1300,
//     z: 1200,
//   },
//   {
//     x: 220,
//     y: -2100,
//     z: 0,
//   },
//   {
//     x: 2500,
//     y: 1000,
//     z: 0,
//   },
// ];

let cameraList = [
  {
    x: 500,
    y: 1300,
    z: 1300,
  },
  {
    x: -2500,
    y: 1000,
    z: 0,
  },
  {
    x: -670,
    y: 1300,
    z: -1500,
  },
  {
    x: 1200,
    y: -1300,
    z: -1300,
  },
  {
    x: 2500,
    y: 1000,
    z: 0,
  },
];
let currentCameraIndex = 0;
function tweenEnd() {
  if (tween && tween.end) {
    tween.end();
  }
}
function changeCamera() {
  controls.autoRotate = false;
  tweenEnd();

  let coords = cameraList[currentCameraIndex];

  if (currentCameraIndex < cameraList.length - 1) {
    camera.position.set(coords.x, coords.y, coords.z);
    // tween = new TWEEN.Tween(camera.position)
    //   .to(
    //     {
    //       x: coords.x,
    //       y: coords.y,
    //       z: coords.z,
    //     },
    //     2000
    //   )
    //   .easing(TWEEN.Easing.Quadratic.InOut)
    //   .start();

    currentCameraIndex += 1;
  } else {
    // tween = new TWEEN.Tween(camera.position)
    //   .to(
    //     {
    //       x: coords.x,
    //       y: coords.y,
    //       z: coords.z,
    //     },
    //     2000
    //   )
    //   .easing(TWEEN.Easing.Quadratic.InOut)
    //   .start();
    camera.position.set(coords.x, coords.y, coords.z);

    currentCameraIndex = 0;
  }
}
// 重置
function handleReset() {
  tweenEnd();

  for (let i of areaList) {
    i.colorName = "默认颜色";
    i.color = "#fff";
    i.material.color.set("#fff");
  }
  camera.position.set(250, 100, 10);
  controls.autoRotate = true;
}
// 创建分享图片
let shareImage = ref(null);
function createImage() {
  shareImage.value = renderer.domElement.toDataURL("/image/png");
}
const onResize = () => {
  isMobile.value = window.innerWidth < 780 ? true : false;

  if (waterRef.value && scene && camera && renderer) {
    const { clientWidth, clientHeight } = waterRef.value;
    // 更新相机
    camera.aspect = clientWidth / clientHeight;
    camera.updateProjectionMatrix();

    // 更新渲染器
    renderer.setSize(clientWidth, clientHeight);

    // 设置渲染器的像素比
    renderer.setPixelRatio(window.devicePixelRatio);
  }
};
onMounted(() => {
  isMobile.value = window.innerWidth < 780 ? true : false;

  init();
  window.addEventListener("resize", onResize);
});
onUnmounted(() => {
  frameId && window.cancelAnimationFrame(frameId);
  window.removeEventListener("resize", onResize);
});

watchEffect(() => {
  onResize();
});
const render = () => {
  if (controlsRef.value) {
    controlsRef.value?.update();
  }
  frameId = window.requestAnimationFrame(render);
  TWEEN.update();
  renderer.render(scene, camera);
};

// let intersects = null;
// const raycaster = new THREE.Raycaster();

function showCamera() {
  // console.log("camera.position:", camera.position);
  console.log(camera);
  console.log(camera.polarAngle);
  // var vector = new THREE.Vector3(
  //   (event.clientX / window.innerWidth) * 2 - 1,
  //   -(event.clientY / window.innerHeight) * 2 + 1,
  //   0.5
  // );
  // console.log(vector);
  // raycaster.setFromCamera(mouse, camera);
  // intersects = raycaster.intersectObject(scene, true);
  // if (intersects.length > 0) {
  //   // let boxMaxY = new THREE.Box3().setFromObject(intersects[0].object).max.y;

  //   // let distance = boxMaxY + 10;
  //   // let angel = Math.PI / 5;

  //   // let position = {
  //   //   x: intersects[0].object.position.x + Math.cos(angel) * distance,
  //   //   y: intersects[0].object.position.y,
  //   //   z: intersects[0].object.position.z + Math.sin(angel) * distance,
  //   // };

  //   // tween = new TWEEN.Tween(camera.position).to(position, 3000);
  //   tween = new TWEEN.Tween(controls.target).to(
  //     intersects[0].object.position,
  //     2000
  //   );

  //   controls.enabled = false;
  //   tween.onComplete(function () {
  //     controls.enabled = true;
  //   });

  //   tween.start();
  // }
}
</script>
<style scoped>
.app {
  width: 100%;
  height: 100vh;
  position: relative;
}
/* 底部选项 */
.menu-part {
  width: 100%;
  height: 1.8rem;

  position: absolute;
  left: 0;
  right: 0;
  bottom: 0;
  /* background: rgba(0, 0, 0, 0.1); */
  z-index: 66;
}

.menu-part ul {
  display: flex;
  justify-content: center;
  align-items: center;
}
.menu-part li {
  width: 80px;
  width: 2rem;
  height: 1.7rem;

  /* padding: 0.1rem 0; */
  box-sizing: border-box;
  /* margin-right: 40px; */
  display: flex;
  -ms-flex-direction: column;
  flex-direction: column;
  -ms-flex-pack: justify;
  justify-content: center;
  -ms-flex-align: center;
  align-items: center;
  cursor: pointer;
}
.menu-part li img {
  width: 1rem;
  height: 1rem;
  margin-bottom: 0.1rem;
  vertical-align: top;
  background: rgba(0, 0, 0, 0.1);
  border-radius: 50%;
  padding: 8px;
  box-sizing: border-box;
}
.menu-part li span {
  font-size: 16px;
  color: #000;
}

.menu-part li:nth-child(3) img {
  padding: 12px;
}
/* 区域颜色选项 */
.area-menu {
  position: absolute;
  right: 10px;
  top: 150px;
  bottom: 148px;
  /* animation: fade-in 0.5s; */
}

.v-enter-active,
.v-leave-active {
  transition: all 0.4s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
  transform: translateX(100%);
}
@keyframes fade-in {
  0% {
    opacity: 0;
    transform: translateX(100%);
  }
  100% {
    opacity: 1;
    transform: translateX(0);
  }
}
.area-menu > div {
  width: 190px;
  height: 100%;
  max-height: 100%;
  position: relative;
}
.area-menu .area-name {
  width: 100%;
  height: 38px;
  font-size: 16px;
  font-weight: 400;
  color: #fff;
  line-height: 38px;
  text-align: center;
  background: rgba(0, 0, 0, 0.8);
}
.color-list {
  width: 100%;
  overflow-y: auto;
  overflow-x: hidden;
  position: absolute;
  top: 40px;
  bottom: 0;
}
.color-item {
  width: 100%;
  height: 56px;
  padding: 8px 12px;
  margin-top: 2px;
  background: rgba(0, 0, 0, 0.1);
  display: flex;
  overflow: hidden;
  -ms-flex-align: center;
  align-items: center;
  box-sizing: border-box;
  justify-content: space-between;
  cursor: pointer;
}
.color-item:hover {
  background: rgba(0, 0, 0, 0.25);
}
.color-item .info {
  display: flex;
  flex-direction: columns;
  justify-content: space-between;
  flex-grow: 1;
  height: 40px;
  overflow: hidden;
  margin-right: 10px;
}
.color-item .color-name {
  margin-right: 10px;
  font-size: 14px;
  font-weight: 500;
  color: #222;
  line-height: 20px;
  white-space: nowrap;
  text-overflow: ellipsis;
  overflow: hidden;
}
.color-item .text {
  font-size: 12px;
  font-weight: 400;
  color: #555;
  line-height: 17px;
}
.color-item .color {
  border-radius: 50%;
  width: 32px;
  height: 32px;
  background: #000;
}
.share-wrap {
  position: absolute;

  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  background: #00000071;
  z-index: 9;
}

.share-inner {
  width: 8rem;
  position: absolute;
  height: 10rem;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  background: #fff;
}
.share-header {
  width: 100%;
  height: 1rem;
  background: #f0f0f0;
  line-height: 1rem;
  font-size: 0.35rem;
  font-weight: 500;
  color: #000;
  padding-left: 0.3rem;
  box-sizing: border-box;
}
.share-body {
  width: 100%;
  height: 7.5rem;
  overflow: hidden;
  position: relative;
  display: flex;
  justify-content: center;
}

.share-img {
  width: auto;
  height: 100%;
  margin: auto;
  display: block;
  transform: scale(1.1);
}
.share-footer {
  widows: 100%;
  height: 1.5rem;
  display: flex;
  align-items: center;
  justify-content: space-evenly;
}
.share-footer img {
  width: 1rem;
  cursor: pointer;
}
.share-wrap .close-icon {
  position: absolute;
  width: 0.66666667rem;
  height: 0.66666667rem;
  right: 0;
  transform: translate(0.33333333rem, -0.33333333rem);
}
.logo {
  position: absolute;
  top: 0.5rem;
  left: 0.5rem;
  width: 2rem;
  height: auto;
}
.sp-relative {
  position: relative;
}
.return-btn {
  position: absolute;
  top: 50%;
  left: 8px;
  width: 22px;
  transform: translateY(-50%);
  cursor: pointer;
}
@media screen and (max-width: 780px) {
  .logo {
    width: 30vw;
    height: auto;
  }
  .area-menu {
    bottom: 2rem;
    height: 2.2rem;
    width: 100%;
    right: unset;
    top: unset;
    left: 0;
  }
  .area-menu > div {
    width: 100%;
    display: flex;
    height: 100%;
    /* transform: translate3d(-115%, 0, 0); */
  }
  .area-menu .area-name {
    width: 1.25rem;
    height: 100%;
    writing-mode: vertical-lr;
    letter-spacing: 2px;
    font-size: 0.35rem;
  }
  .color-list {
    width: 100%;
    height: 100%;
    overflow-y: hidden;
    overflow-x: auto;
    position: unset;
    width: auto;
    height: 100%;
    flex-grow: 1;
    display: flex;

    margin: auto 0;
  }
  .color-item {
    width: 2rem;
    padding: 0.13rem 0;
    height: auto;
    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
    flex-shrink: 0;
    text-align: center;
    position: relative;
  }
  .color-item .color-name {
    width: 1.4rem;
    height: 0.5rem;

    border-radius: 20px;
    font-weight: 400;
    color: #222;
    font-size: 0.3rem;
    text-align: center;
    line-height: 0.5rem;
    margin-right: 0;
  }
  .color-item .color-name.font-bg {
    background: #00000029;
  }
  .color-info {
    display: flex;
  }
  .area-menu1 .color-item .color {
    width: 0.42666667rem;
    height: 0.42666667rem;
    flex-shrink: 0;
  }
  .color-item .text {
    transform: scale(0.9);
    white-space: nowrap;
    font-size: 0.32rem;
  }
  .color-item:before {
    content: "";
    position: absolute;
    border-left: 2px solid #00000033;
    width: 1px;
    height: 0.65rem;
    top: 50%;
    transform: translate(-1rem, -50%);
  }
  .menu-part li span {
    font-size: 0.4rem;
  }
}
</style>
