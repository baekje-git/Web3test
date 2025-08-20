<template>
  <div class="LoginLayout_wrap">
    <div class="LoginBox">
      <div class="Login_Title">
        <div v-if="loginCnt.CNT === 0" class="main_txt">
          백제약품 주식회사<br />
          <b class="fw-7">웹 주문 사이트</b>
        </div>

        <div v-else class="main_txt">
          {{ loginCnt.BRCH_NM }}<br />
          <b class="fw-7">웹 주문 사이트</b>
        </div>
      </div>
      <div class="LoginCard_wrap">
        <slot></slot>
        <div class="LoginCopyRigth">
          &copy; <b>BAEKJE PHARMACEUTICAL Co., Ltd.</b> All rights reserved.
        </div>
      </div>
    </div>
  </div>
</template>
<script setup>
import { ref, onMounted } from "vue";
import { api } from "boot/axios";

const loginCnt = ref({
  CNT: 0,
  BRCH_NM: "",
});

async function selectLoginUrl() {
  try {
    const baseUrl = `${window.location.origin}/`;
    const res = await api.get("/main/selectLoginUrl", {
      params: { url: baseUrl },
    });

    loginCnt.value = res ?? {}; // 객체 전체 할당
  } catch (err) {
    console.error("❌ 에러 발생:", err);
    loginCnt.value = {}; // 초기화
  }
}

onMounted(async () => {
  await selectLoginUrl();
  const url = window.location.href;
  const company = [
    "dhhorim",
    "tspharm",
    "kaymed",
    "charmacist",
    "gfpharm",
    "reinpharm",
    "nurip",
    "mediplankorea",
    "ansimpharm",
  ];

  const companyNm = ref("N");

  company.forEach((m) => {
    if (url.includes(m)) {
      companyNm.value = m;
    }
  });
});
</script>
<style lang="scss" scoped>
// @import url("https://cdn.jsdelivr.net/gh/webfontworld/score@master/SCoreDream.css");
@font-face {
  font-family: "SCoreDream";
  src: url("assets/css/font/S-CoreDream-4Regular.woff") format("woff");
  font-weight: 400;
  font-style: normal;
}
@font-face { 
  font-family: "SCoreDream";
  src: url("assets/css/font/S-CoreDream-5Medium.woff") format("woff");
  font-weight: 500;
  font-style: normal;
}
@font-face {
  font-family: "SCoreDream";
  src: url("assets/css/font/S-CoreDream-6Bold.woff") format("woff");
  font-weight: 600;
  font-style: normal;
}
$ft-login: "SCoreDream";

.LoginLayout_wrap {
  // @include flex_center;
  width: 100vw;
  height: 100vh;
  background-image: url("assets/images/login/bg.jpg");
  background-size: auto 100%;
  background-position: 25% center;
  background-repeat: no-repeat;
  position: relative;
}
.LoginBox {
  width: 100%;
  // padding-top: rem(90px);
  padding-top: 27%;
}
.Login_Title {
  padding: 0 5%;
  line-height: 1em;
  font-size: rem(40px);
  font-weight: 200;
  color: #071b7d;
  span {
    opacity: 0;
    &:nth-child(n + 9) {
      @include fw-7;
    }
  }
  @keyframes fade {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }
  @for $i from 1 to 16 {
    span:nth-child(#{$i}) {
      animation: fade 0.35s 400ms * $i forwards;
    }
  }
}
.LoginCard_wrap {
  position: fixed;
  bottom: 0;
  font-family: $ft-login;
  width: 100%;
  padding: 10% 5% 5%;
  background-color: #fff;
  border-radius: 30px 30px 0 0;
}
.LoginCopyRigth {
  @include fs-7;
  text-align: center;
  margin-top: 15%;
  color: $gray-9;
  word-break: keep-all;
}
</style>
