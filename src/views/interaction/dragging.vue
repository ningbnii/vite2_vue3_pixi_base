<template>
  <div class="canvas_box" ref="canvasBox">
    <div ref="myCanvas"></div>
  </div>
</template>
<script setup>
import * as PIXI from 'pixi.js'
import '@pixi/graphics-extras'
import { ref, onMounted, onUnmounted } from 'vue'

let myCanvas = ref(null)
let canvasBox = ref(null)
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

  // create a texture from an image path
  const texture = PIXI.Texture.from('assets/bunny.png')

  // 设置缩放模式
  texture.baseTexture.scaleMode = PIXI.SCALE_MODES.NEAREST

  // 创建10个精灵
  const sprites = []
  for (let i = 0; i < 10; i++) {
    const sprite = new PIXI.Sprite(texture)

    // set the initial position
    sprite.x = Math.random() * app.screen.width
    sprite.y = Math.random() * app.screen.height

    // set the initial scale
    sprite.scale.x = sprite.scale.y = Math.random() * 1.5 + 0.5

    // 设置到中心
    sprite.anchor.set(0.5)

    // set the initial rotation
    sprite.rotation = Math.random() * Math.PI

    // add to stage
    app.stage.addChild(sprite)

    // the pointer events
    sprite.interactive = true
    sprite.buttonMode = true

    // drag events
    sprite
      .on('pointerdown', onDragStart)
      .on('pointerup', onDragEnd)
      .on('pointerupoutside', onDragEnd)
      .on('pointermove', onDragMove)

    // store the sprite for later use
    sprites.push(sprite)
  }

  // onDragStart
  function onDragStart(event) {
    // store a reference to the data
    // the reason for this is because of multitouch
    // we want to track the movement of this particular touch
    this.data = event.data
    this.alpha = 0.5
    this.dragging = true
  }

  // onDragEnd
  function onDragEnd() {
    this.alpha = 1
    this.dragging = false
    // set the interaction data to null
    this.data = null
  }

  // onDragMove
  function onDragMove() {
    if (this.dragging) {
      console.log(this.data.getLocalPosition(this.parent))
      // 设置精灵的位置
      this.x = this.data.getLocalPosition(this.parent).x
      this.y = this.data.getLocalPosition(this.parent).y
    }
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
