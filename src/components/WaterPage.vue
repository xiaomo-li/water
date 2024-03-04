<template>
  <div>
    <div id="water"></div>
    <div class="page">
      <div class="selectBox">
        <div
          v-for="color in colorList"
          :key="color"
          class="btn"
          :style="{ border: 'none', backgroundColor: color }"
          @click="() => selectLidColor(color)"
        ></div>
      </div>
    </div>
  </div>
</template>

<script>
import * as THREE from "three";
import { FBXLoader } from "three/addons/loaders/FBXLoader.js";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import { RGBELoader } from "three/addons/loaders/RGBELoader.js";
import { GUI } from "three/addons/libs/lil-gui.module.min.js";
export default {
  name: "WaterPage",
  mounted() {
    this.init();
  },
  data() {
    return {
      // 色值
      colorList: [
        "#000",
        "#0c2342",
        "#003a77",
        "#dfd6c7",
        "#b90216",
        "#450d10",
        "#feee75",
        "#b8fce5",
        "#ba6e8a",
        "#36155a",
        "#0f6212",
        "#e57802",
        "#55a7bf",
        "#846535",
        "#f7f7f7",
      ],
      // 部件默认属性
      meterial: {
        area1: null,
        area2: null,
        area3: null,
        area4: null,
        area5: null,
        area6: null,
        area7: null,
        area8: null,
        area9: null,
        area10: null,
        area11: null,
        area12: null,
        area13: null,
      },
    };
  },
  methods: {
    init() {
      const scene = new THREE.Scene();
      scene.background = new THREE.Color(0xaaaaaa);
      let camera = new THREE.PerspectiveCamera(
        45,
        window.innerWidth / window.innerHeight,
        1,
        5000
      );
      camera.position.set(2500, 1000, 100);
      const renderer = new THREE.WebGLRenderer({ antialias: true });
      renderer.setSize(window.innerWidth, window.innerHeight);
      document.getElementById("water").appendChild(renderer.domElement);

      // 环境光
      const ambientLight = new THREE.AmbientLight(0xffffff, 5);
      scene.add(ambientLight);
      // 半球光：天空和地板颜色之间的渐变光
      const hemiLight = new THREE.HemisphereLight(0xffffff, 0x444444, 15);
      hemiLight.position.set(0, 200, 0);
      scene.add(hemiLight);
      // 定向光：互相平行的太阳光线
      const dirLight = new THREE.DirectionalLight(0xffffff, 5);
      dirLight.position.set(1000, 0, 0);
      dirLight.castShadow = true;
      scene.add(dirLight);
      const dirLight2 = new THREE.DirectionalLight(0xffffff, 5);
      dirLight2.position.set(-5000, 0, 0);
      dirLight2.castShadow = true;
      scene.add(dirLight2);
      const dirLight3 = new THREE.DirectionalLight(0xffffff, 5);
      dirLight3.position.set(2000, 0, 0);
      dirLight3.castShadow = true;
      scene.add(dirLight3);

      // const wheelMeterial = new THREE.MeshPhysicalMaterial({
      //   // color: 0xff0000,
      //   color: "red",
      //   metalness: 1, // 金属度
      //   roughness: 0.1, // 粗糙度,
      // });

      // 引入hdr
      let hdr = new RGBELoader().load("/studio012.hdr", function (texture) {
        scene.environment = texture;
        scene.environment.mapping = THREE.EquirectangularReflectionMapping;
        scene.background = texture;
        renderer.outputEncoding = THREE.sRGBEncoding;
        renderer.render(scene, camera);
        // wheelMeterial.envMap = texture;
        //  child.material.envMap=textureCube;
        // console.log(wheelMeterial);
      });

      const wheelMeterial = new THREE.MeshStandardMaterial({
        color: 0xff0000,
        // color: "red",
        // metalness: 1, // 金属度
        roughness: 0.1, // 粗糙度,
        envMap: hdr,
      });

      // 引入模型
      const loader = new FBXLoader();
      loader.load("/1.fbx", function (object) {
        scene.add(object);
        // console.log(object);
        object.traverse(function (child) {
          if (child.isMesh) {
            // 壶盖
            if (child.name == "Retopo_布尔_4_1") {
              child.material = wheelMeterial;
            } else if (child.name == "对称") {
              // 壶把外侧
              child.material = wheelMeterial;
            } else if (child.name == "挤压") {
              child.material = wheelMeterial;
            } else if (child.name == "克隆") {
              child.material = wheelMeterial;
            } else if (child.name == "圆柱体_1") {
              child.material = wheelMeterial;
            } else if (child.name == "空白_1") {
              child.material = wheelMeterial;
            } else if (child.name == "空白") {
              child.material = wheelMeterial;
            } else if (child.name == "Retopo_布尔_4_1_1") {
              child.material = wheelMeterial;
            } else if (child.name == "Retopo_布尔_4") {
              child.material = wheelMeterial;
            } else if (child.name == "Retopo_细分曲面_3") {
              child.material = wheelMeterial;
            } else if (child.name == "布尔") {
              child.material = wheelMeterial;
            } else if (child.name == "对称_1") {
              child.material = wheelMeterial;
            } else if (child.name == "对称_3") {
              child.material = wheelMeterial;
            }
            // child.material.envMap=textureCube;
          }
        });
      });

      // 创建灯光
      // 环境光
      // const ambientLight = new THREE.AmbientLight(0xffffff);
      // scene.add(ambientLight);
      // const hemiLight = new THREE.HemisphereLight(0xffffff, 0x444444, 1);
      // hemiLight.position.set(2000, 220, 0);
      // scene.add(hemiLight);

      // const light1 = new THREE.DirectionalLight(0xffffff, 1);
      // light1.position.set(0, 0, 100); // 壶嘴
      // scene.add(light1);
      // const light2 = new THREE.DirectionalLight(0xffffff, 1);
      // light2.position.set(0, 0, -100); // 壶把
      // scene.add(light2);

      // const light3 = new THREE.DirectionalLight(0xffffff, 1);
      // light3.position.set(200, 0, 40); // 正面
      // scene.add(light3);

      // const light4 = new THREE.DirectionalLight(0xffffff, 1);
      // light4.position.set(-200, 0, 40); // 背面
      // scene.add(light4);

      // const light5 = new THREE.DirectionalLight(0xffffff, 1);
      // light5.position.set(0, 100, 0); // 顶光
      // scene.add(light5);

      // const light6 = new THREE.DirectionalLight(0xffffff, 0.7);
      // light6.position.set(0, 0, 2000); // 壶嘴
      // scene.add(light6);
      // const light7 = new THREE.DirectionalLight(0xffffff, 0.7);
      // light7.position.set(0, 0, -2000); // 壶把
      // scene.add(light7);
      // const light8 = new THREE.DirectionalLight(0xffffff, 2);
      // light8.position.set(-100, -120, 0); // 背面+ 底部
      // scene.add(light8);
      // const light9 = new THREE.DirectionalLight(0xffffff, 2);
      // light9.position.set(100, 120, 0); // 正面+顶部
      // scene.add(light9);
      // 壶盖

      const controls = new OrbitControls(camera, renderer.domElement);
      controls.update();
      controls.minDistance = 1500;
      controls.maxDistance = 4000;

      let gui = new GUI();
      gui.add(wheelMeterial, "metalness", 0.5, 2);
      gui.add(wheelMeterial, "roughness", 0, 1);

      function animate() {
        requestAnimationFrame(animate);
        renderer.render(scene, camera);
      }
      animate();
    },
    selectLidColor(color) {
      wheelMeterial.color.set(color);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
