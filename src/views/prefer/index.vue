<template>
  <div>
    <el-table v-loading="loading" :data="list">
      <el-table-column label="Id" min-width="200">
        <template slot-scope="{row}">
          {{ row.order_no }}
        </template>
      </el-table-column>
      <el-table-column label="Username" width="195" align="center">
        <template slot-scope="{row}">
          {{ row.username }}
        </template>
      </el-table-column>
      <el-table-column label="Tree" width="100" align="center">
        <template slot-scope="{row}">
          <!-- ele-ui：初次渲染慢 -->
          <!-- <el-popover effect="dark" placement="top" trigger="click" popper-class="class-list-dep-popover"
            width="800">
            <div :class="['overflow-tree']">
              <el-scrollbar class="dep-scrollbar">
                <el-tree :data="row.tree" :props="{ label: 'name' }" :default-expand-all="true" />
              </el-scrollbar>
            </div>
            <div class="col-tag" style="margin-left: 4px;" slot="reference">
              树节点
            </div>
          </el-popover> -->
          <!-- 优化1： -->
          <div :ref="`orgRef${row.order_no}`" class="col-tag" style="margin-left: 4px;" @click="handleRef(row, $event)">
            树节点1
          </div>
        </template>
      </el-table-column>
    </el-table>
    <el-popover
      v-if="showOrgPop"
      ref="orgPopoverRef"
      placement="top"
      popper-class="class-list-dep-popover"
      width="400"
      :reference="referencePopover"
      trigger="click"
    >
      <div :class="['dep-tree-container', 'overflow-tree']">
        <el-scrollbar class="dep-scrollbar">
          <el-tree :data="popOrganizations" :props="{ label: 'name' }" :default-expand-all="true" />
        </el-scrollbar>
      </div>
    </el-popover>
  </div>
</template>

<script>
import { popTableList } from '@/api/remote-search'
export default {
  data() {
    return {
      list: [],
      tree: [],
      loading: false,
      showOrgPop: false,
      referencePopover: {},
      currentPopover: -1,
      popOrganizations: []
    }
  },
  mounted() {
    this.fetchData()
    document.addEventListener('click', this.clickToCloseDepPopover)
  },
  beforeDestroy() {
    document.removeEventListener('click', this.clickToCloseDepPopover)
  },
  methods: {
    fetchData() {
      this.loading = true
      popTableList().then(response => {
        this.list = response.data.items
        this.loading = false
      })
    },
    // 优化1
    handleRef(row, e) {
      if (this.currentPopover === row.order_no && this.showOrgPop) return
      this.showOrgPop = false
      this.currentPopover = row.order_no
      this.referencePopover = this.$refs[`orgRef${row.order_no}`].$el
      this.popOrganizations = row.tree
      this.$nextTick(() => {
        this.showOrgPop = true
        this.$nextTick(() => {
          const bodyHeight = document.body.clientHeight
          const ele = this.$refs.orgPopoverRef.$el.childNodes[0]
          const startY = e.clientY - 50
          const popHeight = 300
          const y = e.clientY + popHeight > bodyHeight ? startY - popHeight : startY
          ele.style.top = `${y}px`
          ele.style.left = `${e.clientX - 600}px`
          this.$refs.orgPopoverRef.doShow()
        })
      })
    },
    clickToCloseDepPopover(e) {
      if (e.target.classList[0] !== 'col-tag') {
        this.showOrgPop = false
      }
    },
    scrollClosePopover() {
      this.showOrgPop = false
    }
  }
}
</script>

<style lang="scss">
$--color-text-primary: #424242;
.class-list-dep-popover {
  background-color: $--color-text-primary;
  // display: block !important;
  .overflow-tree {
    height: 280px;
    background-color: $--color-text-primary;
    .dep-scrollbar {
      height: 100%;
      .el-scrollbar__wrap {
        overflow-x: hidden;
      }
    }
  }
  .popper__arrow {
    &::after {
      border-bottom-color: $--color-text-primary !important;
      border-top-color: $--color-text-primary !important;
    }
  }
  .el-tree {
    background-color: $--color-text-primary;
    color: #fff;
    .el-tree-node__content {
      background-color: $--color-text-primary;
      &:hover {
        background-color: $--color-text-primary;
      }
    }
    .el-tree-node.is-current>.el-tree-node__content {
      background-color: $--color-text-primary;
    }
    .el-tree-node.is-expanded>.el-tree-node__content {
      background-color: $--color-text-primary;
    }
  }
}
</style>
