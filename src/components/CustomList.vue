<template>
  <div
    class="list-view"
    :style="{
      height: `${height}px`
    }"
    @scroll="handleScroll"
  >
    <div class="list-view-phantom" :style="{ height: contentHeight }"></div>
    <div class="list-view-content" ref="listViewContent">
      <slot :visibleData="visibleData"></slot>
    </div>
    <div class="list-view-start">start:{{ start }}</div>
  </div>
</template>
<script>
export default {
  name: 'ListView',
  props: {
    // 数据
    data: {
      type: Array,
      default() {
        return [];
      }
    },
    // list-view高度
    height: {
      type: Number,
      default: 0
    },
    // list-view-item高度
    itemHeight: {
      type: Number,
      default: 0
    }
  },
  computed: {
    contentHeight() {
      return this.data.length * this.itemHeight + 'px';
    }
  },
  mounted() {
    this.updateVisibleData();
  },
  data() {
    return {
      start: 0,
      visibleData: []
    };
  },
  methods: {
    updateVisibleData(scrollTop) {
      console.log('updateVisibleData run');
      console.log('updateVisibleData scrollTop', scrollTop);
      scrollTop = scrollTop || 0;
      console.log('this.$el:', this.$el);
      const visibleCount = Math.floor(this.$el.clientHeight / this.itemHeight); // 取得可见区域的可见列表项数量：其实就是props 里面的 height/itemHeight
      console.log('visibleCount', visibleCount);
      const start = Math.floor(scrollTop / this.itemHeight); // 取得可见区域的起始数据索引
      const end = start + visibleCount + (scrollTop ? 1 : 0); // 取得可见区域的结束数据索引:+5是为了防止出现空白
      this.start = start; //测试用
      console.log('this.start', this.start);
      if (this.start >= 10000 - visibleCount) {
        return;
      }
      // this.visibleData = this.data.slice(start, end); // 计算出可见区域对应的数据，让 Vue.js 更新
      this.visibleData.splice(0, this.visibleData.length);
      this.data.slice(start, end).forEach(element => {
        this.visibleData.push(element);
      });
      console.log('this.visibleData', this.visibleData);
      console.log(' this.$refs.listViewContent:', this.$refs.listViewContent);
      this.$refs.listViewContent.style.webkitTransform = `translate3d(0, ${start * this.itemHeight}px, 0)`; // 把可见区域的 top 设置为起始元素在整个列表中的位置（使用 transform 是为了更好的性能）
    },
    handleScroll() {
      console.log('handleScroll run');
      const scrollTop = this.$el.scrollTop;
      console.log('handleScroll scrollTop', scrollTop);
      this.updateVisibleData(scrollTop);
    }
  }
};
</script>
<style lang="scss" scoped>
// relative定位
.list-view {
  box-sizing: content-box;
  overflow: auto;
  position: relative;
  border: 1px solid #aaa;
  width: 300px;

  .list-view-phantom {
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    z-index: -1;
  }

  // absolute定位
  .list-view-content {
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    z-index: 1;

    .list-view-item {
      padding: 0 15px 10px;
      color: #666;
      box-sizing: border-box;

      .item-title {
        background-color: #ccc;
        width: 100%;
        height: 100%;
        line-height: 30px;
      }
    }
  }

  .list-view-start {
    position: fixed;
    top: 10px;
    left: 10px;
    background: rgba(255, 0, 0, 0.4);
    color: #fff;
    width: 100px;
    height: 30px;
    line-height: 30px;
    border-radius: 10px;
  }
}
</style>
