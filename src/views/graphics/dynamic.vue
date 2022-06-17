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

  const graphics = new PIXI.Graphics()
  // add to stage
  app.stage.addChild(graphics)

  // set a fill and line style
  graphics.beginFill(0xff3300)
  graphics.lineStyle(10, 0xff0000, 1)

  // draw a shape
  graphics.moveTo(50, 50)
  graphics.lineTo(250, 50)
  graphics.lineTo(100, 100)
  graphics.lineTo(250, 220)
  graphics.lineTo(50, 220)
  graphics.lineTo(50, 50)
  graphics.closePath()
  graphics.endFill()

  // set a fill and line style again
  graphics.lineStyle(10, 0x0000ff, 0.8)

  // draw a second shape
  graphics.beginFill(0x66ccff)
  graphics.moveTo(100, 100)
  graphics.lineTo(250, 100)
  graphics.quadraticCurveTo(600, 0, 480, 100)
  graphics.lineTo(250, 250)
  graphics.lineTo(100, 250)
  graphics.lineTo(100, 100)
  graphics.closePath()
  graphics.endFill()

  // draw a rectangle
  graphics.lineStyle(2, 0x0000ff, 1)
  graphics.drawRect(50, 250, 100, 100)

  // draw a circle
  graphics.lineStyle(0)
  graphics.beginFill(0xffff0, 0.5)
  graphics.drawCircle(470, 200, 50)
  graphics.endFill()

  // create a moving shape
  // let x = 0
  // let y = 0
  // let speed = 5
  // let direction = 'right'

  // const move = () => {
  //   if (direction === 'right') {
  //     x += speed
  //     if (x > app.screen.width - 50) {
  //       direction = 'up'
  //     }
  //   } else if (direction === 'up') {
  //     y -= speed
  //     if (y < 50) {
  //       direction = 'left'
  //     }
  //   } else if (direction === 'left') {
  //     x -= speed
  //     if (x < 50) {
  //       direction = 'down'
  //     }
  //   } else if (direction === 'down') {
  //     y += speed
  //     if (y > app.screen.height - 50) {
  //       direction = 'right'
  //     }
  //   }
  //   graphics.position.x = x
  //   graphics.position.y = y
  //   app.render()
  //   requestAnimationFrame(move)
  // }

  // move()

  const thing = new PIXI.Graphics()
  app.stage.addChild(thing)
  // position center
  thing.x = app.screen.width / 2
  thing.y = app.screen.height / 2

  let count = 0
  // const update = () => {
  //   count += 0.1
  //   thing.clear()
  //   thing.lineStyle(10, 0xff0000, 1)
  //   thing.beginFill(0xff0000, 0.5)
  //   thing.drawRect(0, 0, 100, 100)
  //   thing.rotation = count
  //   app.render()
  //   requestAnimationFrame(update)
  // }
  // update()

  app.ticker.add(() => {
    count += 0.1
    thing.clear()
    thing.lineStyle(10, 0xff0000, 1)
    thing.beginFill(0xff0000, 0.5)

    thing.moveTo(-120 + Math.sin(count) * 20, -100 + Math.cos(count) * 20)
    thing.lineTo(120 + Math.cos(count) * 20, -100 + Math.sin(count) * 20)
    thing.lineTo(120 + Math.sin(count) * 20, 100 + Math.cos(count) * 20)
    thing.lineTo(-120 + Math.cos(count) * 20, 100 + Math.sin(count) * 20)
    thing.lineTo(-120 + Math.sin(count) * 20, -100 + Math.cos(count) * 20)
    thing.closePath()

    thing.rotation = count * 0.1
  })

  // just click on the stage to draw random lines
  app.stage.interactive = true

  app.renderer.plugins.interaction.on('pointerdown', () => {
    graphics.lineStyle(Math.random() * 30, Math.random() * 0xffffff, 1)
    graphics.moveTo(
      Math.random() * app.screen.width,
      Math.random() * app.screen.height
    )
    graphics.bezierCurveTo(
      Math.random() * app.screen.width,
      Math.random() * app.screen.height,
      Math.random() * app.screen.width,
      Math.random() * app.screen.height,
      Math.random() * app.screen.width,
      Math.random() * app.screen.height
    )
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
