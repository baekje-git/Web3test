<template>
  <q-dialog v-model="isOpen" persistent>
    <q-card style="padding: 32px">
      <div class="dialog_header">
        <span>신규 거래문의</span>
      </div>
      <div class="dialog_body">
        <div>
          <div>
            <form>
              <div>
                <div class="dialog_txt">
                  아래 내용을 기재하신 후 전송버튼을 누르시면, 신속하게
                  연락드리겠습니다.
                </div>
                <div class="table_wrap">
                  <q-markup-table separator="cell">
                    <tbody>
                      <tr>
                        <th scope="row">약국명</th>
                        <td>
                          <q-input
                            v-model="newQaData.drugstoreName"
                            type="text"
                            placeholder="약국명"
                            maxlength="12"
                            class="dia_input"
                          />
                        </td>
                      </tr>
                      <tr>
                        <th scope="row">약사명</th>
                        <td>
                          <q-input
                            v-model="newQaData.pharmacistName"
                            type="text"
                            placeholder="약사명"
                            maxlength="6"
                            class="dia_input"
                          />
                        </td>
                      </tr>
                      <tr>
                        <th scope="col">
                          주소<br />
                          <p>(상세주소 제외)</p>
                        </th>
                        <td>
                          <q-input
                            v-model="newQaData.address"
                            type="text"
                            placeholder="(예) 서울시 구로구"
                            maxlength="20"
                            class="dia_input"
                          />
                        </td>
                      </tr>
                      <tr>
                        <th scope="row">연락처</th>
                        <td>
                          <q-input
                            v-model="newQaData.cellPhoneNumber"
                            maxlength="13"
                            placeholder="휴대폰 번호"
                            :onkeyup="onKeyupCellPhoneNumber"
                            class="dia_input"
                          />
                        </td>
                      </tr>
                    </tbody>
                  </q-markup-table>
                </div>
              </div>

              <div class="btn_wrap">
                <q-btn class="btn_trans" label="닫기" @click="onClickClose" />
                <q-btn
                  class="btn_close"
                  label="전송"
                  color="primary"
                  @click="onClickTransmission"
                />
              </div>
            </form>
          </div>
        </div>
      </div>
    </q-card>
  </q-dialog>
</template>
<script setup>
//--------------------------- Import -----------------------------//

import { ref, computed, defineEmits } from "vue";
import * as util from "src/support/util";
import { api } from "boot/axios";

const emit = defineEmits(["close"]);

//------------------------- Declaration --------------------------//

const props = defineProps({
  isOpen: { type: Boolean },
});

const isOpen = computed(() => {
  return props.isOpen;
});

const newQaData = ref({
  drugstoreName: "",
  pharmacistName: "",
  address: "",
  cellPhoneNumber: "",
  ctt: "",
});

//--------------------------- Event -----------------------------//

async function initNewQaData() {
  newQaData.value.drugstoreName = "";
  newQaData.value.pharmacistName = "";
  newQaData.value.address = "";
  newQaData.value.cellPhoneNumber = "";
}

// 전송 버튼 클릭
async function onClickTransmission() {
  if (newQaData.value.drugstoreName.length === 0) {
    alert("약국명을 입력하세요.");
    return;
  } else if (newQaData.value.pharmacistName.length === 0) {
    alert("약사명을 입력하세요.");
    return;
  } else if (newQaData.value.address.length === 0) {
    alert("주소를 입력하세요.");
    return;
  } else if (newQaData.value.cellPhoneNumber.length === 0) {
    alert("휴대폰 번호를 입력하세요.");
    return;
  }

  const drugstoreName = newQaData.value.drugstoreName;
  const pharmacistName = newQaData.value.pharmacistName;
  const address = newQaData.value.address;
  const cellPhoneNumber = newQaData.value.cellPhoneNumber;

  newQaData.value.ctt = `[${drugstoreName}] [약사명 : ${pharmacistName}] [주소 : ${address}] [연락처 : ${cellPhoneNumber}] [Web신청]`;

  await api.post("/main/newQa", newQaData.value);

  util.showNotify("신규 거래문의가 등록이 완료되었습니다.");

  await initNewQaData();
  emit("close");
}

async function onClickClose() {
  await initNewQaData();
  emit("close");
}

function onKeyupCellPhoneNumber() {
  const cellPhoneNumber = newQaData.value.cellPhoneNumber;

  newQaData.value.cellPhoneNumber = cellPhoneNumber
    .replace(/[^0-9]/g, "")
    .replace(
      /(^02|^0505|^1[0-9]{3}|^0[0-9]{2})([0-9]+)?([0-9]{4})$/,
      "$1-$2-$3"
    )
    .replace("--", "-");
}
</script>
<style lang="scss" scoped>
.dialog_header {
  position: relative;
  width: 100%;
  height: 42px;
  padding-bottom: 4px;
  font-size: 24px;
  font-weight: 600;
  border-bottom: 1px solid $line-300;
}
.btn_wrap {
  @include flex_between;
  margin-top: 40px;
  gap: 10px;
}
.btn_trans,
.btn_close {
  width: 50%;
  border-radius: 0 !important;
  &::after,
  &::before {
    box-shadow: none;
  }
}
.btn_trans {
  color: #111;
  background-color: #fff;
  border: 1px solid $gray-a;
}

.btn_trans::v-deep span {
  color: #111 !important;
}

.dialog_txt {
  @include fs-5;
  padding: 32px 0 4px;
  color: $gray-9;
  font-weight: 600;
}
.table_wrap {
  width: 536px;
  border-top: 1px solid $gray-9;
  border-bottom-color: $td-line;
  tr:focus td::before,
  tr:hover td::before {
    display: none;
  }
  tr:not(:nth-child(1)) th,
  tr:not(:nth-child(1)) td {
    border-top: 1px solid $td-line;
  }
  th {
    width: 120px;
    @include fs-5;
    text-align: right;
    background: #f8f8f8;
  }
  td {
    @include fs-5;
    border-color: $td-line !important;
    border-bottom: none;
  }
  .q-input {
    padding: 0 10px 0 5px;
  }
}
</style>
