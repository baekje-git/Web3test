<template>
  <div class="zoom2-container">
    <img
      ref="imageRef"
      :src="imgUrl"
      alt="Image"
      style="pointer-events: auto"
    />
    <div class="zoom-icon" @click="zoomImage">
      <img src="assets/icons/magnify.svg" alt="icon" />
    </div>
  </div>
  <div class="zoom-btn">
    <BtnComp_mobile
      buttonText="확인"
      btnStyle="default"
      btnSize="lg"
      style="width: 160px"
      v-close-popup
    />
  </div>
</template>

<script setup>
import { ref, onBeforeUnmount, inject, defineProps, onMounted } from "vue";
import { api } from "boot/axios";
import mediumZoom from "medium-zoom";
import BtnComp_mobile from "src/components/mobile/__commonComp/btnComp/BtnComp_mobile.vue";

const bus = inject("bus");

const imageRef = ref(null); // 이미지 DOM 참조
const imgUrl = ref(""); // 이미지 src URL 저장용

const props = defineProps({
  DLV_BRCH_CD: { type: String, default: "" },
  A_APRV_DT: { type: String, default: "" },
  APRV_NO: { type: String, default: "" },
});

// API 호출에 사용할 파라미터
const searchParam = ref({
  dlvBrchCd: props.DLV_BRCH_CD,
  aprvDt: props.A_APRV_DT,
  aprvNo: props.APRV_NO,
  invoiceNo: `${props.DLV_BRCH_CD}${props.A_APRV_DT}${props.APRV_NO}`,
});

let zoomInstance = null;

onMounted(async () => {
  try {
    // 이미지 URL 설정
    imgUrl.value =
      `${process.env.API_URL}/cmmnFile/agreementFileDownLoad2` +
      `?DLV_BRCH_CD=${props.DLV_BRCH_CD}` +
      `&APRV_DT=${props.A_APRV_DT}` +
      `&APRV_NO=${props.APRV_NO}` +
      `&invoiceNo=${props.DLV_BRCH_CD}${props.A_APRV_DT}${props.APRV_NO}`;

    // 이미지 확대(zoom) 초기화
    zoomInstance = mediumZoom(imageRef.value);
  } catch (error) {
    console.error("이미지 또는 데이터 요청 실패:", error);
  }
});

onBeforeUnmount(() => {
  if (zoomInstance) zoomInstance.detach();
});

const zoomImage = () => {
  if (zoomInstance) zoomInstance.zoom();
};
</script>

<style lang="scss" scoped>
.zoom-container {
  @include flex_center;
  position: relative;
  padding: 0 16px;
  img {
    width: 100%;
  }
  .zoom-icon {
    @include flex_center;
    position: absolute;
    bottom: 7%;
    right: 7%;
    img {
      width: 32px;
    }
  }
}
.zoom-btn {
  @include flex_center;
  margin-top: 16px;
}
</style>
