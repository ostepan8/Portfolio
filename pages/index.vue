<template>
<div>
  <canvas ref = "canvas"></canvas>
    <div id="app">
        <div id = "holder" class="absolute text-white text-center max-w-2x w-full px-6" style="top: 50%; transform: translate(-50%,-50%); left: 50%;">
          <h1 id = "owen-stepan" class="opacity-0 my-2 font-rubik tracking-wide text-6xl" style="transform: translateY(30px);">Owen Stepan</h1>
          <p id = "rising" class="opacity-0 font-bebas text-7xl" style="transform: translateY(30px);">Rising Senior in Highschool</p>
          <p id = "school" class="opacity-0 font-bebas text-8xl" style="transform: translateY(30px);">Francis W Parker</p>
          <a id = "work" href ="" class="opacity-0 font-rubik text-2xl border-2 px-4 py-1 rounded-lg hover:bg-white hover:text-blue-900 hover:border-blue-900 inline-block my-2" style="transform: translateY(30px);">View Work</a>
        </div>
      </div>
      </div>
</template>

<script>
import gsap from 'gsap'
import {PlaneGeometry, BufferAttribute,Raycaster, Scene, PerspectiveCamera, WebGLRenderer, MeshPhongMaterial, DoubleSide, FlatShading, Mesh, DirectionalLight,BufferGeometry, PointsMaterial, Float32BufferAttribute,Points} from 'three'
export default {
  mounted(){
    // const dat = require('dat.gui')
    // const gui = new dat.GUI()
const world = {
  plane: {
    width: 400,
    height: 400,
    widthSegments: 50,
    heightSegments: 50
  }
}
// gui.add(world.plane, 'width',1,500).onChange(generatePlane)
// gui.add(world.plane, 'height',1,500).onChange(generatePlane)
// gui.add(world.plane, 'widthSegments',1,150).onChange(generatePlane)
// gui.add(world.plane, 'heightSegments',1,150).onChange(generatePlane)
function generatePlane(){
  planeMesh.geometry.dispose()
  planeMesh.geometry = new PlaneGeometry(world.plane.width,world.plane.height,world.plane.widthSegments,world.plane.heightSegments)
  const { array } = planeMesh.geometry.attributes.position
  const randomValues = []
  for (let i = 0; i <  array.length; i++) {
    if(i % 3 == 0){
      const x = array[i]
      const y= array [i +1]
      const z = array [i+2]
      array[i] = x + (Math.random() - 0.5)*3
      array[i+1] = y + (Math.random() -0.5)*3
      array[i+2] = z + (Math.random() -.5)*6
    }
    randomValues.push(Math.random() *Math.PI*2)
  }
  planeMesh.geometry.attributes.position.randomValues = randomValues
  planeMesh.geometry.attributes.position.originalPosition = planeMesh.geometry.attributes.position.array
  const colors = []
for (let i = 0; i < planeMesh.geometry.attributes.position.count;i++){
  colors.push(0,0.19,0.4)
}
planeMesh.geometry.setAttribute(
  'color',
  new BufferAttribute(new
    Float32Array(colors), 3)
)
}
const raycaster = new Raycaster()
const scene = new Scene();
const camera = new PerspectiveCamera(
  75,
  innerWidth/innerHeight,
  0.1,
  1000
)
const renderer = new WebGLRenderer({
  canvas: this.$refs.canvas
})
renderer.setSize(innerWidth,innerHeight)
renderer.setPixelRatio(devicePixelRatio)
const planeGeometry = new PlaneGeometry(world.plane.width,world.plane.height,world.plane.widthSegments,world.plane.heightSegments)
const planeMaterial = new MeshPhongMaterial(
  {
  side: DoubleSide,
  flatShading: FlatShading,
  vertexColors: true
})
const planeMesh = new Mesh(planeGeometry,planeMaterial)
scene.add(planeMesh)
generatePlane()
const backLight = new DirectionalLight(0xFFFFFF, 1)
console.log(backLight)
backLight.position.set(0,-1,1)
scene.add(backLight)
// const sideLight = new DirectionalLight(0xFFFFFF, 1)
// sideLight.position.set(-5,1,1)
// scene.add(sideLight)
// const sideLight2 = new DirectionalLight(0xFFFFFF, 1)
// sideLight2.position.set(5,-1,1)
// scene.add(sideLight2)
const light = new DirectionalLight(0xFFFFFF, 1)
light.position.set(0,-1,-1)
scene.add(light)
camera.position.z = 85
//points object
// const starGeometry = new BufferGeometry()
// const starMaterial = new PointsMaterial({
//   color: 0xFFFFFF
// })
// const starVertices = []
// for (let i = 0; i <15000;i++){
//   const x = (Math.random()-0.5)* 2000
//   const y = (Math.random()-0.5)* 2000
//   const z = (Math.random())* -10000
//   starVertices.push(x,y,z)
// }
// starGeometry.setAttribute('position', new Float32BufferAttribute(starVertices,3))
// const stars = new Points(starGeometry,starMaterial)
// scene.add(stars)
const mouse = {
  x: undefined,
  y: undefined
}
let frame = 0
function animate(){
  requestAnimationFrame(animate)
  frame += 0.1
  const { array, originalPosition, randomValues} = planeMesh.geometry.attributes.position
  renderer.render(scene, camera)
  //planeMesh.rotation.x += 0.01
  for (let i =0; i< array.length; i+=3){
    array[i] = originalPosition[i]+Math.cos(frame + randomValues[i]) * 0.05 
    array[i+1] = originalPosition[i+1]+Math.sin(frame + randomValues[i+1]) * 0.05
  }
  planeMesh.geometry.attributes.position.needsUpdate = true
  raycaster.setFromCamera(mouse,camera)
  const intersects = raycaster.intersectObject(planeMesh)
  if(intersects.length > 0){
    const {color} = intersects[0].object.geometry.attributes
//vert 1
    color.setX(intersects[0].face.a,0.1)
    color.setY(intersects[0].face.a,0.5)
    color.setZ(intersects[0].face.a,1)
// vert 2
    color.setX(intersects[0].face.b,0.1)
    color.setY(intersects[0].face.b,0.5)
    color.setZ(intersects[0].face.b,1)
// vert 3
    color.setX(intersects[0].face.c,0.1)
    color.setY(intersects[0].face.c,0.5)
    color.setZ(intersects[0].face.c,1)
    intersects[0].object.geometry.attributes.color.needsUpdate = true
    const initialColor = {
      r: 0,
      g: 0.19,
      b: 0.4
    }
    const hoverColor= {
      r: 0.1,
      g: 0.5,
      b: 1
    }
    gsap.to(hoverColor,{
      r: initialColor.r,
      g: initialColor.g,
      b: initialColor.b,
      onUpdate: ()=>{
        //vert 1
          color.setX(intersects[0].face.a,hoverColor.r)
          color.setY(intersects[0].face.a,hoverColor.g)
          color.setZ(intersects[0].face.a,hoverColor.b)
      // vert 2
          color.setX(intersects[0].face.b,hoverColor.r)
          color.setY(intersects[0].face.b,hoverColor.g)
          color.setZ(intersects[0].face.b,hoverColor.b)
      // vert 3
          color.setX(intersects[0].face.c,hoverColor.r)
          color.setY(intersects[0].face.c,hoverColor.g)
          color.setZ(intersects[0].face.c,hoverColor.b)
          intersects[0].object.geometry.attributes.color.needsUpdate = true
     
      }
    })
  }
  // stars.rotation.x += 0.0005
}
animate()
//normalized mouse placements
addEventListener('mousemove', ()=>{
  mouse.x = (event.clientX / innerWidth) * 2 -1
  mouse.y = (event.clientY / innerHeight) *-2 + 1
})
gsap.to('#owen-stepan',{
  opacity:1,
  duration: 1.25,
  y:0,
  delay: .3,
  ease:'expo'
})
gsap.to('#rising',{
  opacity:1,
  duration: 1.25,
  delay: .6,
  y:0,
  ease: 'expo'
})
gsap.to('#school',{
  opacity:1,
  duration: 1.25,
  delay: .9,
  y:0,
  ease: 'expo'
})
gsap.to('#work',{
  opacity:1,
  duration: 1.25,
  delay: 1.2,
  y:0,
  ease: 'expo'
})
document.querySelector('#work').addEventListener('click',(e)=>{
  e.preventDefault()
  gsap.to('#holder',{
    opacity: 0
  })
    gsap.to(light.position,{
    z: -25,
    delay: 0.15,
    duration: 1.75,
    ease:'circ.in'
  })
  gsap.to(backLight.position,{
    z: -25,
    delay: 0.15,
    duration: 1.75,
    ease:'circ.in',
    onComplete: ()=>{
      this.$router.push('/work')
    }
  })
})
addEventListener('resize',()=>{
  camera.aspect = innerWidth/innerHeight
  camera.updateProjectionMatrix()
  renderer.setSize(innerWidth,innerHeight)
})
  }
}
</script>
<style>
.font-rubik{
    font-family: 'Lato', sans-serif;
}
.font-bebas{
  font-family: 'Lato', sans-serif;
}
</style>