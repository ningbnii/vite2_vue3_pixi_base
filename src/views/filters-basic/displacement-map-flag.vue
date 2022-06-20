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

  const flag = PIXI.Sprite.from('assets/flag.png')
  container.addChild(flag)
  // position the flag at the center of the screen
  flag.x = app.screen.width / 2
  flag.y = app.screen.height / 2
  // anchor center
  flag.anchor.set(0.5)

  // create a displacement map filter
  const displacementSprite = PIXI.Sprite.from(
    'assets/displacement_map_repeat.jpg'
  )
  // make sure the sprite is wrapping
  displacementSprite.texture.baseTexture.wrapMode = PIXI.WRAP_MODES.REPEAT
  // set the displacement map filter
  const displacementFilter = new PIXI.filters.DisplacementFilter(
    displacementSprite
  )

  // set position
  displacementSprite.position = flag.position

  app.stage.addChild(displacementSprite)

  flag.filters = [displacementFilter]
  // set padding
  displacementFilter.padding = 10
  // set filter scale
  displacementFilter.scale.x = 30
  displacementFilter.scale.y = 60

  // ticker to animate the displacement map
  app.ticker.add(() => {
    displacementSprite.x += 1
    // displacementSprite.y += 1
    // reset x to 0 when it's over width to keep values from going to very huge numbers
    if (displacementSprite.x > displacementSprite.width) {
      displacementSprite.x = 0
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
