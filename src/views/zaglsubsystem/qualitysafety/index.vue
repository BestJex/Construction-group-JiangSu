<template>
  <div class="mod-role">
    <el-card shadow="never">
    <t-form ref="search" @submit.native.prevent @keyup.enter.native="doRefresh()" label-width="100px"  :model="gridOptions.dataSource.serviceInstanceInputParameters">
      <el-row :gutter="10" class="search-top-operate">
        <el-button class="demo-button" type="primary" plain icon="el-icon-download" @click="doExportExcel()">导出</el-button>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="8">
          <el-form-item label="项目名称：" prop="proName">
            <el-input  @submit.native.prevent @keyup.enter.native="doRefresh()" v-model="gridOptions.dataSource.serviceInstanceInputParameters.proName"
                       placeholder="项目名称" clearable></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8" class="search-date-picker">
          <el-form-item label="工程类别：" prop="proType">
            <t-dic-dropdown-select dicType="engineering_type"  v-model="gridOptions.dataSource.serviceInstanceInputParameters.proType"></t-dic-dropdown-select>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="经营方式：" prop="proRunMode">
            <t-dic-dropdown-select dicType="business_type"  v-model="gridOptions.dataSource.serviceInstanceInputParameters.proRunMode"></t-dic-dropdown-select>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row :gutter="20">
        <el-col :span="8">
          <el-form-item label="审批状态："  prop="approvalStatus">
            <t-dic-dropdown-select dicType="approval_status"  v-model="gridOptions.dataSource.serviceInstanceInputParameters.approvalStatus"></t-dic-dropdown-select>
          </el-form-item>
        </el-col>
        <el-col :span="8" >
          <el-form-item label="经办人：" prop="sign"  :maxLength="50">
            <base-user-select role-category="base_rolecategory_trackingpersoninfomr"
                              v-model="gridOptions.dataSource.serviceInstanceInputParameters.sign"
                              :text="gridOptions.dataSource.serviceInstanceInputParameters.trackingPersonInfoMRName"
                              placeholder="请选择" :readOnly="readOnly">
            </base-user-select>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="经办日期"  prop="conCode">
            <t-datetime-range-picker @change="onStartDateRangeChanged" v-model="gridOptions.dataSource.serviceInstanceInputParameters.conCode"></t-datetime-range-picker>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row type="flex" :span="8" justify="end" class="search-bottom-operate">
        <el-col :span="12">
          <el-form-item>
            <el-button @click="doRefresh()" type="primary" icon="el-icon-search">查询</el-button>
            <el-button type="primary" icon="el-icon-circle-close" @click="doReset()">清空</el-button>
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
  import util from '@/util'
  import baseView from '@/base/baseView'

  export default {
    name: 'myTask',
    extends: baseView,
    data() {
      return {
        checkededRows: [],
        processDefinationlist: [],
        startDateRange: null,
        readOnly: false,
        gridOptions: {
          dataSource: {
            serviceInstance: tapp.services.tQsSdfileApproval.getPagedList,
            serviceInstanceInputParameters: {
              searchKey: null,
              processDefinationKey: null,
              dateRange: ''
            }
          },
          grid: {
            offsetHeight: 125, //125:查询部分高度
            mutiSelect: false,
            reduceMethod: this.getSummaries,
            fit: true, // 列的宽度是否自撑开
            columns: [
              {
                prop: 'proName',
                label: '项目名称',
                sortable: true,
                minWidth: 120,
              },
              {
                prop: 'proType',
                label: '项目类型',
                sortable: true,
                minWidth: 120,
                formatter: (row, column, cellValue) => {
                  return util.dataDicFormat('engineering_type', row.proType) // 第一个参数为字典类型值，复用替换字典类型值，第二个为当前cell值
                }
              },
              {
                prop: 'proConstructCompany',
                label: '建设单位',
                sortable: true,
                minWidth: 120,
              },
              {
                prop: 'proTotalInvestment',
                label: '合同金额',
                sortable: true,
                minWidth: 120,
                render: (h, params) => {
                  var self = this
                  return h('t-highlight-view', {
                    props: {
                      value: self.$util.moneyFormat(params.row.proTotalInvestment || 0)
                    }
                  })
                }
              },
              {
                prop: 'proSubCompany',
                label: '所属分公司',
                sortable: true,
                minWidth: 120,
              },
              {
                prop: 'proRunMode',
                label: '经营方式',
                sortable: true,
                minWidth: 120,
                formatter: (row, column, cellValue) => {
                  return util.dataDicFormat('business_type', row.proRunMode) // 第一个参数为字典类型值，复用替换字典类型值，第二个为当前cell值
                }
              },
              {
                prop: 'filePath',
                label: '文件一览',
                sortable: true,
                minWidth: 120,
              },
              {
                prop: 'approvalStatus',
                label: '审批状态',
                sortable: true,
                minWidth: 120,
                formatter: (row, column, cellValue) => {
                  return util.dataDicFormat('approval_status', row.approvalStatus) // 第一个参数为字典类型值，复用替换字典类型值，第二个为当前cell值
                }
              },
              {
                prop: 'sign',
                label: '经办人',
                sortable: true,
                minWidth: 120,
              },
              {
                prop: 'signTime',
                label: '经办日期',
                sortable: true,
                minWidth: 120,
                formatter: (row, column, cellValue) => {
                  return this.$util.dateFormat(row.updatetime, 'YYYY-MM-DD')
                }
              }
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
        this.gridOptions.dataSource.serviceInstanceInputParameters.starttime = val[0];
        this.gridOptions.dataSource.serviceInstanceInputParameters.endtime = val[1];
      },
      handleSelectionChange(val) {
        this.checkededRows = val;
      },
      doReset() {
        this.$refs.search.resetFields();
        this.gridOptions.dataSource.serviceInstanceInputParameters = {}
        this.doRefresh()
      },
      doExportExcel() {
        this.$refs.searchReulstList.exportCSV('重大危险源文件表');
      },
      doRefresh() {
        this.$refs.searchReulstList.refresh();
      },
      getSummaries (param) {
        const {
          columns,
          data,
          reduces
        } = param
        this.reduces = reduces || []

        // 自行计算
        let _sum = 0
        data.map(item => {
          _sum += parseFloat(item.proTotalInvestment)
        })

        const sums = []
        sums[1] = '合计'
        sums[4] = util.moneyFormat(_sum) || '--'
        return sums
      }
    }
  }
</script>

