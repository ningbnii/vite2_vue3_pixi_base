<template>
  <div class="canvas_box" ref="canvasBox">
    <div ref="myCanvas"></div>
  </div>
</template>
<script setup>
import * as PIXI from "pixi.js";
import { ref, onMounted } from "vue";
import bunny from "../assets/logo.png";
let myCanvas = ref(null);
let canvasBox = ref(null);

onMounted(() => {
  const app = new PIXI.Application({
    width: canvasBox.value.clientWidth,
    height: canvasBox.value.clientHeight,
  });

  app.loader.add("bunny", bunny).load((loader, resources) => {
    const bunny = new PIXI.Sprite(resources.bunny.texture);
    bunny.x = app.renderer.width / 2;
    bunny.y = app.renderer.height / 2;

    bunny.anchor.x = 0.5;
    bunny.anchor.y = 0.5;

    app.stage.addChild(bunny);

    app.ticker.add(() => {
      bunny.rotation += 0.01;
    });
  });
  myCanvas.value.appendChild(app.view);
});
</script>
<style lang="less" scoped>
.canvas_box {
  width: 100vw;
  height: 100vh;
}
</style>
