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

  // load them google fonts first
  window.WebFontConfig = {
    google: {
      families: ['Snippet', 'Arvo:700italic', 'Podkova:700'],
    },
    active: () => {
      // start the animation
      init()
    },
  }

  // inject google fonts
  const wf = document.createElement('script')
  wf.src = 'https://ajax.googleapis.com/ajax/libs/webfont/1.6.26/webfont.js'
  wf.type = 'text/javascript'
  wf.async = 'true'
  const s = document.getElementsByTagName('script')[0]
  s.parentNode.insertBefore(wf, s)

  function init() {
    // create some white text using the Google WebFont library
    const basicText = new PIXI.Text('Basic text in pixi', {
      fontFamily: 'Snippet',
      fontSize: 36,
      fill: '#FFFFFF',
      align: 'center',
    })
    // anchor point for the text is set to the center
    basicText.anchor.set(0.5)
    // position the text on the screen
    basicText.x = app.screen.width / 2
    basicText.y = app.screen.height / 2
    // add the text to the stage
    app.stage.addChild(basicText)
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
