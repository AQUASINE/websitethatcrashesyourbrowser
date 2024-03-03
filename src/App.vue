<script>
import * as THREE from 'three'
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
import { UnrealBloomPass } from "three/examples/jsm/postprocessing/UnrealBloomPass.js";
import { EffectComposer } from "three/examples/jsm/postprocessing/EffectComposer.js";
import { RenderPass } from "three/examples/jsm/postprocessing/RenderPass.js";
import { OutputPass } from "three/examples/jsm/postprocessing/OutputPass.js";
import { ShaderPass } from "three/examples/jsm/postprocessing/ShaderPass.js";
import { FXAAShader } from "three/examples/jsm/shaders/FXAAShader.js";



export default {
  methods: {
    saveAndCrash() {
      if (this.isCrashing) {
        return;
      }
      localStorage.setItem('state', this.state + 1);
      this.$nextTick(() => {
        this.crash();
      });
    },
    crash() {
      this.audio.addEventListener('ended', () => {
        this.freezeBrowser();
      });
      this.audio.play();
    },
    freezeBrowser() {
      this.isCrashing = true;
      console.log([...Array(2 ** 32 - 1)].map(_ => Math.ceil(Math.random() * 111)))

      txt = "uh oh! ";
      for (var i = 0; i === i; i++) {
        txt = txt += " oh man... oh no.... oh dear...";
        console.log(i, txt);
      }

      this.freezeBrowser();
    },
  },
  computed: {
    message() {
      switch (this.state) {
        case 0:
          return "hi! im puappey! i  ;am dog !<br/>i will work hard tu crash web site for yu! :D";
        case 1:
          return "wasn't that fun? i   worked hard !<br>hard tu crashh whole  browser . .but i did my best ! :D";
        case 2:
          return "wow ! i didn t think yu would do it again !<br/>that was exhausting !";
        case 3:
          return "yu are really      persistent !<br/>i am  tired now !   plase   stop   !";
        case 4:
          return "plase  stop                    !                                    ";
        case 5:
          return "the              last   of     my               streingth                                                                                ";
        case 6:
          return "if            yu      keep          forcing      me      tu       do            this         for              yu                                            i                             will     have      tu                          give     it         all                               away                                                                                                                                                                     ";
        case 7:
          return "You                have                no                  heart                        for                    a                               poor                      dog                         like                    me                                                                                                           ";
        default:
          return "";
      }
    }
  },
  data() {
    return {
      mouseX: 0,
      mouseY: 0,
      state: 0,
      isCrashing: false,
      audio: new Audio('/musicnote8.wav'),
      messageLetterByLetter: "",
    }
  },
  mounted() {
    console.log("made by AQUASINE");
    setInterval(() => {
      if (this.isCrashing) {
        this.freezeBrowser();
      }
    }, 10);

    setInterval(() => {
      if (this.messageLetterByLetter.length < this.message.length) {
        this.messageLetterByLetter += this.message[this.messageLetterByLetter.length];
      }
    }, 50);

    // load state from local storage
    // parse it as an integer
    this.state = parseInt(localStorage.getItem('state')) || 0;

    if (this.state > 5) {
      this.audio = new Audio('/menu_back.wav');
    }

    window.addEventListener('mousemove', (e) => {
      this.mouseX = e.clientX;
      this.mouseY = e.clientY;
    });

    this.mouseX = window.innerWidth / 5;
    this.mouseY = window.innerHeight / 2;

    window.addEventListener('keydown', (e) => {
      console.log(e.key);
      if (e.ctrlKey && e.shiftKey && e.altKey && e.key === 'G') {
        this.state = 0;
        localStorage.setItem('state', 0);
      }
    });

    window.addEventListener('resize', () => {
      this.$refs.threejscontainer.removeChild(this.$refs.threejscontainer.children[0]);
      this.$nextTick(() => {
        this.$refs.threejscontainer.appendChild(renderer.domElement);
      })

      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth / 2, window.innerHeight / 2);
      composer.setSize(window.innerWidth / 2, window.innerHeight / 2);
    });

    let backgroundColor = getComputedStyle(document.documentElement).getPropertyValue('--bg1');
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(backgroundColor);


    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth / 2, window.innerHeight / 2);
    this.$nextTick(() => {
      this.$refs.threejscontainer.appendChild(renderer.domElement);
    })

    const light = new THREE.AmbientLight(0x404040, 4); // soft white light
    scene.add(light);
    const pointLight = new THREE.PointLight(0xffffff, 25, 200);
    pointLight.position.set(5, 0, 3);
    scene.add(pointLight);
    camera.position.x = 8;
    camera.position.z = 2;
    camera.rotation.y = 20;

    const pointLight2 = new THREE.PointLight(0xffffff, 100, 200);
    pointLight2.position.set(3, 2, 3);
    scene.add(pointLight2);

    const pointLight3 = new THREE.PointLight(0xffffff, 50, 200);
    pointLight3.position.set(3, 2, -3);
    scene.add(pointLight3);


    let dog;
    let head;
    const loader = new GLTFLoader();
    loader.load("/samoyed.glb", (gltf) => {
      dog = gltf.scene;
      scene.add(dog);

      head = dog.children[0];


      let toHide = [];
      if (this.state == 7) {
        toHide = ["Cube001"]
      } else if (this.state >= 8) {
        toHide = ["Cube001", "Cube002", "Cube003", "Sphere"]
      } 
      head.children.forEach(child => {
          if (toHide.includes(child.name)) {
            child.visible = false;
          }
        });

    }, undefined, (error) => {
      console.error(error);
    });


    // Postprocessing
    const composer = new EffectComposer(renderer);
    const renderPass = new RenderPass(scene, camera);
    const bloomPass = new UnrealBloomPass(new THREE.Vector2(window.innerWidth / 2, window.innerHeight / 2), 1.5, 0.4, 0.85);
    bloomPass.threshold = 0.2
    bloomPass.strength = 0.04;
    bloomPass.radius = 0;
    const outputPass = new OutputPass();

    composer.addPass(renderPass);
    composer.addPass(bloomPass);
    composer.addPass(outputPass);

    const animate = () => {
      requestAnimationFrame(animate);
      if (head) {
        if (this.state >= 3 && this.state < 6){
          camera.rotation.y = Math.sin(Date.now() * 0.0005) * 0.003 + 20;
          head.rotation.x = Math.sin(Date.now() * 0.001) * 0.02 + 3.3;
          head.rotation.y = Math.sin(Date.now() * 0.001) * 0.01 + -0.4;
          head.rotation.z = 0.4;
          head.position.x = -0.8;
          head.position.y = 0.2;
          head.position.z = 0.4;
        } else if (this.state >= 6 && this.state < 8) {
          head.rotation.x = 3.6;
          head.rotation.y = 0.4;
          head.rotation.z = 0.6;
          head.position.x = -0.8;
          head.position.y = 0.2;
          head.position.z = 0.4;
        } else if (this.state >= 8) {
          head.rotation.x = 3.3;
          head.rotation.y = 0.2;
          head.rotation.z = 0.2;
          head.position.x = -0.8;
          head.position.y = 0.2;
          head.position.z = 0.4;
        }
         
         else {          
          camera.rotation.y = Math.sin(Date.now() * -0.0005) * 0.003 + 20;
          head.rotation.x = Math.sin(Date.now() * -0.004) * 0.07 + 3.5;
          head.rotation.y = Math.sin(Date.now() * -0.004) * 0.02 + -(this.mouseX - window.innerWidth / 2) / 1400 + -0.4;
          head.rotation.z = (this.mouseY - window.innerHeight / 1) / 1000 + 0.4;
          head.position.x = -1.2;
          head.position.y = 0.0;
          head.position.z = 0.4;
        }
      }

      if (this.isCrashing) {
        this.crash();
      }

      composer.render(scene, camera);
    };
    animate();

    if (window.location.href.includes("crash=true")) {
      this.crash();
    }
  }
}
</script>

