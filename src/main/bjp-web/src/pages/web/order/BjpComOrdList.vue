<template>
  <div>
    <PageLayout_h>
      <template v-slot:pageTit>통합주문</template>
      <template v-slot:leftSlot>
        <CardLayout>
          <OrderTable_1a btn_defaultLink="" />
        </CardLayout>


      </template>
      <template v-slot:left_bottom_left>
        <CardLayout >
          <OrderTable_3a  />
        </CardLayout>

      </template>
      <template v-slot:left_bottom_right>
        <CardLayout >
          <OrderTable_4a  />
        </CardLayout>

      </template>
      <template v-slot:rightSlot>
        <CardLayout >
          <div class="inner_layout rt">
            <OrderTable_2a  />
          </div>
        </CardLayout>
        <!--
        <CardLayout v-for="(table, index) in rightSlots" :key="index">
          <div class="inner_layout rt">
            <component :is="table" />
          </div>
        </CardLayout>
        -->
      </template>
    </PageLayout_h>
  </div>
</template>
<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from "vue";
import PageLayout_h from "src/layouts/web/common/PageLayout_h.vue";
import CardLayout from "src/layouts/web/common/CardLayout.vue";
import OrderTable_1a from "src/components/web/order/OrderTable_1a.vue";
import OrderTable_2a from "src/components/web/order/OrderTable_2a.vue";
import OrderTable_3a from "src/components/web/order/OrderTable_3a.vue";
import OrderTable_4a from "src/components/web/order/OrderTable_4a.vue";

// 화면 크기에 따라 레이아웃을 변경 추가 240213
const isSmallScreen = ref(window.innerWidth < 830);
const updateScreenSize = () => {
  isSmallScreen.value = window.innerWidth < 830;
};
onMounted(() => {
  window.addEventListener("resize", updateScreenSize);
});
onBeforeUnmount(() => {
  window.removeEventListener("resize", updateScreenSize);
});

// 왼쪽 슬롯

// 오른쪽 슬롯
const rightSlots = computed(() => {
  return isSmallScreen.value
  /*
    ? [OrderTable_3a, OrderTable_4a] // 작은 화면에서 순서 변경
    : [OrderTable_2a, OrderTable_4a]; // 기본 순서
    */
    ? [OrderTable_3a, OrderTable_4a] // 작은 화면에서 순서 변경
    : [OrderTable_2a]; // 기본 순서
});
</script>
<style lang="scss" scoped></style>
