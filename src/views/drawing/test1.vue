<template>
  <div class="canvas_box" ref="canvasBox">
    <div ref="myCanvas"></div>
  </div>
  <div class="box" ref="box"></div>
</template>
<script setup>
import * as PIXI from 'pixi.js'
// import hammerjs
import * as Hammer from 'hammerjs'
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
    resolution: window.devicePixelRatio || 1,
  })

  myCanvas.value.appendChild(app.view)

  let container = new PIXI.Container()
  container.interactive = true
  container.hitArea = new PIXI.Rectangle(
    0,
    0,
    app.screen.width,
    app.screen.height
  )
  app.stage.addChild(container)

  let scale = window.devicePixelRatio || 1
  // scale = 1

  // 创建绘制画布
  let canvas = document.createElement('canvas')
  canvas.width = app.screen.width
  canvas.height = app.screen.height
  let ctx = canvas.getContext('2d')
  ctx.fillStyle = `red`
  ctx.fillRect(0, 0, canvas.width, canvas.height)

  // 创建交互展示画布
  // 将原生canvas作为材质，并将其绘制到交互展示画布上
  let texture = PIXI.Texture.from(canvas)
  let sp = new PIXI.Sprite(texture)
  sp.name = 'preview'
  sp.width = app.screen.width
  sp.height = app.screen.height

  sp.interactive = true
  sp.zIndex = 2
  sp.hitArea = new PIXI.Rectangle(0, 0, app.screen.width, app.screen.height)
  container.addChild(sp)

  // 创建hammer.js实例，绑定到renderer.view
  const mc = new Hammer.Manager(app.view)
  // 添加旋转手势
  let rotate = new Hammer.Rotate()
  mc.add(rotate)
  // 添加缩放手势
  let pinch = new Hammer.Pinch()
  mc.add(pinch)

  let pan = new Hammer.Pan({ pointers: 2 }) // 2指拖动
  mc.add(pan)
  // 旋转和缩放同时触发
  pinch.recognizeWith([rotate, pan])
  rotate.recognizeWith([pan])

  // 添加双指拖动手势
  // 保存拖动的起始位置
  let panStart = null
  mc.on('panstart', function (e) {
    // 获取拖动的起始位置
    const center = e.center
    panStart = {
      x: center.x - app.view.offsetLeft,
      y: center.y - app.view.offsetTop,
    }
  })
  mc.on('panmove', function (e) {
    // 获取拖动的位置
    const center = e.center
    const panEnd = {
      x: center.x - app.view.offsetLeft,
      y: center.y - app.view.offsetTop,
    }
    // 计算拖动的距离
    const dx = panEnd.x - panStart.x
    const dy = panEnd.y - panStart.y

    container.position.x += dx
    container.position.y += dy
    // 更新拖动的起始位置
    panStart = panEnd
  })
  mc.on('panend', function (e) {
    // 获取拖动的位置
    panStart = null
  })

  // 处理缩放事件
  let initialScale = null
  mc.on('pinchstart', function (e) {
    initialScale = container.scale.x
  })

  mc.on('pinchmove', function (e) {
    //计算缩放因子
    const newScale = e.scale * initialScale
    //计算中心点
    const center = new PIXI.Point(e.center.x, e.center.y)
    //将中心点从屏幕坐标转换为容器坐标
    const localPoint = container.toLocal(center)
    //缩放容器
    container.scale.set(newScale)
    //计算缩放后的中心点
    const newCenter = container.toGlobal(localPoint)
    //将容器的中心点移动到新中心点
    container.position.x += center.x - newCenter.x
    container.position.y += center.y - newCenter.y
    // 移动容器位置
  })
  mc.on('pinchend', function (e) {
    initialScale = null
  })

  // 处理旋转事件
  // 保存旋转的中心点
  let rotationCenter = null
  // 保存旋转的角度
  let preRotation = 0

  mc.on('rotatestart', function (e) {
    const center = e.center
    rotationCenter = {
      x: center.x - app.view.offsetLeft,
      y: center.y - app.view.offsetTop,
    }
    preRotation = e.rotation
  })
  // 当双指旋转手势进行时，旋转图像
  mc.on('rotatemove', function (event) {
    if (rotationCenter) {
      // 计算旋转角度
      let angle = event.rotation * (Math.PI / 180)
      let preRotationDeg = preRotation * (Math.PI / 180)
      // 计算旋转中心点
      let center = new PIXI.Point(rotationCenter.x, rotationCenter.y)
      // 将中心点从屏幕坐标转换为容器坐标
      let localPoint = container.toLocal(center)
      // 计算旋转变化量
      let rotationDiff = angle - preRotationDeg
      // 旋转容器
      container.rotation += rotationDiff
      // 计算旋转后的中心点
      let newCenter = container.toGlobal(localPoint)
      // 将容器的中心点移动到新中心点
      container.position.x += center.x - newCenter.x
      container.position.y += center.y - newCenter.y
    }
    preRotation = event.rotation
  })

  // 当双指旋转手势结束时，清空旋转中心点
  mc.on('rotateend', function () {
    rotationCenter = null
    preRotation = 0
  })

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
        ctx.lineWidth = 20
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

      // 画布坐标转换为容器坐标
      let { x, y } = event.data.getLocalPosition(this.parent)

      // 更新坐标点
      lastPoint = { x, y }
      ctx.beginPath()
      ctx.moveTo(lastPoint.x, lastPoint.y)
      isDrawing = true
    }
  }

  function drawMove(event) {
    // 获取触点数量
    let pointerCount = event.data.originalEvent.touches.length
    // 如果触点数量为1，则开始绘制
    if (pointerCount === 1 && isDrawing) {
      let { x, y } = event.data.getLocalPosition(this.parent)

      ctx.lineTo(x, y)
      ctx.stroke()
      texture.update()

      // 更新坐标点
      lastPoint = { x, y }
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
