<template>
  <s-table rowKey="id" :data-source="tableData" :columns="columns" :scroll="{ y: 600 }" autoHeaderHeight xVirtual
    rangeSelection :pagination="false" ignoreCellKey bordered :animateRows="false" :showSorterTooltip="false"
    summary-fixed>
    <template #headerCell="{ title, column }">
      <div>{{ title }}</div>
      <Input v-if="column.dataIndex === 'id'" v-model:value="query.id" placeholder="" />
      <el-input v-if="column.dataIndex === 'age'" v-model="query.age" placeholder="" clearable />

    </template>
  </s-table>
</template>

<script setup>
import '@surely-vue/table/dist/index.css';
import STable from '@surely-vue/table';
import { Input } from 'ant-design-vue'

const query = ref({
  id: '',
  age: '',
})
const tableData = []
const columns = [
  {
    title: 'ID',
    dataIndex: 'id',
    align: "center",
    ellipsis: true,
    resizable: true,
    width: 200,
  },
  {
    title: 'Age',
    dataIndex: 'age',
    align: "center",
    ellipsis: true,
    resizable: true,
    width: 200,
  },
  {
    title: 'Address',
    dataIndex: 'address',
    align: "center",
    ellipsis: true,
    resizable: true,
    width: 500,
  },
  {
    title: 'Name',
    dataIndex: 'name',
    align: "center",
    ellipsis: true,
    resizable: true,
    width: 500,
  }
]


</script>

<style lang="scss" scoped></style>
