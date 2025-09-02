<template>
  <div class="project-list" ref="canvasContainer"></div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, computed, watch, nextTick } from 'vue'
import * as THREE from 'three'
import { RGBELoader } from 'three/examples/jsm/loaders/RGBELoader.js'
import { useThemeStore } from '../store'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'

const canvasContainer = ref<HTMLElement | null>(null)

const themeStore = useThemeStore()
const theme = computed(() => themeStore.theme)
const isSceneReady = ref<boolean>(false)

let scene: THREE.Scene
let myModel: THREE.Group | null = null
const glassObjects: THREE.Mesh[] = []

watch(theme, newTheme => {
  let newColor
  if (newTheme === 'light') {
    newColor = 0xf5f5f5
  } 

  if (newTheme === 'dark') {
    newColor = 0x121212
  }

  scene.background = new THREE.Color(newColor)
})

onMounted(() => {
  if (!canvasContainer.value) return

  scene = new THREE.Scene()
  scene.background = new THREE.Color(0xf5f5f5)

  const camera = new THREE.PerspectiveCamera(80, window.innerWidth / window.innerHeight, 0.1, 1000)
  camera.position.set(0, 2, 7)

  const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: false })
  renderer.setSize(window.innerWidth, window.innerHeight)
  renderer.setPixelRatio(window.devicePixelRatio)
  renderer.physicallyCorrectLights = true
  renderer.outputEncoding = THREE.sRGBEncoding
  canvasContainer.value.appendChild(renderer.domElement)

  // Освещение
  const ambientLight = new THREE.AmbientLight(0xffffff, 0.5)
  scene.add(ambientLight)

  const directionalLight = new THREE.DirectionalLight(0xffffff, .5)
  directionalLight.position.set(5, 5, 5)
  scene.add(directionalLight)

  // HDRI для отражений
  new RGBELoader().load(
    'https://raw.githubusercontent.com/mrdoob/three.js/dev/examples/textures/equirectangular/venice_sunset_1k.hdr',
    (texture) => {
      texture.mapping = THREE.EquirectangularReflectionMapping
      scene.environment = texture
    }
  )

  const loader = new GLTFLoader()
  loader.load(
    '/model.glb', // Путь к вашей модели
    (gltf) => {
      myModel = gltf.scene
      myModel.traverse((child) => {
        if (child instanceof THREE.Mesh) {
          child.material = (child.material as THREE.MeshPhysicalMaterial).clone()
          child.material.transparent = true
          child.material.opacity = 0.8
          child.material.transmission = 0.7 // Убедитесь, что модель поддерживает transmission
          child.material.needsUpdate = true
        }
      })
      createGrid()
      isSceneReady.value = true
      nextTick(() => applyBackgroundFromLocalStorage())
    },
    undefined,
    (error) => console.error('Error loading my_model.glb:', error)
  )

  // Сетка стекляшек
  const createGrid = () => {
    if (!myModel) return

    glassObjects.forEach((obj) => scene.remove(obj))
    glassObjects.length = 0

    const isMobile = window.innerWidth < 768
    const columns = isMobile ? 1 : 3
    const rows = 3
    const spacing = 5

    for (let row = 0; row < rows; row++) {
      for (let col = 0; col < columns; col++) {
        const glass = myModel.clone() as THREE.Mesh
        glass.position.x = col * spacing - (columns - 1) * spacing / 2
        glass.position.y = -row * spacing + (rows - 1) * spacing / 2
        glass.position.z = 0
        glass.scale.set(1, 1, 1) // Настройте масштаб под вашу модель
        scene.add(glass)
        glassObjects.push(glass)
      }
    }
  }

  createGrid()

  // Обработка движения мыши
  const raycaster = new THREE.Raycaster()
  const mouse = new THREE.Vector2()
  const onMouseMove = (event: MouseEvent) => {
    mouse.x = (event.clientX / window.innerWidth) * 2 - 1
    mouse.y = -(event.clientY / window.innerHeight) * 2 + 1
  }
  window.addEventListener('mousemove', onMouseMove)

  // Анимация
  const animate = () => {
    requestAnimationFrame(animate)

    // Обновление наклона стекляшек
    raycaster.setFromCamera(mouse, camera)
    glassObjects.forEach((glass) => {
      const vector = new THREE.Vector3()
      glass.getWorldPosition(vector)
      vector.project(camera)

      const distanceX = mouse.x - vector.x
      const distanceY = vector.y - mouse.y 
      const maxTilt = Math.PI / 24 // Уменьшенный угол наклона (15°)
      const tiltFactor = 2

      glass.rotation.y = distanceX * maxTilt * tiltFactor
      glass.rotation.x = distanceY * maxTilt * tiltFactor
    })

    renderer.render(scene, camera)
  }
  animate()

  // Адаптивность
  const handleResize = () => {
    camera.aspect = window.innerWidth / window.innerHeight
    camera.updateProjectionMatrix()
    renderer.setSize(window.innerWidth, window.innerHeight)
    createGrid()
  }

  window.addEventListener('resize', handleResize)

  // Очистка
  onUnmounted(() => {
    window.removeEventListener('resize', handleResize)
    window.removeEventListener('mousemove', onMouseMove)
    renderer.dispose()
  })
})
</script>

<style lang="scss" scoped>
.project-list {
  display: flex;
  position: absolute;
  width: 100%;

}
</style>