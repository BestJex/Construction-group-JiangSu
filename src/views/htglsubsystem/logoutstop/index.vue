<template>
    <div class="mod-role">
      <el-card shadow="never">
        <t-form ref="search"  @submit.native.prevent @keyup.enter.native="doRefresh()" label-width="100px" :model="gridOptions.dataSource.serviceInstanceInputParameters">
            <el-row :gutter="10" class="search-top-operate">
                  <el-button class="demo-button" type="primary" plain icon="el-icon-download" @click="doExportExcel()">导出</el-button>
            </el-row>
            <el-row :gutter="20">
                <el-col :span="8" class="search-date-picker">
                    <el-form-item label="合同编号：">
                      <el-input></el-input>
                    </el-form-item>
                </el-col>
              <el-col :span="8" class="search-date-picker">
                <el-form-item label="合同名称：">
                  <t-dic-dropdown-select dicType="1260865980897300482"
                                         :readOnly="readOnly"></t-dic-dropdown-select>
                </el-form-item>
              </el-col>
              <el-col :span="8" class="search-date-picker">
                <el-form-item label="合同形式：">
                  <el-input></el-input>
                </el-form-item>
              </el-col>
            </el-row>
          <el-row :gutter="20">
              <el-col :span="8" class="search-date-picker">
                <el-form-item label="项目名称：">
                  <el-input></el-input>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row type="flex" :span="8" justify="end" class="search-bottom-operate">
                <el-col :span="12">
                    <el-form-item>
                        <el-button  @click="doRefresh()" type="primary" icon="el-icon-search">查询</el-button>
                        <el-button  icon="el-icon-download" @click="doReset()">
                            <i class="el-icon-delete"></i>清空
                        </el-button>
                    </el-form-item>
                </el-col>
            </el-row>
        </t-form>
        <t-grid ref="searchReulstList" :options="gridOptions" @selection-change="handleSelectionChange">
        </t-grid>
      </el-card>
    </div>
</template>
<script>
    import baseView from '@/base/baseView'
    export default {
        name: 'myTask',
        extends: baseView,
        data() {
            return {
                checkededRows: [],
                processDefinationlist: [],
                startDateRange: null,
                gridOptions: {
                    dataSource: {
                        serviceInstance: tapp.services.tContCancelLog.getPagedList,
                        serviceInstanceInputParameters: {
                            searchKey: null,
                            processDefinationKey: null,
                            dateRange: ''
                        }
                    },
                    grid: {
                        offsetHeight: 125, //125:查询部分高度
                        mutiSelect: false,
                        fit: true, // 列的宽度是否自撑开
                        columns: [
                                                                                                                                                                                                    {
                                            prop: 'cId',
                                            label: '项目名称',
                                            sortable: true,
                                            minWidth: 120,
                                        },
                                                                                                                                                                                                            {
                                            prop: 'opType',
                                            label: '主合同名称',
                                            sortable: true,
                                            minWidth: 120,
                                        },
                                                                                                                                                                                                            {
                                            prop: 'cType',
                                            label: '主合同编号',
                                            sortable: true,
                                            minWidth: 120,
                                        },
                                                                                                                                                                                                            {
                                            prop: 'createtime',
                                            label: '子合同名称',
                                            sortable: true,
                                            minWidth: 120,
                                        },
                                                                                                                                                                                                            {
                                            prop: 'createuser',
                                            label: '子合同编号',
                                            sortable: true,
                                            minWidth: 120,
                                        },
                                                                                                                        ], // 需要展示的列
                        defaultSort: {
                            prop: 'id',
                            order: 'descending'
                        },
                    }
                }
            }
        },
        components: {},
        created() {
            this.loadCodeTableList();
        },
        methods: {
            // 获取码表值
            loadCodeTableList() {
                // 以下为示例
                                                                            },
            onStartDateRangeChanged(val) {
                this.gridOptions.dataSource.serviceInstanceInputParameters.startDateBegin = val[0];
                this.gridOptions.dataSource.serviceInstanceInputParameters.startDateEnd = val[1];
            },
            handleSelectionChange(val) {
                this.checkededRows = val;
            },
            doReset() {
                this.$refs.search.resetFields();
            },
            doExportExcel() {
                this.$refs.searchReulstList.exportCSV('${comments}表');
            },
            doRefresh() {
                this.$refs.searchReulstList.refresh();
            }
        }
    }
</script>

