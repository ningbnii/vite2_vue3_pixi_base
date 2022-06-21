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

  const graphics = new PIXI.Graphics()

  graphics.lineStyle(2, 0x0000ff, 1)
  graphics.moveTo(0, 0)
  graphics.lineTo(100, 200)
  graphics.lineTo(200, 200)
  graphics.lineTo(240, 100)
  // position
  graphics.x = 50
  graphics.y = 50
  // add to stage
  app.stage.addChild(graphics)

  const bezier = new PIXI.Graphics()
  bezier.lineStyle(2, 0x0000ff, 1)
  bezier.moveTo(0, 0)
  bezier.bezierCurveTo(250, 0, 250, 200, 400, 200)
  bezier.x = 50
  bezier.y = 50
  app.stage.addChild(bezier)

  const realPath2 = new PIXI.Graphics()

  realPath2.lineStyle(2, 0x0000ff, 1)
  realPath2.moveTo(0, 0)
  realPath2.lineTo(0, -100)
  realPath2.lineTo(150, 150)
  realPath2.lineTo(240, 100)
  realPath2.x = 50
  realPath2.y = 250
  app.stage.addChild(realPath2)

  const bezier2 = new PIXI.Graphics()

  const texture = PIXI.Texture.from('assets/bg_rotate.jpg')

  bezier2.lineTextureStyle({ texture, width: 10 })
  bezier2.moveTo(0, 0)
  bezier2.bezierCurveTo(250, 0, 250, 200, 400, 200)
  bezier2.x = 50
  bezier2.y = 250
  app.stage.addChild(bezier2)

  const arc = new PIXI.Graphics()

  arc.lineStyle(2, 0x0000ff, 1)
  arc.arc(100, 400, 50, Math.PI, Math.PI * 2)
  app.stage.addChild(arc)

  const arc2 = new PIXI.Graphics()

  arc2.lineTextureStyle({ texture, width: 10 })
  arc2.arc(100, 500, 50, Math.PI, Math.PI * 2, true)
  app.stage.addChild(arc2)

  const rectAndHole = new PIXI.Graphics()

  rectAndHole.lineStyle(2, 0x0000ff, 1)
  rectAndHole.beginFill(0xff700b, 1)
  rectAndHole.drawRect(50, 50, 100, 100)
  rectAndHole.beginHole()
  rectAndHole.drawRect(70, 70, 60, 60)
  rectAndHole.endHole()
  rectAndHole.x = 50
  rectAndHole.y = 550
  app.stage.addChild(rectAndHole)

  const beautifulRect = new PIXI.Graphics()

  beautifulRect.lineTextureStyle({ texture, width: 10 })
  beautifulRect.beginFill(0xff700b, 1)
  beautifulRect.drawRect(50, 50, 100, 100)
  beautifulRect.x = 200
  beautifulRect.y = 550
  app.stage.addChild(beautifulRect)
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
