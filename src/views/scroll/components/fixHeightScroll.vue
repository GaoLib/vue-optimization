<template>
  <div
    class="scroll-container"
    :style="{ width: `${containerWidth}px`, height: `${containerHeight}px`}"
    @scroll="handleScroll"
  >
    <div class="scroll-content" :style="{ height: `${itemCount * itemSize}px` }">
      <!-- <div v-for="item in itemList" :key="item.id"
        :style="{ height: `${itemSize}px`, top: `${itemSize * (item.id - 1)}px` }">
        {{ item.name }}
      </div> -->
      <slot />
    </div>
  </div>
</template>

<script>
export default {
  name: 'FixHeightScroll',
  props: {
    containerHeight: {
      type: Number,
      default: 0
    },
    containerWidth: {
      type: Number,
      default: 0
    },
    itemSize: {
      type: Number,
      default: 0
    },
    itemCount: {
      type: Number,
      default: 0
    },
    list: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      scrollOffset: 0
    }
  },
  watch: {
    list: {
      deep: true,
      handler() {
        this.getChildren()
      }
    }
  },
  methods: {
    handleScroll(e) {
      const { scrollTop } = e.srcElement
      this.scrollOffset = scrollTop
      this.getChildren()
    },
    getChildren() {
      const startIndex = Math.floor(this.scrollOffset / this.itemSize)
      // 上缓冲区起始索引
      const finialStartIndex = Math.max(0, startIndex - 2)
      // 可视区可展示的最大个数
      const numVisible = Math.ceil(this.containerHeight / this.itemSize)
      // 下缓冲区结束索引
      const endIndex = Math.min(this.itemCount, startIndex + numVisible + 2)
      const initialChildren = this.$slots.default
      initialChildren.forEach((el, index) => {
        if (index < finialStartIndex || index >= endIndex) {
          this.$nextTick(() => {
            el.elm.style.display = 'none'
          })
        } else {
          this.$nextTick(() => {
            const element = el.elm
            element.style.display = 'block'
            element.style.position = 'absolute'
            element.style.height = `${this.itemSize}px`
            element.style.width = '100%'
            element.style.top = `${this.itemSize * index}px`
          })
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.scroll-container {
  position: relative;
  overflow: auto;
  .scroll-content {
    width: 100%;
  }
}
</style>
