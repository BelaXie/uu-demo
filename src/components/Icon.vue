<template>
  <svg class="icon" aria-hidden="true">
    <use :xlink:href="`#icon-${name}`" />
  </svg>
</template>
<script lang="ts">
import "../svg.js";
export default {
  name: "UUIcon",
  props: ["name"]
};
</script>
<style lang="scss" scoped>
@import "../assets/styles/helper.scss";
.icon {
  width: 1em;
  height: 1em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
}
</style>
