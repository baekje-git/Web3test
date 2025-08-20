<template>
  <div>
    <ClientComp_mobile />
    <SalesTable_e_mobile TableTitle="거래처검색" />
  </div>
</template>
<script setup>
import ClientComp_mobile from "src/components/mobile/DialogComp/ClientDialogComp/ClientComp_mobile.vue";
import SalesTable_e_mobile from "src/components/mobile/TableComp/sales/SalesTable_e_mobile.vue";
</script>
<style lang="scss" scoped></style>
