<template>
  <div class="container-fluid">
    <div class="row">
      <van-list finished-text="没有更多了">
        <h1>demos-basic</h1>
        <van-cell title="container" is-link to="/demos-basic/container" />
        <van-cell
          title="transparent-background"
          is-link
          to="/demos-basic/transparent-background"
        />
        <van-cell title="tinting" is-link to="/demos-basic/tinting" />
        <van-cell
          title="cacheAsBitmap"
          is-link
          to="/demos-basic/cacheAsBitmap"
        />
        <van-cell
          title="particle-container"
          is-link
          to="/demos-basic/particle-container"
        />
        <van-cell title="blend-modes" is-link to="/demos-basic/blend-modes" />
        <van-cell title="simplePlane" is-link to="/demos-basic/simplePlane" />
        <h1>demos-advanced</h1>
        <van-cell title="slots" is-link to="/demos-advanced/slots" />
        <h1>sprite</h1>
        <van-cell title="basic" is-link to="/sprite/basic" />
        <van-cell title="texture-swap" is-link to="/sprite/texture-swap" />
        <van-cell
          title="animated-sprite-explosion"
          is-link
          to="/sprite/animated-sprite-explosion"
        />
        <van-cell
          title="animated-sprite-jet"
          is-link
          to="/sprite/animated-sprite-jet"
        />
        <van-cell
          title="animated-sprite-speed"
          is-link
          to="/sprite/animated-sprite-speed"
        />
        <van-cell title="tiling-sprite" is-link to="/sprite/tiling-sprite" />
        <van-cell title="video" is-link to="/sprite/video" />
        <h1>text</h1>
        <van-cell title="text" is-link to="/text/text" />
        <van-cell title="bitmap-text" is-link to="/text/bitmap-text" />
        <van-cell title="web-font" is-link to="/text/web-font" />
      </van-list>
    </div>
  </div>
</template>
<script setup></script>
<style lang="less" scoped></style>
