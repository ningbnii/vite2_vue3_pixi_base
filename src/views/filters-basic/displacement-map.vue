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
  app.stage.interactive = true

  const container = new PIXI.Container()
  app.stage.addChild(container)

  const padding = 100
  const bounds = new PIXI.Rectangle(
    -padding,
    -padding,
    app.screen.width + padding * 2,
    app.screen.height + padding * 2
  )

  const maggots = []

  for (let i = 0; i < 20; i++) {
    const maggot = new PIXI.Sprite(PIXI.Texture.from('assets/maggot.png'))
    maggot.anchor.set(0.5)
    container.addChild(maggot)

    maggot.x = Math.random() * bounds.width
    maggot.y = Math.random() * bounds.height

    maggot.direction = Math.random() * Math.PI * 2
    maggot.speed = 1
    maggot.turnSpeed = Math.random() - 0.8

    maggot.scale.x = maggot.scale.y = Math.random() * 0.5 + 0.25

    // original
    maggot.original = new PIXI.Point()
    maggot.original.copyFrom(maggot.scale)

    maggots.push(maggot)
  }

  const displacementSprite = PIXI.Sprite.from('assets/displace.png')
  const displacementFilter = new PIXI.filters.DisplacementFilter(
    displacementSprite
  )

  app.stage.addChild(displacementSprite)

  container.filters = [displacementFilter]

  displacementSprite.anchor.set(0.5)
  // set displacementFilter scale
  // 设置滤镜的缩放
  displacementFilter.scale.x = 100
  displacementFilter.scale.y = 100

  const ring = PIXI.Sprite.from('assets/ring.png')
  app.stage.addChild(ring)
  ring.anchor.set(0.5)
  // visible = false
  ring.visible = false

  const bg = PIXI.Sprite.from('assets/bg_grass.jpg')
  container.addChild(bg)
  // set size of bg
  bg.width = app.screen.width
  bg.height = app.screen.height
  // bg alpha
  bg.alpha = 0.5

  // event
  app.stage.on('pointermove', onPointerMove)

  function onPointerMove(event) {
    ring.visible = true

    // set displacementSprite position
    displacementSprite.position.set(
      event.data.global.x - 25,
      event.data.global.y
    )
    ring.position.copyFrom(displacementSprite.position)
  }

  // animation
  let count = 0
  app.ticker.add(() => {
    count += 0.05
    for (let i = 0; i < maggots.length; i++) {
      const maggot = maggots[i]

      maggot.direction += maggot.turnSpeed * 0.01
      maggot.x += Math.sin(maggot.direction) * maggot.speed
      maggot.y += Math.cos(maggot.direction) * maggot.speed
      // rotation
      maggot.rotation = -maggot.direction - Math.PI / 2
      // scale
      maggot.scale.x = maggot.scale.y =
        maggot.original.x + Math.sin(count + i) * 0.1

      if (maggot.x < bounds.x) {
        maggot.x += bounds.width
      } else if (maggot.x > bounds.width) {
        maggot.x -= bounds.width
      }

      if (maggot.y < bounds.y) {
        maggot.y += bounds.height
      } else if (maggot.y > bounds.height) {
        maggot.y -= bounds.height
      }
    }
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
