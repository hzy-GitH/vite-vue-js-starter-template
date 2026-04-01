<template>
 <s-table ref="tableRef" rowKey="rowKey" :data-source="tableData" :columns="columns" :scroll="{ y: 600 }" autoHeaderHeight xVirtual
    :rangeSelection="true" :pagination="false" ignoreCellKey bordered :animateRows="false" :showSorterTooltip="false"
    summary-fixed @cellClick="handleRowClick"	@cellKeydown="cellKeydown">
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

const columns =  ref([])
const tableData = ref([])
for(let i = 0; i < 1000; i++) {
  tableData.value.push({
    rowKey: i,
    id: i,
    age: i,
    address: '',
    name: '',
  })
  columns.value.push({
    title: 'title' + i,
    dataIndex: 'id'+ i,
	key: 'id'+ i,
    align: "center",
    ellipsis: true,
    resizable: true,
    width: 200,
	editable:true,
    editableTrigger: 'click',
  })
}

const tableRef = ref(null)

const currentRowkey = ref()
const currentColumn = ref()
const activeKey = ref()
// 键盘导航键集合
const NAVIGATION_KEYS = ['ArrowDown', 'ArrowUp', 'ArrowRight', 'ArrowLeft', 'Enter'];

// 检查是否为导航键
const isNavigationKey = key => NAVIGATION_KEYS.includes(key);

const handleRowClick = (event, params) => {
	currentRowkey.value = params.recordIndexs[0];
	currentColumn.value = params.column;

	activeKey.value = params.column.dataIndex;

  	// 强制刷新, 使得高亮列生效
	tableData.value = [...tableData.value];
};

// 获取当前列索引
const getCurrentColumnIndex = () => {
	const columnKey = currentColumn.value?.dataIndex || activeKey.value;
	if (!columnKey) return -1;
	return columns.value.findIndex(i => i.dataIndex === columnKey);
};

// 获取当前列对象
const getCurrentColumn = () => {
	const index = columns.value.findIndex(i => i.dataIndex === activeKey.value);
	return index >= 0 ? columns.value[index] : null;
};

// 更新选中范围和触发事件
const updateSelectionAndEmit = (rowIndex, key) => {
	const dataLength = tableData.value.length;
	if (dataLength === 0) return;

	const safeIndex = Math.max(0, Math.min(rowIndex, dataLength - 1));
	const record = tableData.value[safeIndex];

	if (!record) return;

	tableRef.value?.clearAllSelectedRange();
	tableRef.value?.appendCellToSelectedRange({
		columnsKey: [activeKey.value],
		columnStartKey: activeKey.value,
		columnEndKey: activeKey.value,
		rowStartIndex: safeIndex,
		rowEndIndex: safeIndex,
	});

};

// 处理键盘导航
const handleKeyboardNavigation = (key, event) => {
	if (!isNavigationKey(key)) return;

	const maxRowIndex = tableData.value.length - 1;
	const maxColIndex = columns.value.length - 1;
	const currentRowIndex = Number.isInteger(currentRowkey.value) ? currentRowkey.value : -1;
	const currentColumnValue = currentColumn.value || getCurrentColumn();

	if (maxRowIndex < 0 || maxColIndex < 0 || currentRowIndex < 0 || !currentColumnValue) return;

	let newRowIndex = currentRowIndex;
	let newColumn = currentColumnValue;
	let nextActiveKey = activeKey.value;

	switch (key) {
		case 'ArrowDown':
			newRowIndex = Math.min(currentRowIndex + 1, maxRowIndex);
			break;
		case 'ArrowUp':
			newRowIndex = Math.max(currentRowIndex - 1, 0);
			break;
		case 'ArrowLeft': {
			const colIndex = getCurrentColumnIndex();
			if (colIndex < 0) return;
			const newColIndex = Math.max(0, colIndex - 1);
			newColumn = columns.value[newColIndex];
			nextActiveKey = newColumn?.dataIndex || '';
			break;
		}
		case 'ArrowRight': {
			const colIndex = getCurrentColumnIndex();
			if (colIndex < 0) return;
			const newColIndex = Math.min(maxColIndex, colIndex + 1);
			newColumn = columns.value[newColIndex];
			nextActiveKey = newColumn?.dataIndex || '';
			break;
		}
		case 'Enter':
			newRowIndex = Math.min(currentRowIndex + 1, maxRowIndex);
			break;
	}

	const rowUnchanged = newRowIndex === currentRowIndex;
	const colUnchanged = (newColumn?.dataIndex || '') === (currentColumnValue?.dataIndex || '');

	if (rowUnchanged && colUnchanged) {
		if (event) {
			event.preventDefault();
			event.stopPropagation();
		}
		return;
	}

	// 关闭当前编辑器
	const rowKey = currentRowkey.value;
	tableRef.value?.closeEditor([
		{
			columnKey: activeKey.value,
			rowKey,
		},
	]);

	if (nextActiveKey !== activeKey.value) {
		activeKey.value = nextActiveKey;
	}

	// 如果列可编辑，打开编辑器
	if (newColumn?.editable && newRowIndex >= 0 && newRowIndex <= maxRowIndex) {
		openEdit(newRowIndex);
	}

	currentRowkey.value = newRowIndex;
	currentColumn.value = newColumn;

	updateSelectionAndEmit(newRowIndex, key);
	tableRef.value?.ensureRowVisible(newRowIndex);
	
	if (newColumn.dataIndex === columns.value[columns.value.length - 2].dataIndex) {
		tableRef.value.scrollTo({ columnIndex: columns.value.length - 1 });
	}
};

function cellKeydown(e, params) {
	if (isNavigationKey(e.key)) {
		// 更新当前单元格位置
		currentRowkey.value = params.cellPosition.rowIndex;
		currentColumn.value = params.cellPosition.column;
		activeKey.value = currentColumn.value?.dataIndex || '';

		// 使用统一的键盘导航处理
		handleKeyboardNavigation(e.key, e);
	}
}

const openEdit = rowKey => {
	const safeRowKey = rowKey === -1 ? 0 : rowKey;
	tableRef.value?.openEditor([
		{
			columnKey: activeKey.value,
			rowKey: safeRowKey,
		},
	]);
};


</script>

<style>
.surely-table-wrapper {
	width:100%;
	max-width:1000px !important;
}
</style>
