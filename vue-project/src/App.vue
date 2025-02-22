<script setup>
import { ref, watchEffect } from "vue";
import { SearchOutlined } from "@ant-design/icons-vue";
import _ from "lodash";

const cevap = ref([]);
const checked = ref(false);
const arananCumle = ref("");
const ilkCevap = ref([]);
const siralamaTipleri = ref({
  title: true,
  id: true,
  userId: true,
  completed: true,
});
const denemeTip = ref(true);

const columns = [
  {
    title: "userId",
    dataIndex: "userId",
    key: "userId",
  },
  {
    title: "id",
    dataIndex: "id",
    key: "id",
  },
  {
    title: "title",
    dataIndex: "title",
    key: "title",
  },
  {
    title: "completed",
    dataIndex: "completed",
    key: "completed",
  },
];

watchEffect(async () => {
  const url = "https://jsonplaceholder.typicode.com/todos";
  const temp = await (await fetch(url)).json();
  cevap.value = temp.map((value) => ({
    ...value,
    completed: value.completed ? "true" : "false",
  }));
  ilkCevap.value = cevap.value;
  console.log({ ilkCevap: ilkCevap.value });
});

function aramaYap() {
  cevap.value = ilkCevap.value.filter((value) => {
    return Object.keys(value).some((key) => {
      return String(value[key]).includes(arananCumle.value);
    });
  });
}

function sirala1(param) {
  denemeTip.value = !denemeTip.value;
  if (!denemeTip.value) {
    cevap.value = _.sortBy(cevap.value, param).reverse();
  } else {
    cevap.value = _.sortBy(cevap.value, param);
  }
}

function sirala2(siralamaÖlçeği) {
  siralamaTipleri.value[`${siralamaÖlçeği}`] =
    !siralamaTipleri.value[`${siralamaÖlçeği}`];
  if (siralamaTipleri.value[`${siralamaÖlçeği}`] == false) {
    cevap.value = _.sortBy(cevap.value, `${siralamaÖlçeği}`).reverse();
  } else {
    cevap.value = _.sortBy(cevap.value, `${siralamaÖlçeği}`);
  }
}
</script>

<template>
  <div :id="checked ? 'body' : ''">
    <input
      style="width: 400px; border-radius: 1cqb"
      placeholder="Localhost Üzerinden Arayın"
      v-model="arananCumle"
      @keyup.capture="aramaYap"
    />
    <a-button
      style="margin-left: 15px"
      type="primary"
      shape="circle"
      @click="aramaYap"
      >Ara</a-button
    >

    <button @click="sirala2('userId')">userId Sırala</button>
    <button @click="sirala2('title')">title Sırala</button>
    <button @click="sirala2('id')">id Sırala</button>
    <button @click="sirala2('completed')">completed Sırala</button>

    <div v-if="cevap.length">
      <div style="margin: 15px; display: flex; justify-content: center"></div>

      <a-table
        :style="checked ? { background: '#4c4b57', color: 'white' } : {}"
        :dataSource="cevap"
        :columns="columns"
        rowKey="id"
      >

      </a-table>
    </div>
    <div style="margin-top: 50px" v-else-if="!cevap.length">
      <a-alert message="Aranan Şey Bulunamadı!" type="error" show-icon />
    </div>
    <br />
    <br />
    <label
      style="margin-top: 602px; margin-right: 15px; margin-bottom: 50px"
      :class="checked ? 'color' : ''"
    >
      Gece Modu:
    </label>
    <a-switch v-model:checked="checked" />
    <br />
  </div>
</template>

<style >
.highlight {
  background-color: rgb(241, 245, 142);
  color: black;
}
#body {
  background-color: currentColor;
}
.color {
  color: white;
}
</style>