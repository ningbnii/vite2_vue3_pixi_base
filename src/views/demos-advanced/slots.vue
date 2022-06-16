<template>
  <div class="canvas_box" ref="canvasBox">
    <div ref="myCanvas"></div>
  </div>
</template>
<script setup>
import * as PIXI from 'pixi.js'
import { ref, onMounted, onUnmounted } from 'vue'

let myCanvas = ref(null)
let canvasBox = ref(null)
let app = {}

onMounted(() => {
  app = new PIXI.Application({
    width: canvasBox.value.clientWidth,
    height: canvasBox.value.clientHeight,
    backgroundColor: 0x1099bb,
    // resolution: window.devicePixelRatio || 1,
  })

  myCanvas.value.appendChild(app.view)

  app.loader
    .add('eggHead', 'assets/eggHead.png')
    .add('flowerTop', 'assets/flowerTop.png')
    .add('helmlok', 'assets/helmlok.png')
    .add('skully', 'assets/skully.png')
    .load(onAssetsLoaded)

  const REEL_WIDTH = app.screen.width / 5
  const SYMBOL_SIZE = app.screen.width / 5

  function onAssetsLoaded() {
    // create different slot symbols
    const slotTextures = [
      PIXI.Texture.from('eggHead'),
      PIXI.Texture.from('flowerTop'),
      PIXI.Texture.from('helmlok'),
      PIXI.Texture.from('skully'),
    ]

    // build the reels
    const reels = []
    const reelContainer = new PIXI.Container()
    for (let i = 0; i < 5; i++) {
      const rc = new PIXI.Container()
      rc.x = i * REEL_WIDTH + 35
      reelContainer.addChild(rc)

      const reel = {
        symbols: [],
        container: rc,
        position: 0,
        previousPosition: 0,
        blur: new PIXI.filters.BlurFilter(),
      }
      reel.blur.blurX = 0
      reel.blur.blurY = 0
      rc.filters = [reel.blur]

      // build the symbols
      for (let j = 0; j < 4; j++) {
        const symbol = new PIXI.Sprite(
          slotTextures[Math.floor(Math.random() * slotTextures.length)]
        )
        // scale the symbol to fit symbol area
        symbol.y = j * SYMBOL_SIZE
        symbol.x = Math.round((SYMBOL_SIZE - symbol.width) / 2)
        symbol.scale.x = symbol.scale.y = Math.min(
          SYMBOL_SIZE / symbol.width,
          SYMBOL_SIZE / symbol.height
        )

        rc.addChild(symbol)
        reel.symbols.push(symbol)
      }
      reels.push(reel)
    }

    app.stage.addChild(reelContainer)

    // build top & bottom covers and position reelContainer
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
