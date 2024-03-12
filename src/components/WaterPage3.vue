<template>
  <div class="app">
    <div ref="waterRef" @click="showCamera"></div>

    <img ref="logo" class="logo" src="../assets/logo.png" alt="" />
    <!-- 加载动画 -->
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
            showAreaList = false;
            showColorList = false;
            showComponentList = !showComponentList;
            showComponentStyleList = false;
          "
        >
          <div class="img-wrap small img1">
            <img src="../assets/icon0.png" alt="" />
          </div>

          <span>部件</span>
        </li>
        <li
          class="small"
          @click="
            showAreaList = !showAreaList;
            showColorList = false;
            showComponentStyleList = false;
            showComponentList = false;
          "
        >
          <div class="img-wrap small">
            <img src="../assets/icon1.png" alt="" />
          </div>

          <span>颜色</span>
        </li>

        <li @click="changeCamera">
          <div class="img-wrap">
            <img src="../assets/icon2.png" alt="" />
          </div>
          <span>视图</span>
        </li>
        <li @click="handleEffect" class="small">
          <div class="img-wrap small">
            <img src="../assets/icon3.png" alt="" />
          </div>
          <span>旋转</span>
        </li>
        <li @click="handleReset">
          <div class="img-wrap">
            <img src="../assets/icon4.png" alt="" />
          </div>
          <span>重置</span>
        </li>
        <li @click="createImage">
          <div class="img-wrap small">
            <img src="../assets/icon5.png" alt="" />
          </div>
          <span>分享</span>
        </li>
      </ul>
    </div>
    <!-- 分享图片 -->

    <div class="share-wrap" v-show="shareImage !== null">
      <div class="share-inner fade-in">
        <img
          src="../assets/close.png"
          alt=""
          class="close-icon"
          @click="shareImage = null"
        />

        <div class="share-header">分享图片</div>
        <div class="share-body">
          <img class="logo logo2" src="../assets/logo.png" alt="" />
          <a :href="shareImage" download="kettle.png">
            <img
              class="share-img"
              :src="shareImage"
              alt=""
              crossorigin="anonymous"
            />
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
    <!-- 选择需要更换的部位 -->
    <transition>
      <div class="area-menu area-menu1" v-show="showComponentList">
        <div>
          <div class="area-name">部件</div>
          <div class="color-list" v-if="isMobile == false">
            <div
              class="color-item"
              v-for="(item, index) in componentList"
              :key="index"
              @click="() => handleSelectComponent(index)"
            >
              <div class="color-info">
                <div class="color-name">{{ item.areaName }}</div>
                <div class="text">{{ item.currentApply }}</div>
              </div>
            </div>
          </div>
          <!-- 选择部位——手机端 -->
          <div class="color-list" v-else-if="isMobile == true">
            <div
              class="color-item"
              v-for="(item, index) in componentList"
              :key="index"
              @click="() => handleSelectComponent(index)"
            >
              <div class="color-name font-bg">{{ item.areaName }}</div>
              <div class="color-info">
                <!-- <div
                  class="color"
                  :style="{ backgroundColor: areaInfo.color }"
                ></div>-->
                <div class="text">{{ item.currentApply }}</div>
              </div>
            </div>
          </div>
        </div>
      </div></transition
    >
    <!-- 选择零件款式 -->
    <transition>
      <div class="area-menu area-menu2" v-show="showComponentStyleList">
        <!-- <div class="area-menu area-menu2"> -->
        <div>
          <div class="sp-relative">
            <img
              src="../assets/return1.png"
              alt=""
              class="return-btn"
              v-show="!isMobile"
              @click="
                showComponentStyleList = false;
                showComponentList = !showComponentList;
              "
            />
            <div class="area-name" v-show="!isMobile">
              {{ componentList[currentComponentIndex].areaName }}
            </div>
            <div
              class="area-name"
              v-show="isMobile"
              @click="
                showComponentStyleList = false;
                showComponentList = !showComponentList;
              "
            >
              返回
            </div>
          </div>
          <div class="color-list" v-if="isMobile == false">
            <div
              class="color-item"
              v-for="(item, index) in componentList[currentComponentIndex]
                .components"
              :key="index"
              @click="() => switchComponent(index)"
            >
              <div class="color-info">
                <div class="color-name">{{ item.styleName }}</div>
                <!-- <div class="text">当前应用</div> -->
              </div>
            </div>
          </div>
          <!-- 选择颜色——手机端 -->
          <div class="color-list" v-else-if="isMobile == true">
            <div
              class="color-item"
              v-for="(item, index) in componentList[currentComponentIndex]
                .components"
              :key="index"
              @click="() => switchComponent(index)"
            >
              <div class="color-name">{{ item.styleName }}</div>
            </div>
          </div>
        </div>
      </div></transition
    >
    <!-- 选择需要更换的颜色部位 -->
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
              v-show="!isMobile"
              @click="
                showAreaList = !showAreaList;
                showColorList = false;
              "
            />
            <div class="area-name" v-show="!isMobile">{{ currentArea }}</div>
            <div
              class="area-name"
              v-show="isMobile"
              @click="
                showAreaList = !showAreaList;
                showColorList = false;
              "
            >
              返回
            </div>
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
// import * as TWEEN from "@tweenjs/tween.js";
import "../style/style.css";

