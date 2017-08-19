<template>
  <div class="main">
    <div class="kill-list">
      <kill-view v-for="obj in killLog" :data="obj" :key="obj.killID"></kill-view>
    </div>
    <div class="map">
      <div id="canvas-container"></div>
    </div>
    <div class="status">
      <icon name="refresh" scale="2" spin v-if="state == states.LOADING" class="loading"></icon>
    </div>
  </div>
</template>

<script>
import KillView from './kill-view'
var THREE = require('three')

export default {
  components: { KillView },
  name: 'hello',
  data () {
    return {
      killLog: [],
      queueID: 'zkill-background',
      state: 1,
      states: {
        LOADING: 0,
        NONE: 1,
        ERROR: 2
      }
    }
  },
  methods: {
    getData: function () {
      var self = this
      var request = new XMLHttpRequest()

      request.open('GET', 'https://redisq.zkillboard.com/listen.php?queueID=' + this.queueID, true)

      request.onload = function () {
        if (request.status >= 200 && request.status < 400) {
          var data = JSON.parse(request.responseText)

          if (typeof data.package !== 'undefined' && data.package !== null) {
            self.killLog.unshift(data)
            self.killLog = self.killLog.slice(0, 25)
          }
        } else {
          // We reached our target server, but it returned an error

        }

        self.state = self.states.NONE

        self.getData()
      }

      request.onerror = function () {
        self.state = self.states.ERROR
      }

      setTimeout(function () {
        request.send()
        self.state = self.states.LOADING
      }, 1000)
    }
  },
  mounted () {
    this.getData()

    var camera, scene, renderer, mesh, material

    renderer = new THREE.WebGLRenderer()

    var container = document.getElementById('canvas-container')
    if (container !== null) {
      var w = container.offsetWidth
      var h = container.offsetHeight
      renderer.setSize(w, h)
      container.appendChild(renderer.domElement)

      camera = new THREE.PerspectiveCamera(70, w / h, 1, 1000)
      camera.position.z = 400

      // Create scene.
      scene = new THREE.Scene()

      // Create material
      material = new THREE.MeshPhongMaterial()

      // Create cube and add to scene.
      var geometry = new THREE.BoxGeometry(200, 200, 200)
      mesh = new THREE.Mesh(geometry, material)
      scene.add(mesh)

      // Create ambient light and add to scene.
      var light = new THREE.AmbientLight(0x404040) // soft white light
      scene.add(light)

      // Create directional light and add to scene.
      var directionalLight = new THREE.DirectionalLight(0xffffff)
      directionalLight.position.set(1, 1, 1).normalize()
      scene.add(directionalLight)

      var animate = function () {
        requestAnimationFrame(animate)
        mesh.rotation.x += 0.005
        mesh.rotation.y += 0.01
        renderer.render(scene, camera)
      }

      animate()
    }
//    var renderer = new THREE.WebGLRenderer({canvas: document.getElementById('myCanvas'), antialias: true})
//    renderer.setClearColor(0x00ff00)
//    renderer.setPixelRatio(window.devicePixelRatio)
//    renderer.setSize(window.innerWidth, window.innerHeight)
//
// // CAMERA
//    var camera = new THREE.PerspectiveCamera(35, window.innerWidth / window.innerHeight, 0.1, 3000)
// // SCENE
//    var scene = new THREE.Scene()
//
// // LIGHTS
//    var light = new THREE.AmbientLight(0xffffff, 0.5)
//    scene.add(light)
//
//    var light1 = new THREE.PointLight(0xffffff, 0.5)
//    scene.add(light1)
//
// // OBJECT
//    var geometry = new THREE.CubeGeometry(100, 100, 100)
//    var material = new THREE.MeshLambertMaterial({color: 0xF3FFE2})
//    var mesh = new THREE.Mesh(geometry, material)
//    mesh.position.set(0, 0, -2500)
//
//    scene.add(mesh)
//
// // RENDER LOOP
//    requestAnimationFrame(render)
//
//    function render () {
//      mesh.rotation.x += 0.01
//      mesh.rotation.y += 0.01
//      renderer.render(scene, camera)
//      requestAnimationFrame(render)
//    }
  }
}
</script>

<style scoped="">
  .main {
    display: grid;
    grid-template-columns: 275px 1fr 32px;
    grid-template-rows: 1fr 32px;
    grid-gap: 3px;
    color: whitesmoke;

    /* we don't want ot have a scroll bar */
    height: 100vh;
    width: 100vw;
    position: absolute;
    top: 0;
    left: 0;
    overflow: hidden;
  }

  .kill-list {
    width: 275px;
    overflow: hidden;
  }

  .status {
    grid-column: 3;
    grid-row: 2;
  }

  #canvas-container {
    width: 250px;
    height: 250px;
  }

  .loading {
    color: dimgray;
    opacity: 0.1;
  }
</style>