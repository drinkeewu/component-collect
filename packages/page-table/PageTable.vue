<template>
  <div class="comp-page-table">
    <el-table
      ref="pageTable"
      v-loading="loading"
      class="custom-table"
      header-cell-class-name="custom-table-cell"
      cell-class-name="custom-table-cell"
      :data="data"
      element-loading-text="加载中..."
      :row-class-name="tableRowClassName"
      :row-key="rowKey"
      :height="tableHeight"
      border
      @selection-change="onSelectionChange"
    >
      <el-table-column
        v-if="selection"
        align="center"
        type="selection"
        width="50"
        fixed="left"
        :selectabled="selectabled"
        :reserve-selection="reserveSelection"
      />
      <el-table-column
        v-if="data.length > 0 && !hideDefaultIndex"
        label="序号"
        type="index"
        width="60"
        align="center"
        fixed="left"
      />
      <el-table-column
        v-for="(col, index) in cols"
        :key="`col-${index}`"
        :label="col.label"
        :prop="col.prop"
        align="center"
        :fixed="col.fixed || false"
        :min-width="col.minWidth || tableColMinWidth(col.label)"
        :show-overflow-tooltip="showTooltip"
      />
      <el-table-column
        v-if="operator"
        fixed="right"
        label="操作"
        :width="operatorWidth"
        align="center"
      >
        <template slot-scope="scope">
          <flex justify="center">
            <slot
              name="operator"
              :data="scope"
            />
          </flex>
        </template>
      </el-table-column>
    </el-table>

    <flex
      justify="center"
      padding-y="20px"
    >
      <el-pagination
        class="pagination"
        background
        layout="total, sizes, prev, pager, next, jumper"
        :pager-count="5"
        :current-page="pagination.pageNum"
        :page-sizes="pagination.pageSizes"
        :page-size="pagination.pageSize"
        :total="pagination.total"
        @current-change="onPageNumChange"
        @size-change="onPageSizeChange"
      />
    </flex>
  </div>
</template>

<script>
import Vue from 'vue';
import {
  Table, TableColumn, Pagination, Loading,
} from 'element-ui';
import Flex from '../flex';
import { tableColMinWidth } from '../../utils';
import 'element-ui/lib/theme-chalk/index.css'

Vue.use(Loading.directive);
Vue.prototype.$loading = Loading.service;

export default {
  name: 'PageTable',
  components: {
    'el-table': Table,
    'el-table-column': TableColumn,
    'el-pagination': Pagination,
    flex: Flex,
  },
  props: {
    data: {
      type: Array,
      default() {
        return [];
      },
    },
    cols: {
      type: Array,
      default() {
        return [];
       
      },
    },
    pagination: {
      type: Object,
      default() {
        return {
          pageNum: 1,
          pageSize: 20,
          total: 0,
          pageSizes: [10, 20, 30, 40, 50, 100],
        };
      },
    },
    operator: {
      type: Boolean,
      default: false,
    },
    operatorWidth: {
      type: [String, Number],
      default: 60,
    },
    selection: {
      type: Boolean,
      default: false,
    },
    loading: {
      type: Boolean,
      default: false,
    },
    showTooltip: {
      type: Boolean,
      default: false,
    },
    rowKey: {
      type: String,
      default: '',
    },
    reserveSelection: {
      type: Boolean,
      default: false,
    },
    // eslint-disable-next-line vue/require-default-prop
    tableHeight: {
      type: [String, Number],
    },
    selectabled: {
      type: [Function, Boolean],
      default: true,
    },
    hideDefaultIndex: {
      type: Boolean,
      default: false,
    },

  },
  data() {
    return {
      tableColMinWidth,
    };
  },
  mounted() {
    this.$emit('mounted', this.$refs.pageTable);
  },
  methods: {
    onPageSizeChange(val) {
      this.$emit('size-change', val);
    },
    onPageNumChange(val) {
      this.$emit('current-change', val);
    },
    onSelectionChange(list) {
      this.$emit('selection-change', list);
    },
    tableRowClassName({ rowIndex }) {
      if (rowIndex % 2 > 0) {
        return 'highlight-row';
      }
      return '';
    },
  },
};
</script>

<style scoped >
</style>