const isMobile = ref(false);
const isLoading = ref(true);
const showAreaList = ref(false);
const showColorList = ref(false); // 选择颜色
const showComponentList = ref(false); // 选择需要更换的部件
const showComponentStyleList = ref(false);

// 当前选择的部件
const currentArea = ref(null);
let currentAreaIndex = 0;
let currentComponentIndex = 0;
// 更换颜色的部位
const areaList = [
  {
    areaName: "上开关", // 开盖按钮
    modelName: "开盖按钮",
    colorName: "默认颜色",
    color: "#fff",
    material: null,
    currentcomponent: "默认款式",
    components: [],
  },
  {
    areaName: "水壶盖", // 盖子
    modelName: "盖子",
    colorName: "默认颜色",
    color: "#fff",
    material: null,
    currentcomponent: "默认款式",
    components: [],
  },
  {
    areaName: "壶体", // 把手内侧 对称_1
    colorName: "默认颜色",
    color: "#fff",
    material: null,
    currentcomponent: "默认款式",
    components: [],
  },
  {
    areaName: "把手", // 把手外侧
    colorName: "默认颜色",
    modelName: "把手外侧",
    color: "#fff",
    material: null,
    currentcomponent: "默认款式",
    components: [],
  },
  {
    areaName: "按钮", // 电源开盖
    modelName: "电源开盖",
    colorName: "默认颜色",
    color: "#fff",
    material: null,
    currentcomponent: "默认款式",
    components: [],
  },
  {
    areaName: "底座", // 挤压 克隆  路径_9 路径_6 圆柱体_1
    colorName: "默认颜色",
    color: "#fff",
    material: null,
    currentcomponent: "默认款式",
    components: [],
  },

  {
    areaName: "指示灯", // 指示灯
    colorName: "默认颜色",
    color: "#fff",
    material: null,
    currentcomponent: "默认款式",
    components: [],
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
// 更换零件的选项
const componentList = [
  {
    areaName: "壶盖",
    currentApply: "默认款式",
    components: [],
  },
  {
    areaName: "把手",
    currentApply: "默认款式",
    components: [],
  },
];
let scene;
let camera;
let renderer;
const waterRef = ref(null);
let frameId = 0;
let controls;
const controlsRef = ref(null);
// let tween;
let plane;
// 切换颜色
function switchColor(index, colorInfo) {
  areaList[index].color = colorInfo.color;
  areaList[index].colorName = colorInfo.name;
  areaList[index].material.color.set(colorInfo.color);
  // showColorList.value = true;
}
//

// 选择区域
function handleSelectArea(info, index) {
  controls.autoRotate = false;

  currentArea.value = info;
  currentAreaIndex = index;
  showAreaList.value = false;
  showComponentList.value = false;
  showComponentStyleList.value = false;
  showColorList.value = true;
}

function handleSelectComponent(index) {
  showComponentList.value = false;
  showComponentStyleList.value = true;
  currentComponentIndex = index;
}
for (let i of areaList) {
  if (i.areaName == "指示灯") {
    i.material = new THREE.MeshBasicMaterial({
      color: 0xffffff,
    });
  } else {
    i.material = new THREE.MeshPhysicalMaterial({
      color: 0xffffff,
      metalness: 0.8, // 金属度
      roughness: 0.9, // 粗糙度
      clearcoat: 0.3, // 轻漆
      clearcoatRoughness: 0.4, // 轻漆粗糙度
    });
  }
}

let waterModel;
// 切换零件
function switchComponent(index) {
  // 获取壶模型
  let areaName = componentList[currentComponentIndex].areaName;
  let components =
    componentList[currentComponentIndex].components[index].component;
  let componentToRemove = [];
  let componentToAdd = [];

  componentList[currentComponentIndex].currentApply =
    componentList[currentComponentIndex].components[index].styleName;

  if (waterModel.type == "Group") {
    waterModel.traverse(function (child) {
      if (areaName == "壶盖" && child.name == "开盖按钮") {
        for (let item = 0; item < components.length; item++) {
          if (components[item].name == child.name) {
            componentToRemove.push(child);
            componentToAdd.push(components[item].content);
          }
        }
      }
      if (areaName == "壶盖" && child.name == "盖子") {
        for (let item = 0; item < components.length; item++) {
          if (components[item].name == child.name) {
            componentToRemove.push(child);
            componentToAdd.push(components[item].content);
          }
        }
      }
      if (areaName == "把手" && child.name == "把手外侧") {
        for (let item = 0; item < components.length; item++) {
          if (components[item].name == child.name) {
            componentToRemove.push(child);
            componentToAdd.push(components[item].content);
          }
        }
      }
      if (areaName == "把手" && child.name == "把手内侧") {
        for (let item = 0; item < components.length; item++) {
          if (components[item].name == child.name) {
            componentToRemove.push(child);
            componentToAdd.push(components[item].content);
          }
        }
      }
      if (areaName == "把手" && child.name == "电源开盖") {
        for (let item = 0; item < components.length; item++) {
          if (components[item].name == child.name) {
            componentToRemove.push(child);
            componentToAdd.push(components[item].content);
          }
        }
      }
    });
    componentToRemove.forEach((child) => waterModel.remove(child));
    componentToAdd.forEach((component) => waterModel.add(component));
  }

  componentList[currentComponentIndex].currentApply =
    componentList[currentComponentIndex].components[index].styleName;
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
      preserveDrawingBuffer: true, // 生成图片必备项
      logarithmicDepthBuffer: true, // 抗锯齿
    });

    renderer.setPixelRatio(window.devicePixelRatio);
    renderer.shadowMap.enabled = true;
    renderer.shadowMap.type = THREE.PCFShadowMap; // 默认的阴影类型
    // renderer.setClearColor("#ffffff");
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
    light3.position.set(-20, 0, 4); // 背面
    scene.add(light3);

    const light5 = new THREE.DirectionalLight(0xffffff, 0.5);
    light5.position.set(0, 100, 0); // 顶光
    scene.add(light5);

    const light6 = new THREE.DirectionalLight(0xffffff, 1);
    light6.position.set(1000, 100, 1000); // 壶嘴
    scene.add(light6);
    // const light7 = new THREE.DirectionalLight(0xffffff, 1);
    // light7.position.set(-1000, -100, -1000); // 壶把
    // scene.add(light7);
    const light8 = new THREE.DirectionalLight(0xffffff, 1);
    light8.position.set(-500, 0, 0); // 背面+ 底部
    scene.add(light8);
    // const light9 = new THREE.DirectionalLight(0xffffff, 1);
    // light9.position.set(500, 200, 0); // 正面+顶部
    // scene.add(light9);
    const light4 = new THREE.DirectionalLight(0xffffff, 2.9);
    // light4.position.set(-200, 200, 40); // 正面
    light4.position.set(20, 40, 4);
    scene.add(light4);
    light4.shadow.camera.near = 0.1;
    // light4.shadow.camera.far = 250;
    light4.shadow.camera.left = -50;
    light4.shadow.camera.right = 60;
    light4.shadow.camera.top = 50;
    light4.shadow.radius = 2;
    light4.castShadow = true;
    // light4.shadow.camera.bottom = 50;

    // 接收阴影的平面

    let planeGeometry, planeMaterial;

    planeGeometry = new THREE.PlaneGeometry(500, 500); // 骨架
    planeMaterial = new THREE.MeshPhongMaterial({
      color: 0xffffff,
    });
    plane = new THREE.Mesh(planeGeometry, planeMaterial); // 网格
    plane.position.set(0, -15, 0);
    plane.receiveShadow = true;
    plane.rotation.x = (-90 * Math.PI) / 180;
    scene.add(plane);

    // 引入并渲染模型
    const loader = new FBXLoader();
    loader.load(`./model/4.fbx`, function (object) {
      isLoading.value = false;
      // console.log(object);
      object.position.set(0, -15, 0);
      waterModel = object;
      // waterModel.value = object;
      scene.add(waterModel);
      let lidStyle = [];
      let handStyle = [];
      object.traverse(function (child) {
        if (child.isMesh) {
          child.castShadow = true;

          if (child.name == "对称_1") {
            child.material = areaList[2].material;
          } else if (child.name == "把手内侧") {
            child.material = areaList[2].material;
            handStyle.push({ name: "把手内侧", content: child });
          } else if (child.name == "盖子") {
            child.material = areaList[1].material;
            lidStyle.push({ name: "盖子", content: child });
          } else if (
            child.name == "挤压" ||
            child.name == "克隆" ||
            child.name == "路径_9" ||
            child.name == "路径_6" ||
            child.name == "圆柱体_1"
          ) {
            child.material = areaList[5].material;
          } else if (child.name == "把手外侧") {
            handStyle.push({ name: "把手外侧", content: child });
            child.material = areaList[3].material;
          } else if (child.name == "电源开盖") {
            handStyle.push({ name: "电源开盖", content: child });
            child.material = areaList[4].material;
          } else if (child.name == "指示灯") {
            child.material = areaList[6].material;
          } else if (child.name == "布尔" || child.name == "不锈钢金属") {
            child.material = matter;
          } else if (child.name == "开盖按钮") {
            // 上开关
            lidStyle.push({ name: "开盖按钮", content: child });
            child.material = areaList[0].material;
          }
        }
      });
      componentList[0].components.push({
        styleName: "默认款式",
        component: [...lidStyle],
      });
      componentList[1].components.push({
        styleName: "默认款式",
        component: [...handStyle],
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

    loader.load(`./model/a.fbx`, function (object) {
      handleLoadComponent(object, "款式A");
    });
    loader.load(`./model/b.fbx`, function (object) {
      handleLoadComponent(object, "款式B");
    });
    loader.load(`./model/c.fbx`, function (object) {
      handleLoadComponent(object, "款式C");
    });
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
      metalness: 0.7, // 金属度
      roughness: 0.37, // 粗糙度
      clearcoat: 0.3, // 轻漆
      clearcoatRoughness: 0.6, // 轻漆粗糙度
    });
    // let gui = new GUI();
    // gui.add(matter, "metalness", 0.5, 2);
    // gui.add(matter, "roughness", 0, 1);
    // gui.add(matter, "clearcoat", 0, 2);
    // gui.add(matter, "clearcoatRoughness", 0.2, 0.8);

    new RGBELoader().load("./model/room3.hdr", function (texture) {
      const pmremGenerator = new THREE.PMREMGenerator(renderer);
      pmremGenerator.compileEquirectangularShader();
      const envMap = pmremGenerator.fromEquirectangular(texture).texture;
      scene.environment = envMap;
      texture.dispose();
      pmremGenerator.dispose();
    });

    render();
  }
}

function handleLoadComponent(object, styleName) {
  object.position.set(0, -15, 0);
  let lidStyle = [];
  let handStyle = [];
  object.traverse(function (child) {
    if (child.isMesh) {
      if (child.name == "开盖按钮") {
        child.material = areaList[0].material;
        child.castShadow = true;
        lidStyle.push({ name: child.name, content: child });
      } else if (child.name == "盖子") {
        child.material = areaList[1].material;
        child.castShadow = true;
        lidStyle.push({ name: child.name, content: child });
      } else if (child.name == "把手外侧") {
        child.material = areaList[3].material;
        child.castShadow = true;
        handStyle.push({ name: child.name, content: child });
      } else if (child.name == "把手内侧") {
        child.material = areaList[2].material;
        child.castShadow = true;
        handStyle.push({ name: child.name, content: child });
      } else if (child.name == "电源开盖") {
        child.material = areaList[4].material;
        child.castShadow = true;
        handStyle.push({ name: child.name, content: child });
      }
    }
  });
  componentList[0].components.push({
    styleName: styleName,
    component: [...lidStyle],
  });
  componentList[1].components.push({
    styleName: styleName,
    component: [...handStyle],
  });
}
function handleEffect() {
  controls.autoRotate = !controls.autoRotate;
}

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
// function tweenEnd() {
//   if (tween && tween.end) {
//     tween.end();
//   }
// }
function changeCamera() {
  controls.autoRotate = false;
  // tweenEnd();

  let coords = cameraList[currentCameraIndex];

  if (currentCameraIndex < cameraList.length - 1) {
    camera.position.set(coords.x, coords.y, coords.z);

    currentCameraIndex += 1;
  } else {
    camera.position.set(coords.x, coords.y, coords.z);

    currentCameraIndex = 0;
  }
}
// 重置
function handleReset() {
  // tweenEnd();

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
let logo = ref(null);

function createImage() {
  // 将logo和图片结合在一起
  const canvas = document.createElement("canvas");
  const context = canvas.getContext("2d");
  canvas.width = renderer.domElement.width;
  canvas.height = renderer.domElement.height;
  context.drawImage(renderer.domElement, 0, 0);
  context.drawImage(logo.value, 20, 20, logo.value.width, logo.value.height);
  shareImage.value = canvas.toDataURL("image/png");
  // shareImage.value = renderer.domElement.toDataURL("/image/png");
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
  // TWEEN.update();
  renderer.render(scene, camera);
};

function showCamera() {
  // console.log("camera.position:", camera.position);
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
  z-index: 6;
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
.menu-part li .img-wrap {
  width: 1rem;
  height: 1rem;
  background: rgba(0, 0, 0, 0.1);
  border-radius: 50%;
  margin-bottom: 0.1rem;
  vertical-align: top;
  box-sizing: border-box;
  padding: 0.1rem;
  display: flex;
  align-items: center;
  justify-content: center;
}
.menu-part li img {
  width: 100%;
  /* height: 100%; */
}
.menu-part li span {
  font-size: 16px;
  color: #000;
}

.menu-part .small.img-wrap {
  padding: 0.2rem;
}
.menu-part .small.img-wrap.img1 {
  padding-left: 0.25rem;
}
/* 区域颜色选项 */
.area-menu {
  position: absolute;
  right: 10px;
  top: 150px;
  bottom: 148px;
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

.fade-in {
  animation: fade-in 0.5s;
}

@keyframes fade-in {
  0% {
    opacity: 0;
    top: 100%;
  }
  100% {
    opacity: 1;
    top: 50%;
  }
}
@keyframes fade-out {
  0% {
    opacity: 1;
    top: 50%;
  }
  100% {
    opacity: 0;
    top: 100%;
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
  cursor: pointer;
}
.logo {
  position: absolute;
  top: 0.5rem;
  left: 0.5rem;
  width: 2rem;
  height: auto;
  z-index: 2;
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
  .logo2 {
    width: 20vw;
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
    /* writing-mode: vertical-lr; */
    width: 0.8rem;
    height: 100%;
    letter-spacing: 2px;
    font-size: 0.35rem;
    flex-shrink: 0;
    width: 30px;
    display: flex;
    align-items: center;
    font-size: 14px;
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
