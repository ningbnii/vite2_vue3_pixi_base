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
  // 创建PIXI应用程序
  const container = new PIXI.Container()
  // 设置container的中心点为屏幕中心点
  // container.pivot.set(app.screen.width / 2, app.screen.height / 2)
  app.stage.addChild(container)

  // 加载纹理并创建一个可拖动的精灵
  const sprite = PIXI.Sprite.from('assets/eggHead.png')
  sprite.anchor.set(0.5)
  sprite.x = app.screen.width / 2
  sprite.y = app.screen.height / 2
  sprite.interactive = true
  sprite.buttonMode = true
  container.addChild(sprite)

  // 定义变量
  let isDragging = false
  let previousPosition = null
  let distance = 0
  let rotation = 0
  let scale = { x: 1, y: 1 }

  // 监听触摸事件
  app.renderer.plugins.interaction.on('pointerdown', (event) => {
    if (event.data.pointerType === 'touch') {
      if (event.data.originalEvent.touches.length === 2) {
        isDragging = true
        previousPosition = event.data.originalEvent.touches
        distance = getDistance(previousPosition)
        rotation = getRotation(previousPosition)
      }
    }
  })

  app.renderer.plugins.interaction.on('pointermove', (event) => {
    if (isDragging && event.data.originalEvent.touches.length === 2) {
      const currentPosition = event.data.originalEvent.touches
      const newDistance = getDistance(currentPosition)
      const newRotation = getRotation(currentPosition)
      // 设置container的中心点为两个触摸点的中心点
      const center = getCenter(currentPosition)
      // container.pivot.set(center.x, center.y)

      const deltaScale = newDistance / distance
      const deltaRotation = newRotation - rotation

      scale.x *= deltaScale
      scale.y *= deltaScale

      sprite.scale.set(scale.x, scale.y)
      sprite.rotation += deltaRotation

      // 计算移动距离并应用到精灵位置
      const dx = currentPosition[0].clientX - previousPosition[0].clientX
      const dy = currentPosition[0].clientY - previousPosition[0].clientY
      sprite.x += dx
      sprite.y += dy

      previousPosition = currentPosition
      distance = newDistance
      rotation = newRotation
    }
  })

  app.renderer.plugins.interaction.on('pointerup', (event) => {
    if (event.data.pointerType === 'touch') {
      if (event.data.originalEvent.touches.length !== 2) {
        isDragging = false
        previousPosition = null
        distance = 0
        rotation = 0
      }
    }
  })

  // 计算两个点之间的距离
  function getDistance(touches) {
    const dx = touches[1].clientX - touches[0].clientX
    const dy = touches[1].clientY - touches[0].clientY
    return Math.sqrt(dx * dx + dy * dy)
  }

  // 计算两个点之间的旋转角度
  function getRotation(touches) {
    const dx = touches[1].clientX - touches[0].clientX
    const dy = touches[1].clientY - touches[0].clientY
    return Math.atan2(dy, dx)
  }

  // 计算所有触摸位置的中心点
  function getCenter(positions) {
    let x = 0
    let y = 0
    for (let i = 0; i < positions.length; i++) {
      x += positions[i].x
      y += positions[i].y
    }
    return new PIXI.Point(x / positions.length, y / positions.length)
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
