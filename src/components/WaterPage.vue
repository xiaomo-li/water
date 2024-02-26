<template>
  <div class="hello" id="water"></div>
</template>

<script>
import * as THREE from "three";
import { FBXLoader } from "three/addons/loaders/FBXLoader.js";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
// import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";

export default {
  name: "WaterPage",
  mounted() {
    this.init();
  },
  methods: {
    init() {
      const scene = new THREE.Scene();
      scene.background = new THREE.Color(0xa0a0a0);
      const camera = new THREE.PerspectiveCamera(
        45,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      );
      camera.position.set(100, 200, 300);
      const renderer = new THREE.WebGLRenderer();
      renderer.setSize(window.innerWidth, window.innerHeight);
      let water = document.getElementById("water");
      water.appendChild(renderer.domElement);
      // 环境光
      // const light = new THREE.AmbientLight(0xffffff, 5);
      // scene.add(light);
      // 半球光：天空和地板颜色之间的渐变光
      const hemiLight = new THREE.HemisphereLight(0xffffff, 0x444444, 5);
      // hemiLight.position.set(0, 200, 0);
      scene.add(hemiLight);
      // 定向光：互相平行的太阳光线
      const dirLight = new THREE.DirectionalLight(0xffffff, 5);
      // dirLight.position.set(0, 500, 100);
      // dirLight.castShadow = true;
      // dirLight.shadow.camera.top = 180;
      // dirLight.shadow.camera.bottom = -100;
      // dirLight.shadow.camera.left = -120;
      // dirLight.shadow.camera.right = 120;
      scene.add(dirLight);

      // 引入模型
      const loader = new FBXLoader();
      loader.load("/1.fbx", function (object) {
        scene.add(object);
      });
      // window.addEventListener("resize", onWindowResize);

      // function onWindowResize() {
      //   camera.aspect = window.innerWidth / window.innerHeight;
      //   camera.updateProjectionMatrix();

      //   renderer.setSize(window.innerWidth, window.innerHeight);
      // }

      const controls = new OrbitControls(camera, renderer.domElement);
      controls.target.set(0, 100, 0);
      controls.update();

      function animate() {
        requestAnimationFrame(animate);
        renderer.render(scene, camera);
      }
      animate();
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
