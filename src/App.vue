<template>
  <s-table rowKey="id" :data-source="tableData" :columns="columns" :scroll="{ y: 600 }" autoHeaderHeight xVirtual
    rangeSelection :pagination="false" ignoreCellKey bordered :animateRows="false" :showSorterTooltip="false"
    summary-fixed>
    <template #headerCell="{ title, column }">
      <div>{{ title }}</div>
      <el-input v-if="column.dataIndex === 'id'" v-model="query.id" placeholder="" clearable />

    </template>
  </s-table>
</template>

<script setup>
import '@surely-vue/table/dist/index.css';
import STable from '@surely-vue/table';

const query = ref({
  id: '',
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
