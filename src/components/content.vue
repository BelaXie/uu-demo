<template>
  <div class="content">
    <slot />
  </div>
</template>

<script>
export default {
  name: "UUContent",
};
</script>

<style lang="scss" scoped>
.content {
  flex-grow: 1;
}
</style>
