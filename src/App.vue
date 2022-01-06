<template>
  <router-view></router-view>
</template>
<script>
export default {};
</script>
<style lang="less">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  width: 100%;
  /* 最大宽度 */
  max-width: 750px;
  /* 最小宽度 */
  min-width: 320px;
  margin: 0 auto;
  font-size: 14px;
  font-family: -apple-system, Helvetica, sans-serif;
  line-height: 1.5;
  color: #666;
}

/*去掉a标签在移动端中点击会出现蓝色遮罩层的效果  设置为transparent 完成透明*/
a {
  -webkit-tap-highlight-color: transparent;
}

/*在移动端浏览器默认的外观在iOS上加上这个属性才能给按钮和输入框自定义样式*/
input {
  -webkit-appearance: none;
}

/*禁用长按页面时的弹出菜单*/
img,
a {
  -webkit-touch-callout: none;
}

ul {
  list-style: none;
}

img {
  /* 清除图片底下的空白缝隙 */
  vertical-align: middle;
  width: 100%;
}

input,
button {
  outline: 0;
  border: 0;
}

a {
  display: block;
  color: #666;
  text-decoration: none;
}

/* 清除浮动 */
.clearfix::after {
  content: "";
  display: block;
  height: 0;
  visibility: hidden;
  clear: both;
}
</style>
