<template>
  <div class="acceptance-bill-table-container">
    <el-row class="fina-function-button">
      <el-col :span="4">
        <el-button @click="openDialog" type="primary">到账：预付款</el-button>
      </el-col>
      <el-col :span="6">
        <span>小计金额：{{ $util.moneyFormat(totalAmount) }}万元</span>
      </el-col>
    </el-row>
    <el-table :data="dataInfo" border size="mini">
      <el-table-column prop="bRealAmount" label="可使用金额（万元）" align="center">
        <template slot-scope="scope">
          <span>{{ $util.moneyFormat(scope.row.bRealAmount) }}</span>
        </template>
      </el-table-column>
      <el-table-column prop="payment" label="本次付款金额（万元）" align="center">
        <template slot-scope="scope">
          <span>{{ $util.moneyFormat(scope.row.payment) }}</span>
        </template>
      </el-table-column>
      <el-table-column label="本次付款扣除管理费" align="center">
        <el-table-column prop="achievement" label="管理费（%）" align="center"></el-table-column>
        <el-table-column prop="achievement" label="扣款（万元）" align="center">
          <template slot-scope="scope">
            <span>{{ $util.moneyFormat(scope.row.payment) }}</span>
          </template>
        </el-table-column>
      </el-table-column>
    </el-table>
    <el-dialog title="项目到帐请款列表" :visible.sync="dialogVisible" width='80%' center @click="openDialog">
      <t-form ref="search" label-width="120px" :model="gridOptions.dataSource.serviceInstanceInputParameters">
        <el-row :gutter="10" class="search-top-operate">
          <el-button type="primary" @click="choose">
            确定
          </el-button>
          <el-button type="info" @click="cancel">
            取消
          </el-button>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item label="到帐方式：" prop="rWay" style="margin-bottom: 15px;">
              <t-dic-dropdown-select dicType="account_way" @change="doRefresh()" v-model="gridOptions.dataSource.serviceInstanceInputParameters.rWay"></t-dic-dropdown-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="到帐类型：" prop="rType" style="margin-bottom: 15px;">
              <t-dic-dropdown-select dicType="account_type" @change="doRefresh()" v-model="gridOptions.dataSource.serviceInstanceInputParameters.rType" disabled></t-dic-dropdown-select>
            </el-form-item>
          </el-col>
        </el-row>
      </t-form>
      <t-grid ref="searchResultList" :options="gridOptions" @selection-change="handleSelectionChange"></t-grid>
    </el-dialog>
  </div>
</template>

<script>
  import baseView from '@/base/baseView'
  import isEmpty from "lodash/isEmpty";
  export default {
    extends: baseView,
    props: {
      searchData: {
        type: Object,
        required: false
      }
    },
    data () {
      return {
        dataInfo: [],
        totalAmount: 0, // 小计
        dialogVisible: false,
        gridOptions: {
          mutiSelect: true,
          dataSource: {
            serviceInstance: tapp.services.finaTakebackApproval.getPagedList,
            serviceInstanceInputParameters: {
              rWay: '',
              rType: '', // 默认为预付款 yfk
            }
          },
          grid: {
            offsetHeight: 125, // 125:查询部分高度
            mutiSelect: true,
            maxHeight: 350,
            columns: [
              {
                prop: 'proName',
                label: '项目名称'
              },

              {
                prop: 'proCode',
                label: '项目编号',
                width: 170
              },
              {
                prop: 'bRealAmount',
                label: '可使用金额（万元）| 联营',
                width: 200,
                formatter: (row, column, cellValue) => {
                  return this.$util.moneyFormat(row.bRealAmount)
                }
              },
              {
                prop: 'rWay',
                label: '到帐方式',
                formatter: (row, column, cellValue) => {
                  return this.$util.dataDicFormat('account_way', row.rWay) // 第一个参数为字典类型值，复用替换字典类型值，第二个为当前cell值
                }
              },
              {
                prop: 'rType',
                label: '到帐类型',
                width: 100,
                formatter: (row, column, cellValue) => {
                  return this.$util.dataDicFormat('account_type', row.rType) // 第一个参数为字典类型值，复用替换字典类型值，第二个为当前cell值
                }
              },
              {
                prop: 'proBuildArea',
                label: '到账时间'
              },
              {
                prop: 'proRunMode',
                columnKey: 'proRunMode', // 保持与prop一致（可以去掉）
                label: '经营模式',
                width: 100,
                formatter: (row, column, cellValue) => {
                  return this.$util.dataDicFormat('business_type', row.proRunMode) // 第一个参数为字典类型值，复用替换字典类型值，第二个为当前cell值
                }
              },
              {
                prop: 'payment',
                label: '本次付款金额（万元）|联营',
                width: 180,
                render: (h, params) => {
                  return h('t-currency-input', {
                    props: {
                      value: params.row.payment,
                      unitValue: 10000
                    },
                    on: {
                      input: function (event) {
                        console.log('event', event)
                        params.row.payment = event
                      }
                    }
                  })
                }
              },
            ], // 需要展示的列
            defaultSort: {
              prop: 'id',
              order: 'descending'
            }
          }
        }
      }
    },
    components: {
    },
    created () {
      this.currentValue = this.value
    },
    watch: {
      searchData: {
        handler (newValue, oldValue) {
          this.gridOptions.dataSource.serviceInstanceInputParameters = newValue
          this.doRefresh()
        },
        deep: true
      }
    },
    activated () {
      this.$nextTick(() => {
      })
    },
    computed: {},
    methods: {
      handleSelectionChange (val) {
        this.selectedRows = val
        console.log('selectedData 选中的数据', val)
      },
      doRefresh () {
        console.log('doRefresh 查询条件', this.gridOptions.dataSource.serviceInstanceInputParameters)
        this.$refs.searchResultList.refresh()
      },
      // 打开承兑汇票弹框
      openDialog () {
        // 给弹出的框赋值查询条件
        this.dialogVisible = true
      },
      // 打开承兑汇票弹框
      closeDialog () {
        this.dialogVisible = false
      },
      doReset () {
        this.$refs.search.resetFields()
        this.selectedRows = []
        this.doRefresh()
      },
      // 点击确定
      choose () {
        if (this.validatorSelectedData()) {
          this.dialogVisible = false
          // 给承兑汇票的table赋值
          this.dataInfo = this.selectedRows
          this.setTotalAmount(this.dataInfo)
          // 传送到父组件
          this.$emit('selectedData', this.dataInfo)
          this.doReset()
        }
      },
      // 点击取消
      cancel () {
        this.dialogVisible = false
        this.doReset()
      },
      // 校验选中的数据是否填写了本次付款金额（万元）
      validatorSelectedData () {
        let isPass = true
        for (let v of this.selectedRows) {
          if (!v.payment) {
            isPass = false
            this.$notify.error({
              title: '错误',
              message: '选中的数据的【本次付款金额（万元）】不能为空'
            })
            break
          }
        }
        return isPass
      },
      // 计算小计
      setTotalAmount (data) {
        let sum = 0
        data.map(v => {
          if (v.payment) {
            sum += Number(v.payment)
          }
        })
        this.totalAmount = sum
      }
    }
  }
</script>
<style scoped type="text/css">
  .acceptance-bill-table-container {
    margin: 10px 0;
  }

  .fina-function-button {
    display: flex;
    align-items: center;
    margin: 0 0 10px 0;
  }
</style>

