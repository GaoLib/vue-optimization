<template>
  <div class="scroll-main-container">
    <p>固定高度虚拟滚动列表</p>
    <fix-height-scroll
      :container-height="200"
      :container-width="200"
      :item-size="50"
      :item-count="100"
      :list="list"
    >
      <template slot>
        <div
          v-for="(item, index) in list"
          :key="item.id"
          :class="['scroll-item', index % 2 ? 'odd-item':'even-item']"
        >
          {{ item.name }}
        </div>
      </template>
    </fix-height-scroll>
  </div>
</template>

<script>
import fixHeightScroll from './components/fixHeightScroll'

export default {
  components: {
    fixHeightScroll
  },
  data() {
    return {
      list: []
    }
  },
  mounted() {
    this.generateList()
  },
  methods: {
    generateList() {
      for (let i = 0; i < 100; i++) {
        this.list.push({
          id: i + 1,
          name: `Row ${i + 1}`
        })
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.scroll-main-container {
  padding: 10px;
  .scroll-item {
    height: 50px;
    text-align: center;
    padding: 16px;
    box-sizing: border-box;
  }
  .odd-item {
    background-color: lightcyan;
  }
  .even-item {
    background-color: indianred;
  }
}
</style>
