<template>
  <div class="wrapper" :class="wrapperClass">
    <div class="toast" ref="toast">
      <div class="message">
        <slot v-if="!enableHtml"></slot>
        <div v-else v-html="$slots.default[0]"></div>
      </div>
      <span class="line" v-if="closeButton" ref="line"></span>
      <span @click="onClickClose" class="close" v-if="closeButton">{{ closeButton.text }}</span>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    zIndex: {
      type: [Number, String],
      default: "auto",
    },
    autoClose: {
      type: [Boolean, Number],
      default: 5,
      validator(value) {
        return value === false || typeof value === "number";
      },
    },
    closeButton: {
      type: Object,
      //   default() {
      //     return {
      //       text: "知道了",
      //       callback: undefined
      //     };
      //   }
    },
    enableHtml: {
      type: Boolean,
      default: false,
    },
    position: {
      type: String,
      default: "top",
      validator(value) {
        return ["top", "middle", "bottom"].indexOf(value) >= 0;
      },
    },
  },
  computed: {
    wrapperClass() {
      return {
        [`position-${this.position}`]: true,
      };
    },
  },
  mounted() {
    this.updateStyle();
    this.execAutoClose();
    this.$el.style.zIndex = this.zIndex;
  },
  methods: {
    execAutoClose() {
      if (this.autoClose) {
        setTimeout(() => {
          this.close();
        }, this.autoClose * 1000);
      }
    },
    updateStyle() {
      // 如果不使用nextTick，而是直接在mounted中设置高度的话，那个时候得到的getBoundingClientRect()都为0，这是因为在plugin.js中先 $mount 再放在了 body 页面中
      this.$nextTick(() => {
        if (this.$refs.line) {
          //el.style只能获得内联样式，不能获得css样式，所以要用getBoundingClientRect()
          this.$refs.line.style.height = `${this.$refs.toast.getBoundingClientRect().height}px`;
        }
      });
    },
    close() {
      this.$el.remove();
      this.$emit("close");
      this.$destroy();
    },
    log() {
      console.log("hello");
    },
    onClickClose() {
      this.close();
      if (this.closeButton && typeof this.closeButton.callback === "function") this.closeButton.callback(this);
    },
  },
};
</script>

<style lang="scss" scoped>
$toast-min-height: 40px;
$toast-bg: rgba(0, 0, 0, 0.75);
$font-size: 14px;
$animation-duration: 350ms;
@keyframes slide-up {
  0% {
    opacity: 0;
    transform: translateY(100%);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}
@keyframes slide-down {
  0% {
    opacity: 0;
    transform: translateY(-100%);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}
@keyframes fade-in {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
.wrapper {
  position: fixed;
  left: 50%;
  transform: translateX(-50%);
  &.position-top {
    top: 0;
    > .toast {
      animation: slide-down $animation-duration;
      border-top-left-radius: 0;
      border-top-right-radius: 0;
    }
  }
  &.position-bottom {
    bottom: 0;
    > .toast {
      animation: slide-up $animation-duration;
      border-bottom-left-radius: 0;
      border-bottom-right-radius: 0;
    }
  }
  &.position-middle {
    top: 50%;
    transform: translate(-50%, -50%);
    > .toast {
      animation: fade-in $animation-duration;
    }
  }
}
.toast {
  min-height: $toast-min-height;
  background-color: $toast-bg;
  padding: 0 16px;
  color: white;
  display: flex;
  align-items: center;
  font-size: $font-size;
  box-shadow: 0 0 3px 0 rgba($color: #000000, $alpha: 0.5);
  border-radius: 4px;
  line-height: 1.8;
  > .message {
    padding: 4px 0;
  }
  > .close {
    padding-left: 16px;
    flex-shrink: 0;
  }
  > .line {
    border-left: 1px solid #666;
    margin-left: 16px;
  }
}
</style>
