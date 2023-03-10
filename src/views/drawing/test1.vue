<template>
  <div class="canvas_box" ref="canvasBox">
    <div ref="myCanvas"></div>
  </div>
  <div class="box" ref="box"></div>
</template>
<script setup>
import * as PIXI from 'pixi.js'
import '@pixi/graphics-extras'
import { ref, onMounted, onUnmounted } from 'vue'

let myCanvas = ref(null)
let canvasBox = ref(null)
let box = ref(null)
let app = {}

onMounted(() => {
  app = new PIXI.Application({
    width: canvasBox.value.clientWidth,
    height: canvasBox.value.clientHeight,
    backgroundColor: 0x1099bb,
    antialias: true,
    autoDensity: true,
    resolution: window.devicePixelRatio,
  })

  myCanvas.value.appendChild(app.view)

  let scale = window.devicePixelRatio
  // scale = 1

  // 创建绘制画布
  let canvas = document.createElement('canvas')
  canvas.width = app.renderer.width * scale
  canvas.height = app.renderer.height * scale
  let ctx = canvas.getContext('2d')
  ctx.fillStyle = `red`
  ctx.fillRect(0, 0, canvas.width, canvas.height)

  // 创建交互展示画布
  // 将原生canvas作为材质，并将其绘制到交互展示画布上
  let texture = PIXI.Texture.from(canvas)
  let sp = new PIXI.Sprite(texture)
  sp.name = 'preview'
  sp.width = app.renderer.width
  sp.height = app.renderer.height

  sp.interactive = true
  sp.zIndex = 2
  sp.hitArea = new PIXI.Rectangle(0, 0, app.renderer.width, app.renderer.height)
  app.stage.addChild(sp)

  // 绘画模式
  let mode = 'add' // add: 添加，erase: 擦除
  // 初始坐标
  let lastPoint = { x: 0, y: 0 }
  // 是否绘制中
  let isDrawing = false

  // 交互行为
  sp.on('pointerdown', drawStart)
  sp.on('pointermove', drawMove)
  sp.on('pointerup', drawEnd)
  let start1 = {}
  let start2 = {}

  function drawStart(event) {
    // 获取触点数量
    let pointerCount = event.data.originalEvent.touches.length
    // 如果触点数量为1，则开始绘制
    if (pointerCount === 1) {
      let r = Math.random() * 255
      let g = Math.random() * 255
      let b = Math.random() * 255
      let a = Math.random() * 1

      if (mode == 'add') {
        // ctx.lineWidth = 7
        ctx.globalCompositeOperation = 'source-over'
        // ctx.beginPath()
        ctx.strokeStyle = `rgba(${r}, ${g}, ${b}, ${a})`
        ctx.lineWidth = 1
        ctx.lineCap = 'round'
        ctx.lineJoin = 'round'
        // ctx.moveTo(event.data.global.x, event.data.global.y)
        // lastPoint = { x: event.data.global.x, y: event.data.global.y }
        // isDrawing = true
      } else {
        ctx.lineWidth = 21
        ctx.globalCompositeOperation = 'destination-out'
      }

      ctx.lineCap = 'round'
      ctx.lineJoin = 'round'
      ctx.strokeStyle = `rgba(${r}, ${g}, ${b}, ${a})`

      // 获取鼠标移动的位置
      let { x, y } = event.data.global
      lastPoint = { x, y }
      ctx.beginPath()
      ctx.moveTo(lastPoint.x * scale, lastPoint.y * scale)
      isDrawing = true
    }

    // 获取触点id
    if (pointerCount === 2) {
      start1 = {
        x: event.data.originalEvent.touches[0].clientX,
        y: event.data.originalEvent.touches[0].clientY,
      }

      start2 = {
        x: event.data.originalEvent.touches[1].clientX,
        y: event.data.originalEvent.touches[1].clientY,
      }
    }
  }

  function drawMove(event) {
    // 获取触点数量
    let pointerCount = event.data.originalEvent.touches.length
    // 如果触点数量为1，则开始绘制
    if (pointerCount === 1 && isDrawing) {
      // let { x, y } = event.data.getLocalPosition(this.parent)
      let { x, y } = event.data.global

      ctx.lineTo(x * scale, y * scale)
      ctx.stroke()
      texture.update()

      // 更新坐标点
      lastPoint = { x, y }
    }
    // 如果触点数量为2，则控制旋转、缩放、移动
    if (pointerCount === 2) {
      let end1 = {
        x: event.data.originalEvent.touches[0].clientX,
        y: event.data.originalEvent.touches[0].clientY,
      }
      let end2 = {
        x: event.data.originalEvent.touches[1].clientX,
        y: event.data.originalEvent.touches[1].clientY,
      }
      let startDistance = getDistance(start1, start2)
      let endDistance = getDistance(end1, end2)
      // 缩放
      if (
        sp.scale.x + (endDistance - startDistance) / 1000 < 5 &&
        sp.scale.x + (endDistance - startDistance) / 1000 > 0.5
      ) {
        sp.scale.x += (endDistance - startDistance) / 1000
        sp.scale.y += (endDistance - startDistance) / 1000
      }
    }
  }

  function drawEnd(event) {
    // 获取触点数量
    let pointerCount = event.data.originalEvent.touches.length
    // 如果触点数量为0，则停止绘制
    if (pointerCount === 1 && isDrawing) {
      isDrawing = false
    }
  }

  /**
   * 获取两点间距离
   */
  function getDistance(point1, point2) {
    let x = point1.x - point2.x
    let y = point1.y - point2.y
    return Math.sqrt(x * x + y * y)
  }
})

onUnmounted(() => {
  // 销毁
  app.destroy()
})
</script>
<style lang="less" scoped>
.canvas_box {
  width: 100vw;
  height: 100vh;
}
</style>
