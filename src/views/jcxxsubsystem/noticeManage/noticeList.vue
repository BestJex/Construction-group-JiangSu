<template>
  <div class="mod-role">
    <el-row :gutter="10" class="search-top-operate">
      <el-button class="demo-button" type="primary" plain icon="el-icon-download" @click="doExportExcel()">导出
      </el-button>
    </el-row>
    <el-card shadow="never">
      <t-form ref="search" @submit.native.prevent @keyup.enter.native="doRefresh()" label-width="100px"
              :model="gridOptions.dataSource.serviceInstanceInputParameters">
        <t-sub-title :title="'公告列表'"></t-sub-title>
        <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item prop="noticeType" label="公告类型：" aria-placeholder="请选择">
              <t-dic-dropdown-select dicType="notice_type"
                                     v-model="gridOptions.dataSource.serviceInstanceInputParameters.noticeType"
                                     :readOnly="readOnly"></t-dic-dropdown-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="noticeTitle" label="公告标题：">
              <el-input v-model="gridOptions.dataSource.serviceInstanceInputParameters.noticeTitle"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="noticeContent" label="公告内容：">
              <el-input v-model="gridOptions.dataSource.serviceInstanceInputParameters.noticeContent"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item prop="createuser" label="发布人：">
              <el-input v-model="gridOptions.dataSource.serviceInstanceInputParameters.createuser"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="timeLimit" label="有效期：">
              <t-dic-dropdown-select dicType="time_limit"
                                     v-model="gridOptions.dataSource.serviceInstanceInputParameters.timeLimit"
                                     :readOnly="readOnly"></t-dic-dropdown-select>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row type="flex" :span="8" justify="end" class="search-bottom-operate">
          <el-col :span="12">
            <el-form-item>
              <el-button @click="doRefresh()" type="primary" icon="el-icon-search">查询</el-button>
              <el-button @click="doReset" type="primary" icon="el-icon-circle-close">清空</el-button>
            </el-form-item>
          </el-col>
        </el-row>
      </t-form>
    </el-card>
    <el-card shadow="never">
      <t-grid ref="searchReulstList" :options="gridOptions" @selection-change="handleSelectionChange">
      </t-grid>
    </el-card>
  </div>
</template>
<script>
  import baseView from '@/base/baseView'
  import util from '@/util'

  export default {
    name: 'myTask',
    extends: baseView,
    props: {
      readOnly: {
        type: Boolean,
        default: false,
        required: false
      }
    },
    data () {
      return {
        checkededRows: [],
        processDefinationlist: [],
        startDateRange: null,
        gridOptions: {
          dataSource: {
            serviceInstance: tapp.services.tBaseinfoNotice.getPagedList,
            serviceInstanceInputParameters: {
              noticeTitle: null,
              noticeContent: null,
              noticeType: null,
              createuser: null,
              timeLimit: null
            }
          },
          grid: {
            offsetHeight: 125, // 125:查询部分高度
            mutiSelect: false,
            columns: [
              {
                prop: 'noticeTitle',
                label: '公告标题',
                sortable: false
              },
              {
                prop: 'noticeContent',
                label: '公告内容',
                sortable: false
              },
              {
                prop: 'noticeType',
                label: '公告类型',
                sortable: false,
                formatter: (row, column, cellValue) => {
                  return util.dataDicFormat('notice_type', row.noticeType) // 第一个参数为字典类型值，复用替换字典类型值，第二个为当前cell值
                }
              },
              {
                prop: 'fromDept',
                label: '发布部门',
                sortable: false
              },
              {
                prop: 'remark',
                label: '是否置顶',
                sortable: false,
                formatter: (row, column, cellValue) => {
                  return util.dataDicFormat('y_or_n', row.remark) // 第一个参数为字典类型值，复用替换字典类型值，第二个为当前cell值
                }
              },
              {
                prop: 'timeLimit',
                label: '有效期',
                sortable: false,
                formatter: (row, column, cellValue) => {
                  return util.dataDicFormat('time_limit', row.timeLimit) // 第一个参数为字典类型值，复用替换字典类型值，第二个为当前cell值
                }
              },

              {
                prop: 'createuser',
                label: '发布人',
                sortable: false
              },
              {
                prop: 'createtime',
                label: '发布时间',
                sortable: false,
                formatter: (row, column, cellValue) => {
                  return this.$util.dateFormat(row.createtime, 'YYYY-MM-DD')
                }
              }
            ], // 需要展示的列
            defaultSort: {
              prop: 'id',
              order: 'descending'
            }
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
        this.gridOptions.dataSource.serviceInstanceInputParameters = {}
        this.doRefresh()
      },
      doExportExcel () {
        this.$refs.searchReulstList.exportCSV('公告列表')
      },
      doRefresh () {
        this.$refs.searchReulstList.refresh()
      }
    }
  }
</script>

