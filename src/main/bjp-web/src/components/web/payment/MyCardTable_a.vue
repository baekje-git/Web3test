<template>
  <div class="">
    <q-table
      class="paymentTable_1b"
      :rows="rows"
      :columns="columns"
      row-key="SNO"
      table-header-class="table_header"
      dense
      hide-bottom
      virtual-scroll
      :rows-per-page-options="[0]"
      :virtual-scroll-sticky-size-start="0"
      :selected="selected"
      @row-click="onClickRow"
    >
      <template v-slot:top>
        <div class="q-table_wrap">
          <div class="flex_between" style="margin-bottom: 15px">
            <div class="q-table_header_wrap">
              <div class="q-table_header_title">
                <p>나의 카드정보</p>
              </div>
            </div>
          </div>
        </div>
      </template>
    </q-table>

    <!-- <div class="flex_end">
      <BtnComp_active btn-label="뒤로가기" @click="onClickBack" />
    </div> -->
  </div>
</template>

<script setup>
//---------------------- Import & Declaration ------------------------//

import { ref, computed, onMounted, inject } from "vue";
import { api } from "boot/axios";
import * as util from "src/support/util";
import { useRouter } from "vue-router";

import BtnComp_default from "src/components/web/common/BtnComp_default.vue";
import BtnComp_active from "../common/BtnComp_active.vue";

const userInfo = util.getUserData();
const bus = inject("bus");
const router = useRouter();

const rows = ref([]);
const selected = ref([]);

const searchParam = ref({
  custCd: userInfo.CUST_CD,
});

//---------------------------- Function ------------------------------//

async function fetchItemList() {
  const res = await api.get("/payment/myCard", {
    params: searchParam.value,
  });

  rows.value = res;
}

// 카드정보 그리드 클릭
async function onClickRow(evt, row) {
  bus.emit("myCardDetail", row);

  const index = selected.value.indexOf(row);

  if (index === -1) {
    selected.value = [];
    selected.value.push(row);
  } else {
    selected.value.splice(index, 1);
  }
}

bus.on("refreshMyCardList", async () => {
  await fetchItemList();
});

onMounted(async () => {
  await fetchItemList();
});

const isDisabel = computed(() => {
  if (userInfo.USER_GB_CD === "20") {
    return true;
  }
  return false;
});

//-------------------------- Grid Setting ----------------------------//

const columns = ref([
  { name: "CARD_NM", label: "카드 명칭", field: "CARD_NM", align: "center" },
  {
    name: "CARD_INFO",
    label: "등록 카드 정보",
    field: "CARD_INFO",
    align: "center",
  },
]);
</script>

<style lang="scss" scoped>
.paymentTable_1b {
  // height: 740px;
  height: 690px;
  overflow-y: auto;
  @media screen and (max-width: 830px) {
    height: auto;
  }
}
.flex_between {
  @include flex_between;
}
</style>
