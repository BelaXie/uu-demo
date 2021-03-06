<template>
  <div class="popover" ref="popOver">
    <div class="content-wrapper" ref="contentWrapper" v-if="visible" :class="{ [`position-${position}`]: true }">
      <slot name="content" :close="close"></slot>
    </div>
    <div class="triggerWrapper" ref="triggerWrapper">
      <slot></slot>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      visible: false,
    };
  },
  props: {
    position: {
      type: String,
      default: "top",
      validator(value) {
        return ["top", "left", "right", "bottom"].indexOf(value) >= 0;
      },
    },
    trigger: {
      type: String,
      default: "click",
      validator(value) {
        return ["click", "hover"].indexOf(value) >= 0;
      },
    },
  },
  mounted() {
    if (this.trigger === "click") {
      this.$refs.popOver.addEventListener("click", this.onClick);
    } else {
      this.$refs.popOver.addEventListener("mouseenter", this.open);
      this.$refs.popOver.addEventListener("mouseleave", this.close);
    }
  },
  destroyed() {
    this.$refs.popOver.removeEventListener("mouseenter", this.open);
    this.$refs.popOver.removeEventListener("mouseleave", this.close);
    this.$refs.popOver.removeEventListener("click", this.onClick);
  },
  methods: {
    showContent() {
      //放在body后面的原因是防止外部的父元素使用了overflow:hidden将内容隐藏掉了
      const { triggerWrapper, contentWrapper } = this.$refs;
      document.body.appendChild(contentWrapper);
      const { height: height2 } = contentWrapper.getBoundingClientRect();
      const { left, top, width, height } = triggerWrapper.getBoundingClientRect();
      let positions = {
        top: { left: window.scrollX + left, top: window.scrollY + top },
        bottom: { left: window.scrollX + left, top: window.scrollY + height + top },
        left: { left: window.scrollX + left, top: window.scrollY + top + (height - height2) / 2 },
        right: { left: window.scrollX + width + left, top: window.scrollY + top + (height - height2) / 2 },
      };
      contentWrapper.style.left = positions[this.position].left + "px";
      contentWrapper.style.top = positions[this.position].top + "px";
    },
    eventHandler(e) {
      if (this.$refs.triggerWrapper.contains(e.target) || (this.$refs.contentWrapper && this.$refs.contentWrapper.contains(e.target))) {
        return;
      }
      this.close();
    },
    close() {
      this.visible = false;
      document.removeEventListener("click", this.eventHandler);
    },
    open() {
      this.visible = true;
      this.$nextTick(() => {
        this.showContent();
      });
      document.addEventListener("click", this.eventHandler);
    },
    onClick() {
      if (this.visible === true) {
        this.close();
      } else {
        this.open();
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.popover {
  display: inline-block;
  padding: 10px;
  vertical-align: top;
  position: relative;
  //   border: 1px solid red;
}
.content-wrapper {
  // border: 1px solid green;
  $border-color: #333;
  $border-radius: 4px;
  position: absolute;
  border: 1px solid $border-color;
  border-radius: $border-radius;
  padding: 0.5em 1em;
  max-width: 15em;
  word-break: break-all;
  background-color: white;
  filter: drop-shadow(0 1px 1px rgba(0, 0, 0, 0.5));
  &::before,
  &::after {
    position: absolute;
    display: block;
    width: 0px;
    height: 0px;
    content: "";
    border: 10px solid transparent;
  }
  &.position-top {
    transform: translateY(-100%);
    margin-top: -10px;
    &::before {
      left: 10px;
      top: 100%;
      border-top-color: black;
      border-bottom: none;
    }
    &::after {
      left: 10px;
      top: calc(100% - 1px);
      border-bottom: none;
      border-top-color: white;
    }
  }
  &.position-bottom {
    margin-top: 10px;
    &::before {
      left: 10px;
      bottom: 100%;
      border-top: none;
      border-bottom-color: black;
    }
    &::after {
      bottom: calc(100% - 1px);
      border-top: none;
      left: 10px;
      border-bottom-color: white;
    }
  }
  &.position-left {
    transform: translateX(-100%);
    margin-left: -10px;
    &::before {
      top: 50%;
      transform: translateY(-50%);
      left: 100%;
      border-right: none;
      border-left-color: black;
    }
    &::after {
      transform: translateY(-50%);
      border-right: none;
      top: 50%;
      left: calc(100% - 1px);
      border-left-color: white;
    }
  }
  &.position-right {
    margin-left: 10px;
    &::before {
      top: 50%;
      transform: translateY(-50%);
      border-left: none;
      right: 100%;
      border-right-color: black;
    }
    &::after {
      border-left: none;
      top: 50%;
      transform: translateY(-50%);
      right: calc(100% - 1px);
      border-right-color: white;
    }
  }
}
</style>
