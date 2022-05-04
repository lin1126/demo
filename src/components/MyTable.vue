<template>
  <a-table bordered
           :data-source="dataSource"
           :columns="columns"
           :customRow="customRow">
    <template #[item]="scope"
              v-for="item in renderArr">
      <template v-if="scope.column.dataIndex == 'name'">
        <span class="move-icon"
              :key="item">
          <HolderOutlined />
        </span>
        <span :key="item">
          {{scope.record.name}}
        </span>
      </template>
      <slot :name="item"
            :scope="scope"></slot>
    </template>
  </a-table>

</template>
<script setup lang="ts">
import { ref, useSlots } from "vue";
import { HolderOutlined } from "@ant-design/icons-vue";

const props = defineProps({
  dataSource: {
    type: Array,
    default: [],
  },
  columns: {
    type: Array,
    default: [],
  },
});
// 插槽的实例
const slots = useSlots();

const tableData = ref([] as any[]);
// 渲染的数据
const renderArr = Object.keys(slots);
if (props.dataSource) {
  tableData.value = props.dataSource; //表单数据
}

let dragItem: any;
let targItem: any;
const emit = defineEmits(["sortData"]);
let rowKey: any;
let rowKey1: any;
let dragleave: any;
// 设置行属性,实现拖拽
const customRow = (record: any) => {
  return {
    draggable: true,
    ondrag(e: any) {
      e.preventDefault();
      dragItem = record;
    },
    ondragleave(e: any) {
      e.preventDefault();
      let sub: any;
      if (e.target.parentNode.nodeName == "TR") {
        sub = e.target.parentNode;
      } else if (e.target.parentNode.parentNode.nodeName == "TR") {
        sub = e.target.parentNode.parentNode;
      } else if (e.target.parentNode.parentNode.parentNode.nodeName == "TR") {
        sub = e.target.parentNode.parentNode.parentNode;
      }
      rowKey = sub.getAttribute("data-row-key");
      sub = sub.children;
      // 记录最后一次离开的行
      dragleave = sub;
      if (
        rowKey != null &&
        rowKey != undefined &&
        rowKey1 != null &&
        rowKey1 != undefined
      ) {
        if (rowKey != rowKey1) {
          for (let index = 0; index < sub.length; index++) {
            sub[index].style.borderBottom = "1px solid  #f0f0f0";
          }
        }
      }
    },
    ondragenter(e: any) {
      e.preventDefault();
      let sub: any;
      if (e.target.parentNode.nodeName == "TR") {
        sub = e.target.parentNode;
      } else if (e.target.parentNode.parentNode.nodeName == "TR") {
        sub = e.target.parentNode.parentNode;
      } else if (e.target.parentNode.parentNode.parentNode.nodeName == "TR") {
        sub = e.target.parentNode.parentNode.parentNode;
      }
      rowKey1 = sub.getAttribute("data-row-key");
      sub = sub.children;
      for (let index = 0; index < sub.length; index++) {
        sub[index].style.borderBottom = "4px solid #1890ff";
      }
    },
    ondrop(e: any) {
      e.preventDefault();
      targItem = record;
      let sub: any;
      if (e.target.parentNode.nodeName == "TR") {
        sub = e.target.parentNode.children;
      } else if (e.target.parentNode.parentNode.nodeName == "TR") {
        sub = e.target.parentNode.parentNode.children;
      } else if (e.target.parentNode.parentNode.parentNode.nodeName == "TR") {
        sub = e.target.parentNode.parentNode.parentNode.children;
      }
      for (let index = 0; index < sub.length; index++) {
        sub[index].style.borderBottom = "1px solid  #f0f0f0";
      }
    },
    ondragend(e: DragEvent) {
      e.preventDefault();
      if (targItem != null && targItem != undefined) {
        if (dragItem !== targItem) {
          const dragItemIndex = tableData.value.indexOf(dragItem);
          const targItemIndex = tableData.value.indexOf(targItem);
          // // 解构交换: 将拖拽行于目标行交换
          // [tableData.value[dragItemIndex], tableData.value[targItemIndex]] = [
          //   tableData.value[targItemIndex],
          //   tableData.value[dragItemIndex],
          // ];
          // 移动拖拽的行,其他行自动向上补齐
          tableData.value.splice(dragItemIndex, 1);
          tableData.value.splice(targItemIndex, 0, dragItem);
          emit("sortData", tableData.value);
        }
      }
      for (let index = 0; index < dragleave.length; index++) {
        dragleave[index].style.borderBottom = "1px solid  #f0f0f0";
      }
      targItem = null;
    },
    ondragover(e: DragEvent) {
      e.preventDefault();
      return false;
    },
  };
};
</script>

<style  scoped>
.move-icon {
  cursor: grab;
  margin: 0 16px;
}
</style>