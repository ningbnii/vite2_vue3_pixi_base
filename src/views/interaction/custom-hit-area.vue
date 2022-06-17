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
    // resolution: window.devicePixelRatio || 1,
  })

  myCanvas.value.appendChild(app.view)

  // create a texture
  const yellowstar = PIXI.Texture.from('assets/yellowstar.png')

  // create a new Sprite using the texture
  const starButton1 = new PIXI.Sprite(yellowstar)

  // set the position
  starButton1.x = 50
  starButton1.y = 200

  // 设置可以点击
  starButton1.interactive = true
  starButton1.buttonMode = true

  // pointerdown
  starButton1.on('pointerdown', (event) => onPointerDown(starButton1))

  // pointerover
  starButton1.on('pointerover', (event) => onPointerOver(starButton1))

  // pointerout
  starButton1.on('pointerout', (event) => onPointerOut(starButton1))

  // create a new Sprite using the texture
  const starButton2 = new PIXI.Sprite(yellowstar)

  // set the position
  starButton2.position.set(250, 200)

  // create a hitarea that matches the size of the sprite
  starButton2.hitArea = new PIXI.Polygon([
    80, 0, 100, 50, 160, 55, 115, 95, 130, 150, 80, 120, 30, 150, 45, 95, 0, 55,
    60, 50,
  ])

  // set the button interactive state
  starButton2.interactive = true
  starButton2.buttonMode = true

  // pointerdown
  starButton2.on('pointerdown', (event) => onPointerDown(starButton2))

  // pointerover
  starButton2.on('pointerover', (event) => onPointerOver(starButton2))

  // pointerout
  starButton2.on('pointerout', (event) => onPointerOut(starButton2))

  // with mask, no hit area
  const starButton3 = new PIXI.Sprite(yellowstar)

  // set the position
  starButton3.position.set(450, 200)

  const squareMask = new PIXI.Graphics()
    .beginFill(0xffffff)
    .drawRect(starButton3.x, starButton3.y, 75, 200)
    .endFill()
  starButton3.mask = squareMask

  // set the button interactive state
  starButton3.interactive = true
  starButton3.buttonMode = true

  // pointerdown
  starButton3.on('pointerdown', (event) => onPointerDown(starButton3))

  // pointerover
  starButton3.on('pointerover', (event) => onPointerOver(starButton3))

  // pointerout
  starButton3.on('pointerout', (event) => onPointerOut(starButton3))

  const starButton4 = new PIXI.Sprite(yellowstar)
  starButton4.position.set(600, 200)

  const squareMask2 = new PIXI.Graphics()
    .beginFill(0xffffff)
    .drawRect(starButton4.x, starButton4.y, 75, 200)
    .endFill()

  starButton4.mask = squareMask2

  // Again, hitarea for intersection checks
  starButton4.hitArea = new PIXI.Polygon([
    80, 0, 100, 50, 160, 55, 115, 95, 130, 150, 80, 120, 30, 150, 45, 95, 0, 55,
    60, 50,
  ])
  starButton4.buttonMode = true
  starButton4.interactive = true

  starButton4.on('pointerdown', (event) => onPointerDown(starButton4))

  starButton4.on('pointerover', (event) => onPointerOver(starButton4))

  starButton4.on('pointerout', (event) => onPointerOut(starButton4))

  const style = new PIXI.TextStyle({ fill: '#ffffff' })

  const text1 = new PIXI.Text('Standard', style)
  text1.x = starButton1.x + 25
  text1.y = starButton1.y + 170

  const text2 = new PIXI.Text('Hit Area', style)
  text2.x = starButton2.x + 35
  text2.y = starButton2.y + 170

  const text3 = new PIXI.Text('Mask', style)
  text3.x = starButton3.x + 10
  text3.y = starButton3.y + 170

  const text4 = new PIXI.Text('Mask + Hit Area', style)
  text4.x = starButton4.x - 10
  text4.y = starButton4.y + 170

  // add the sprite to the stage
  app.stage.addChild(
    starButton1,
    starButton2,
    starButton3,
    starButton4,
    text1,
    text2,
    text3,
    text4
  )

  // onPointerDown
  function onPointerDown(sprite) {
    sprite.tint = 0x333333
  }

  // onPointerOver
  function onPointerOver(sprite) {
    sprite.tint = 0xcccccc
  }

  // onPointerOut
  function onPointerOut(sprite) {
    sprite.tint = 0xffffff
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
