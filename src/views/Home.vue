<template>
  <div class="home">
    <CustomList :data="data" :height="height" :itemHeight="itemHeight">
      <template v-slot:default="{ visibleData }">
        <div
          class="list-view-item"
          :style="{
            height: itemHeight + 'px'
          }"
          v-for="(item, index) in visibleData"
          :key="index"
        >
          <div class="item-title">{{ item }}</div>
        </div>
      </template>
    </CustomList>
  </div>
</template>

<script>
import CustomList from '@/components/CustomList.vue';

export default {
  name: 'Home',
  components: {
    CustomList
  },
  data() {
    const data = [];
    for (let i = 0; i < 100000; i++) {
      data.push('列表' + i);
    }
    return {
      data,
      height: 400,
      itemHeight: 40
    };
  }
};
</script>

<style lang="scss" scoped>
.home {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
}
</style>
