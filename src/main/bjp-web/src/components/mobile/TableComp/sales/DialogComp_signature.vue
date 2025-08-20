<template>
  <div class="signatureDialogComp_wrap">
    <div @click="signature = true">
      <slot></slot>
    </div>

    <q-dialog v-model="signature">
      <q-card style="padding: 3%">
        <!-- <q-card-section>
          <div class="q-card_signature_title">
            <p>잔고확인 사인등록</p>
            <img src="assets/icons/x-solid.svg" @click="signature = false" />
          </div>
        </q-card-section> -->
        <div class="dialog_header">
          <span>잔고확인 사인등록</span>
        </div>

        <div class="signature_wrap">
          <div class="signature_text">
            <p>거래처명</p>
            <p>{{ dataParam.custNm }}</p>
          </div>
          <div class="signature_text">

              <p>잔액</p>
              <p>{{ util.getNumberFormat(dataParam.balanceAAmt) }}</p>
          </div>
          <div class="signature_text">
             <p>확인일자</p>
              <p>{{ dataParam.checkDt }}</p>
          </div>
          <div class="signature_text">
              <p>잔고일자</p>
              <p>{{ dataParam.aprvDt }}</p>
          </div>


          <div class="signature">
            <Vue3Signature
              ref="signatureRef"
              :sigOption="signatureOptions"
              :w="'100%'"
              :h="'240px'"
              :disabled="state.disabled"
              class="canvas"
              @begin="onTouchStart"
            />
          </div>
          <div class="signature_btn">
            <q-btn class="active" dense @click="clearCanvas">취소</q-btn>
            <q-btn class="default" dense @click="saveSignature">사인등록</q-btn>
          </div>
        </div>

        <q-card-actions class="q-card_btn_wrap">
          <div>
            <BtnComp_active btn-label="닫기" v-close-popup />
          </div>
        </q-card-actions>
      </q-card>
    </q-dialog>
  </div>
</template>

<script setup>
//---------------------- Import & Declaration ------------------------//

import { ref, reactive, nextTick, watch, inject } from "vue";
import { api } from "boot/axios";
import Vue3Signature from "vue3-signature";
import * as util from "src/support/util";
import moment from "moment";

import BtnComp_active from "src/components/web/common/BtnComp_active.vue";

const userInfo = util.getUserData();
const signature = ref(false);
const signatureRef = ref(null); // Ref 선언
const bus = inject("bus");

const signatureOptions = reactive({
  penColor: "rgb(0, 0, 0)",
  backgroundColor: "rgb(245,245,245)",
});
const state = reactive({
  disabled: false,
});

const clearCanvas = () => {
  if (signatureRef.value) {
    signatureRef.value.clear(); // Ref를 통해 clear 메소드 호출
    saveYn.value = "N";
  }
};

const props = defineProps({
  data: { type: Object },
});

const dataParam = ref({
  custCd: userInfo.CUST_CD,
  custNm: userInfo.CUST_NM,
  userId: userInfo.USER_ID,
  sn: "",
  balanceAAmt: "",
  aprvDt: "",
  checkDt: moment().format("YYYY.MM.DD"),
  uploadFile: "",
});

const saveYn = ref("N");

//---------------------------- Function ------------------------------//

// 터치 시작
function onTouchStart() {
  saveYn.value = "Y";
}

async function saveSignature() {

  if (saveYn.value === "Y") {
    await nextTick(); // Vue가 업데이트된 후에 실행되도록 보장
    const canvas = signatureRef.value.$el.querySelector("canvas");
    const signatureData = canvas.toDataURL(); // Canvas 엘리먼트의 toDataURL 호출
    // console.log(signatureData); // 서명 데이터 출력 또는 다른 작업 수행

    dataParam.value.uploadFile = signatureData;

    try {
      await api.post("/ordLedger/saveSign", dataParam.value);
      signature.value = false;

      bus.emit("refreshOrdLedger");
    } catch (error) {
      alert(error.response.data);
    }
  }
}

watch(signature, () => {

  console.log(">>>>>>>>"+ JSON.stringify(props.data))
  if (signature.value) {
    dataParam.value.sn = props.data.DATA[0].SN;
    dataParam.value.balanceAAmt = props.data.DATA[0].BALANCE_A_AMT;
    dataParam.value.aprvDt = props.data.APRV_DT;
  }
});
</script>

<style lang="scss">
.dialog_header {
  position: relative;
  width: 100%;
  height: 20%;
  margin-bottom: 10px;
  padding-bottom: 4px;
  font-size: 24px;
  font-weight: 600;
  border-bottom: 1px solid $line-300;
}

.signature_wrap {
  min-width: 50%;
  padding: 0 5%;
  .signature_text {
    @include flex_between;
    gap: 40px;
    p {
      @include fs-5;
      span {
        @include fs-5;
      }
      span:nth-child(1) {
        @include fs-5;
        @include fw-7;
        margin-right: 16px;
      }
    }
    p:nth-child(1) {
      @include fw-7;
      color: $b3;
    }
  }

  .signature {
    .canvas {
      width: 50%;
      height: 240px;
      margin: 16px 0 8px;
    }
  }

  .signature_btn {
    @include flex_between;
    gap: 18px;
    span {
      @include fs-5;
    }
    .active,
    .default {
      padding: 0 16px;
      width: 50%;
      height: 40px;
      background-image: none;
      border-radius: 0 !important;
      &::after,
      &::before {
        box-shadow: none;
        display: none;
      }
    }
    .active {
      background-color: #fff;
      border: 1px solid $gray-a;
      color: $gray-1;
    }
    .default {
      color: #fff;
      background-color: $primary;
    }
  }
}

.q-card_btn_wrap {
  @include flex_end;
  margin: 8px;
  div {
    @include flex_center;
    border-radius: 2.4px;
    gap: 8px;
  }
}
</style>
