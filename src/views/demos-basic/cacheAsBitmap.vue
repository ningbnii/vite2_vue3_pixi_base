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
    // backgroundColor: 0x1099bb,
    antialias: true,
    autoDensity: true,
    resolution: window.devicePixelRatio,
  })

  myCanvas.value.appendChild(app.view)

  app.stop()

  // load resources
  app.loader
    .add('spritesheet', 'assets/spritesheet/monsters.json')
    .load(onAssetsLoaded)

  // holder to store aliens
  const aliens = []
  const alienFrames = [
    'eggHead.png',
    'flowerTop.png',
    'helmlok.png',
    'skully.png',
  ]

  let count = 0

  // create an empty container
  const alienContainer = new PIXI.Container()
  alienContainer.x = app.screen.width / 2
  alienContainer.y = app.screen.height / 2

  // make the stage interactive
  app.stage.interactive = true

  // add the container to the stage
  app.stage.addChild(alienContainer)

  function onAssetsLoaded() {
    // add a bunch of aliens with textures from an image path
    for (let i = 0; i < 100; i++) {
      const frameName = alienFrames[i % 4]

      // create an alien using the frame name
      const alien = PIXI.Sprite.from(frameName)
      // tint the aliens a random color
      alien.tint = Math.random() * 0xffffff
      /**
       * another way of doing the above would be to use the following:
       * var texture = PIXI.Texture.from(frameName)
       * var alien = new PIXI.Sprite(texture)
       */

      // set the anchor point so the texture is centerd on the sprite
      alien.anchor.x = 0.5
      alien.anchor.y = 0.5
      // position the alien on the x axis at a random position between the left and right of the screen
      alien.x = Math.random() * app.screen.width - app.screen.width / 2
      // position the alien on the y axis at a random position between the top and bottom of the screen
      alien.y = Math.random() * app.screen.height - app.screen.height / 2
      // set the scale of the alien to be a random number between 0.1 and 1
      // alien.scale.set(Math.random() * 0.5 + 0.1)
      // set the rotation of the alien to a random number between 0 and 360 degrees
      // alien.rotation = Math.random() * Math.PI * 2

      // push
      aliens.push(alien)
      alienContainer.addChild(alien)
    }
    // start
    app.start()
  }

  // combines both mouse click and touch tap
  app.stage.on('pointertap', onTap)

  function onTap(event) {
    // get the mouse position
    // const mousePosition = event.data.global
    alienContainer.cacheAsBitmap = !alienContainer.cacheAsBitmap
  }

  // listen for animate update
  app.ticker.add(() => {
    // loop through all the aliens
    for (let i = 0; i < aliens.length; i++) {
      // get the alien
      const alien = aliens[i]
      // rotation
      alien.rotation += 0.1
    }

    count += 0.01
    // alienContainer scale and rotation
    alienContainer.scale.x = Math.sin(count)
    alienContainer.scale.y = Math.sin(count)
    alienContainer.rotation += 0.01
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
