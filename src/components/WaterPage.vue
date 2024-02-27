<template>
  <div class="hello" id="water"></div>
</template>

<script>
import * as THREE from "three";
import { FBXLoader } from "three/addons/loaders/FBXLoader.js";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
// import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";
import { RGBELoader } from "three/addons/loaders/RGBELoader.js";
export default {
  name: "WaterPage",
  mounted() {
    this.init();
  },
  methods: {
    init() {
      const scene = new THREE.Scene();
      // scene.background = new THREE.Color(0xaaaaaa);
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
      const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
      scene.add(ambientLight);
      // 半球光：天空和地板颜色之间的渐变光
      const hemiLight = new THREE.HemisphereLight(0xffffff, 0x444444, 5);
      hemiLight.position.set(0, 200, 0);
      scene.add(hemiLight);
      // 定向光：互相平行的太阳光线
      const dirLight = new THREE.DirectionalLight(0xffffff, 5);
      dirLight.position.set(0, 500, 100);
      dirLight.castShadow = true;
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

      new RGBELoader().load("/studio012.hdr"),
        function (texture) {
          texture.mapping = THREE.EquirectangularReflectionMapping;
          scene.environment = texture;
          renderer.outputEncoding = THREE.sRGBEncoding;
          renderer.render(scene, camera);
        };

      const controls = new OrbitControls(camera, renderer.domElement);
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
