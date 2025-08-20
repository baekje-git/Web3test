<template>
  <div class="">
    <q-table
      class=""
      row-key="APRV_NO"
      :rows="rows"
      flat
      hide-header
      hide-bottom
      virtual-scroll
      :rows-per-page-options="[0]"
      :virtual-scroll-sticky-size-start="0"
      :loading="loading"
    >
      <template v-slot:loading>
        <div class="m-loading_wrap" v-if="loading === true">
          <q-inner-loading class="loading" :showing="loading" color="primary" />
        </div>
      </template>
      <template v-slot:top>
        <div class="table_Top_wrap">
          <div class="table_Title_mobile">
            <span>{{ TableTitle }}</span>
          </div>
          <div class="filter_area">
            <div class="flex_between" style="gap: 8px">
              <InputComp_mobile
                v-model:input_model_props="searchParam.keyword"
                placeholder="거래처명"
                style="width: 100%"
                @keyup.enter="onClickSearch"
              />
            </div>
            <BtnComp_mobile
              buttonText="검색"
              btnStyle="default"
              class="btn_srch"
              @click="onClickSearch"
            />

          </div>

          <div class="table_totalVal">
            <span class="table_total_Text">
              검색건수 {{ util.getNumberFormat(pmntCnt ?? 0) }}건
            </span>
          </div>
        </div>
      </template>

      <template v-slot:body="props">
        <q-tr>
          <!--
          <q-td>
            <div>{{ props.row.CUST_NM }}</div>
          </q-td>
          <q-td>
            <div>{{ props.row.CUST_ADDRESS }}</div>
          </q-td>
          <q-td>
            <div>{{ props.row.BRCH_NM }}</div>
          </q-td>
          <q-td>
            <div>{{ props.row.DEAL_END_YN }}</div>
          </q-td>
          -->

          <div class="table_wrap">
            <div class="table_item table_item_mobile">
              <q-td class="table-td_mobile">{{ props.row.BRCH_SNM }}</q-td>
            </div>
            <div class="table_item table_item_mobile">
              <q-td class="table-td_mobile">{{ props.row.CUST_NM }}</q-td>
            </div>
            <div class="table_item table_item_mobile">
              <q-td class="table-td_mobile">{{ props.row.DEAL_END_YN }}</q-td>
            </div>
            <div class="table_item table_item_mobile">
              <q-td class="table-td_mobile">{{ props.row.CUST_ADDRESS }}</q-td>
            </div>



          </div>

        </q-tr>
      </template>
    </q-table>
  </div>
</template>

<script setup>
import { ref, inject, onMounted, computed } from "vue";
import { api } from "boot/axios";
import moment from "moment";
import * as util from "src/support/util";

import DialogLayout_mobile from "src/layouts/mobile/DialogLayouts/DialogLayout_mobile.vue";
import BtnComp_mobile from "src/components/mobile/__commonComp/btnComp/BtnComp_mobile.vue";
import InputComp_mobile from "src/components/mobile/__commonComp/inputComp/InputComp_mobile.vue";
import SalesDetailComp_b_mobile from "src/components/mobile/DialogComp/3.salesDialogComp/SalesDetailComp_b_mobile.vue";


//적용 CLASS
const btnResult = ref("on");

//필터 체크
const isFilterChecked = ref(false);
//필터표시
const isActiveBtns = ref([]);
const isActiveBtn1 = ref("3개월");
const isActiveBtn2 = ref("최신순");

const dialog_full = ref(true);
const dialog_filter = ref(false);
const dialog_상세 = ref(false);

const userInfo = util.getUserData();
//로딩
const loading = ref(true);
const show = ref(false);
const nodata_show = ref(false);

const bus = inject("bus");
const rows = ref([]);
const rowval = () => {
  return rows.value.length;
};

const pmntCnt = ref(0);

const props = defineProps({
  TableTitle: {
    type: String,
    default: "TableTitle",
  },
});

const searchParam = ref({
  custCd: userInfo.CUST_CD,
  keyword: ""
});

//조회필터
const filterbtnGroups1 = [
  {
    title: "조회필터",
    subtitle: "복수선택",
    gridStyle: 4,
    btnDatePicker: "off",
    buttons: [
      { btnLabel: "전체" },
      { btnLabel: "전문" },
      { btnLabel: "일반" },
      { btnLabel: "급여" },
      { btnLabel: "비급여" },
      { btnLabel: "비과세" },
      { btnLabel: "수금할인" },
    ],
  },
];


//---------------------------- Function ------------------------------//
// 검색버튼 클릭
async function onClickSearch() {

  await fetchItemList();
}

async function fetchItemList() {
  if (searchParam.value.keyword =="") {
    alert("조회 조건을 선택해 주시기 바랍니다.");
    return;
  }


  loading.value = true;
  const { count, list } = await api.get("/custSearch/listSearch", {
    params: searchParam.value,
  });
  pmntCnt.value = count.CNT;

  rows.value = list;
  loading.value = false;
}
// 조회기간 버튼 클릭
//기간 클릭
bus.off("onClickPeriod");
bus.on("onClickPeriod", async (params) => {
  searchParam.value.startDt = params.value.startDt;
  searchParam.value.endDt = params.value.endDt;

  await fetchItemList();
});

async function onViewDtl(row) {
  for (var i = 0; i < rows.value.length; i++) {
    rows.value[i].dtl_model = false;
  }
  row.dtl_model = true;

  //await bus.emit("pmntItemDetail", props.row);
}

//날짜 임의변경
const onOnChangeDate = () => {
  // let index = filterbtnGroups2[0].buttons.findIndex(
  //   (obj) => obj.btnLabel == "직접입력"
  // );

  sortType2.value = "직접입력";
  searchParam.value.sellMonth = "";
  bus.emit("onOnChangeDate", sortType2.value);
};

// bus.off("closeDialogPopup");
// bus.on("closeDialogPopup", async () => {
//   //직전 선택 값으로 설정
//   console.log(filterbtnGroups2[0].buttons[isActiveBtn1.value]?.btnLabel);
//   selectedSortType2(filterbtnGroups2[0].buttons[isActiveBtn1.value]?.btnLabel);
// });

onMounted(async () => {

});
</script>

<style lang="scss" scoped>
.table_wrap {
  display: grid;
  grid-template-columns: repeat(10, 1fr);
  padding: 8px 0 !important;
  gap: 4px;
  .table_item:nth-child(1){
    @include flex_start;


    grid-column: span 4;
  }
  .table_item:nth-child(2){
    grid-column: span 4;

  }
  .table_item:nth-child(3){
    grid-column: span 2;

  }
  .table_item:nth-child(4){
    grid-column: span 8;

  }
  .table_item:nth-child(5){
    grid-column: span 2;

  }
  .table_item:nth-child(6){
    grid-column: span 1;

  }
  .table_item:nth-child(7){
    grid-column: span 3;

  }
  .table_item:nth-child(8){
    grid-column: span 4;

  }
  .table_item:nth-child(9){
    grid-column: span 2;

  }
  .table_item:nth-child(10){
    grid-column: span 1;

  }
  .table_item:nth-child(11){
    grid-column: span 3;

  }
}

//기간 표시 스타일
// .button-container {
//   .btn-focus {
//     background-image: linear-gradient(to bottom, #1f3f82, #003853) !important;
//     color: #ffffff !important;
//   }
// }
</style>
