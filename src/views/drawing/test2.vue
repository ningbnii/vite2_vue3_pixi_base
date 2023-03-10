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
// import gsap
// import gsap from 'gsap'
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
  // 创建PIXI应用程序
  const container = new PIXI.Container()
  container.interactive = true
  container.hitArea = new PIXI.Rectangle(
    0,
    0,
    app.screen.width,
    app.screen.height
  )
  app.stage.addChild(container)

  // 加载纹理并创建一个可拖动的精灵
  const sprite = PIXI.Sprite.from('assets/eggHead.png')
  // sprite设置位于container的中心点
  // sprite.anchor.set(0.5)
  // sprite.position.set(app.screen.width / 2, app.screen.height / 2)

  container.addChild(sprite)

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
