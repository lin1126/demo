<template>
  <div style="height:100px;"></div>
  <MyTable class="my-table"
           :dataSource='dataSource'
           :columns="columns"
           @sortData="_sortData">
    <template #bodyCell="{scope}">
      <template v-if="scope.column.dataIndex === 'operation'">
        <span @click="showConfirm(scope.record.key)">
          <a>Delete</a>
        </span>
      </template>
    </template>
  </MyTable>
</template>

<script setup lang="ts">
import { ref, createVNode } from "vue";
import { Modal } from "ant-design-vue";
import { ExclamationCircleOutlined } from "@ant-design/icons-vue";

import MyTable from "./components/MyTable.vue";
const dataSource = ref([
  {
    key: "0",
    name: "Edward King 0",
    age: 99,
    address: "London, Park Lane no. 0",
  },
  {
    key: "1",
    name: "Edward King 1",
    age: 32,
    address: "London, Park Lane no. 1",
  },
  {
    key: "2",
    name: "Edward King 2",
    age: 32,
    address: "London, Park Lane no. 2",
  },
  {
    key: "3",
    name: "Edward King 3",
    age: 32,
    address: "London, Park Lane no. 3",
  },
  {
    key: "4",
    name: "Edward King 4",
    age: 32,
    address: "London, Park Lane no. 4",
  },
]);
const columns = ref([
  {
    title: "name",
    dataIndex: "name",
    width: "30%",
  },
  {
    title: "age",
    dataIndex: "age",
  },
  {
    title: "address",
    dataIndex: "address",
  },
  {
    title: "operation",
    dataIndex: "operation",
  },
]);
// 声明method方法
const deleteRow = (key: string) => {
  dataSource.value = dataSource.value.filter((item) => item.key !== key);
};
// 删除确认框;
const showConfirm = (key: string) => {
  Modal.confirm({
    title: "是否删除该数据？",
    icon: createVNode(ExclamationCircleOutlined),
    onOk() {
      deleteRow(key);
    },
    onCancel() {
      console.log("Cancel");
    },
  });
};
// 获取返回的数据
const _sortData = (data: any) => {
  console.log("5555555", data);
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.my-table {
  width: 60%;
  margin: 0 auto;
}
</style>