<template>
  <div class="container__crash-button">
    <div class="info__crash" >
      <div class="message" v-html="messageLetterByLetter" :class="{timesnewroman: state > 6}">
      </div>
      <button class="button__crash" v-if="message === messageLetterByLetter" @click="saveAndCrash" :class="{timesnewroman: state > 6}" >Crash</button>
    </div>
  </div>
  <div ref="threejscontainer" class="threecontainer">
  </div>
</template>

<style scoped>
.container__crash-button {
  position: absolute;
  top: -100px;
  left: 500px;
  width: 800px;
  height: 600px;
  background-image: url("/msg.png");
  z-index: 1;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 1em;
}

.button__crash {
  margin-top: 1em;
}

.timesnewroman {
  font-family: 'Times New Roman', Times, serif !important;
}

.info__crash {
  display: flex;
  flex-direction: column;
  align-items: center;
  position: absolute;
  font-size: large;
  font-family: 'Comic Sans MS', cursive, sans-serif;
  transform: scaleY(1.5);
  max-width: 600px;
  margin-top: -50px;
}

.message {
  max-width: 400px;
  text-align: center;
}

.threecontainer {
  transform: scale(2) translateY(25%) translateX(10%);
  image-rendering: pixelated !important;
  position: absolute;
  z-index: 0;
}
</style>
