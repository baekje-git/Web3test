<template>
  <div class="LoginLayout_wrap">
    <div class="LoginBox">
      <div class="Login_Title">
        {{ loginTitle }}
      </div>
      <div class="LoginCard_wrap">
        <slot></slot>
        <div class="LoginCopyRigth">
          COPYRIGHT (C) BAEKJE PHARMACEUTICAL Co., Ltd. All rights reserved.
        </div>
      </div>
    </div>
  </div>
</template>
<script setup>
import { computed } from 'vue';

const hostname = window.location.hostname;

const loginTitle = computed(() => {
  switch (hostname) {
    case 'dhhorim.ollehpharm.co.kr':
      return '디에이치호림';
    case 'www.tspharm.kr':
      return '티에스팜';
    case 'kaymed.co.kr':
      return '케이메디칼';
    case 'bj.charmacist.store':
      return '참약사';
    case 'www.gfpharm.co.kr':
      return '대길약품';
    case 'www.reinpharm.com':
      return '리인팜';
    case 'www.nurip.co.kr':
      return '누리팜';
    case 'mediplankorea.com':
      return '메디플랜';
    case 'www.ansimpharm.co.kr':
      return '안심약품';
    default:
      return '백제약품';
  }
});

</script>
<style lang="scss" scoped>

.LoginLayout_wrap {
  @include flex_center;
  width: 100vw;
  height: 100vh;
  background-image: url("/public/assets/images/login_mobile/login_bg_mobile.png");
  background-size: cover;
  background-repeat: no-repeat;
  background-position: center;
  position: relative;
}
.Login_Title {
  @include fs-2;
  @include fw-7;
  color: $w;
  text-align: center;
  margin-bottom: 40px;
}
.LoginCard_wrap {
  min-width: 330px;
  padding: 15% 5%;
  border-radius: 32px;
  box-shadow: 0 3px 6px 0 rgba(0, 0, 0, 0.16);
  background-color: rgba(255, 255, 255, 0.77);
}
.LoginCopyRigth {
  @include fs-8;
  text-align: center;
  margin-top: 100px;
}

</style>
