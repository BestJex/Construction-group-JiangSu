<template>
  <div class="mod-role">
    <el-row :gutter="20" class="page-title">
      <el-col>
        <div class="title">项目诉讼列表</div>
      </el-col>
    </el-row>
    <el-row :gutter="10" class="search-top-operate">
      <el-button class="demo-button" type="primary" plain icon="el-icon-download" @click="doExportExcel()">导出
      </el-button>
    </el-row>
    <el-card shadow="never">
    <t-form ref="search" @submit.native.prevent @keyup.enter.native="doRefresh()" label-width="100px"
            :model="gridOptions.dataSource.serviceInstanceInputParameters">
      <el-row :gutter="20">
        <el-col :span="8">
          <el-form-item label="项目编号：" prop="proCode">
            <el-input @submit.native.prevent @keyup.enter.native="doRefresh()"
                      v-model="gridOptions.dataSource.serviceInstanceInputParameters.proCode" placeholder="项目编号"
                      clearable></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="项目名称：" prop="proName">
            <el-input @submit.native.prevent @keyup.enter.native="doRefresh()"
                      v-model="gridOptions.dataSource.serviceInstanceInputParameters.proName" placeholder="项目名称"
                      clearable></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="建设单位：" prop="proConstructCompany">
            <el-input @submit.native.prevent @keyup.enter.native="doRefresh()"
                      v-model="gridOptions.dataSource.serviceInstanceInputParameters.proConstructCompany" placeholder="建设单位"
                      clearable></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="工程类别：" prop="proType">
            <t-dic-dropdown-select dicType="engineering_type" v-model="gridOptions.dataSource.serviceInstanceInputParameters.proType" :readOnly="readOnly"></t-dic-dropdown-select>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="经营方式：" prop="proRunMode">
            <t-dic-dropdown-select dicType="business_type" v-model="gridOptions.dataSource.serviceInstanceInputParameters.proRunMode" :readOnly="readOnly"></t-dic-dropdown-select>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="诉讼状态：" prop="litigationStatus">
            <t-dic-dropdown-select dicType="litigation_status" v-model="gridOptions.dataSource.serviceInstanceInputParameters.litigationStatus" :readOnly="readOnly"></t-dic-dropdown-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row type="flex" :span="8" justify="end" class="search-bottom-operate">
        <el-col :span="12">
          <el-form-item>
            <el-button @click="doRefresh()" class="demo-button" type="primary" plain icon="el-icon-search">查询
            </el-button>
            <el-button class="demo-button" type="primary" icon="el-icon-circle-close" @click="doReset()">清空
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
  import util from '@/util'
  import baseView from '@/base/baseView'

  export default {
    name: 'myTask',
    extends: baseView,
    props: {
      readOnly: {
        type: Boolean,
        default: false,
        required: false
      },
    },
    data () {
      return {
        checkededRows: [],
        processDefinationlist: [],
        startDateRange: null,
        gridOptions: {
          dataSource: {
            serviceInstance: tapp.services.proLitigation.getPagedList,
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
                prop: 'proCode',
                label: '项目编号',
                minWidth: 120
              },
              {
                prop: 'proName',
                label: '项目名称'
              },
              {
                prop: 'proConstructCompany',
                label: '建设单位'
              },
              {
                prop: 'proType',
                label: '工程类别',
                formatter: (row, column, cellValue) => {
                  return util.dataDicFormat('engineering_type', row.proType) // 第一个参数为字典类型值，复用替换字典类型值，第二个为当前cell值
                }
              },
              {
                prop: 'proSubCompany',
                label: '分公司'
              },
              {
                prop: 'proBuildArea',
                label: '建筑面积'
              },
              {
                prop: 'proTotalInvestment',
                label: '总投资'
              },
              {
                prop: 'proRunMode',
                label: '经营方式',
                formatter: (row, column, cellValue) => {
                  return util.dataDicFormat('business_type', row.proRunMode) // 第一个参数为字典类型值，复用替换字典类型值，第二个为当前cell值
                }
              },
              {
                prop: 'litigationCode',
                label: '诉讼编号'
              },
              {
                prop: 'signTime',
                label: '标记时间',
                minWidth: 100,
                formatter: (row, column, cellValue) => {
                  return this.$util.dateFormat(row.signTime, 'YYYY-MM-DD')
                }
              },
              {
                prop: 'litigationStatus',
                label: '诉讼状态',
                minWidth: 120,
                formatter: (row, column, cellValue) => {
                  return util.dataDicFormat('litigation_status', row.litigationStatus) // 第一个参数为字典类型值，复用替换字典类型值，第二个为当前cell值
                }
              },
              {
                prop: 'freezingAmount',
                label: '冻结金额',
                minWidth: 120
              },
              {
                prop: 'cancelTime',
                label: '标记取消时间',
                minWidth: 120,
                formatter: (row, column, cellValue) => {
                  return this.$util.dateFormat(row.cancelTime, 'YYYY-MM-DD')
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
    created () {
      this.loadCodeTableList()
    },
    methods: {
      // 获取码表值
      loadCodeTableList () {
        // 以下为示例
      },
      onStartDateRangeChanged (val) {
        this.gridOptions.dataSource.serviceInstanceInputParameters.startDateBegin = val[0]
        this.gridOptions.dataSource.serviceInstanceInputParameters.startDateEnd = val[1]
      },
      handleSelectionChange (val) {
        this.checkededRows = val
      },
      doReset () {
        this.$refs.search.resetFields()
      },
      doExportExcel () {
        this.$refs.searchReulstList.exportCSV('${comments}表')
      },
      doRefresh () {
        this.$refs.searchReulstList.refresh()
      }
    }
  }
</script>

