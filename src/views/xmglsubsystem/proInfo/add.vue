<template>
  <div>
    <el-row :gutter="20" class="page-title">
      <el-col>
        <div class="title">项目信息登记</div>
      </el-col>
    </el-row>
    <el-row :gutter="10" class="search-top-operate">
      <el-button class="demo-button" type="primary" icon="el-icon-upload2" @click="doSave()">保存</el-button>
    </el-row>
    <el-form :model="dataForm" :rules="dataRule" ref="ruleForm" @submit.native.prevent
             label-width="150px" label-position="right">
      <el-card shadow="never">
        <t-sub-title :title="'基本信息'"></t-sub-title>
        <el-row :gutter="20">
          <el-col :span="8" v-if="haveOrNot === 'not'">
            <el-form-item prop="proName" label="项目名称：" verify class="is-required">
              <el-input v-model="dataForm.proName"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8" v-if="haveOrNot === 'have'">
            <el-form-item label="项目名称：" prop="pcId">
              <t-record-select placeholder="选择一个备案项目" v-model="dataForm.pcId" @selectedRecord="getSelectedRecord">
                <el-button v-if="haveOrNot === 'have'" slot="append" icon="el-icon-search" @click="queryDialogVisible=true"></el-button>
              </t-record-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="proCode" label="投标流程：">
              <t-dic-radio-select dicType="have_or_not" v-model="haveOrNot" :readOnly="readOnly"></t-dic-radio-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="proCode" label="项目编号：">
              <el-input v-model="dataForm.proCode" readonly placeholder="保存后系统自动生成"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="16">
            <el-form-item prop="proConstructCompany" label="建设单位：">
              <el-input v-model="dataForm.proConstructCompany"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="proConstructCompanyAttr" label="单位性质：">
              <t-dic-dropdown-select dicType="unit_nature" v-model="dataForm.proConstructCompanyAttr"
                                     :readOnly="readOnly"></t-dic-dropdown-select>
            </el-form-item>
          </el-col>
          <el-col :span="16">
            <el-form-item label="项目地址：" prop="pro_address">
              <el-row type="flex" justify="space-between">
                <el-col :span="10">
                  <t-region-s-picker :province.sync="dataForm.proAddressProvince" :city.sync="dataForm.proAddressCity"
                                   :readOnly="readOnly"></t-region-s-picker>
                </el-col>
                <el-col :span="13">
                  <el-form-item prop="proAddressDetail">
                    <el-input v-model="dataForm.proAddressDetail"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="proTotalInvestment" label="项目总投资：">
              <t-currency-input v-model="dataForm.proTotalInvestment"></t-currency-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="proSubCompanyName" label="所属分公司：">
              <el-input v-model="dataForm.proSubCompanyName"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="proBusDeptName" label="所属事业部：">
              <el-input v-model="dataForm.proBusDeptName"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="proDriveSubject" label="实施主体：">
              <t-dic-dropdown-select dicType="ss_zt" v-model="dataForm.proDriveSubject"
                                     :readOnly="readOnly"></t-dic-dropdown-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="proContractAttr" label="承包形式：">
              <t-dic-dropdown-select dicType="contract_model" v-model="dataForm.proContractAttr"
                                     :readOnly="readOnly"></t-dic-dropdown-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="proType" label="工程类别：">
              <t-dic-dropdown-select dicType="engineering_type" v-model="dataForm.proType"
                                     :readOnly="readOnly"></t-dic-dropdown-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="proSubType" label="类别子项：">
              <t-dic-dropdown-select dicType="category_child" v-model="dataForm.proSubType"
                                     :readOnly="readOnly"></t-dic-dropdown-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="proFundSource" label="资金来源：">
              <t-dic-dropdown-select dicType="money_source" v-model="dataForm.proFundSource"
                                     :readOnly="readOnly"></t-dic-dropdown-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="proStructure" label="结构形式：">
              <t-dic-dropdown-select dicType="structure_type" v-model="dataForm.proStructure"
                                     :readOnly="readOnly"></t-dic-dropdown-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="proContractScope" label="承包范围：">
              <el-input v-model="dataForm.proContractScope"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="name" label="项目经理：">
              <t-manager-select placeholder="选择一个项目经理" v-model="dataForm.name" @selectedManager="getSelectedManager"></t-manager-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="proWinAmount" label="中标金额：">
              <t-currency-input v-model="dataForm.proWinAmount" @change="moneyCapital"></t-currency-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="proWinAmountC" label="金额大写：">
              <el-input v-model="dataForm.proWinAmountC"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="计划开工日期：" prop="proPlanStartDate" verify class="is-required">
              <t-datetime-picker v-model="dataForm.proPlanStartDate" type="date" :readOnly="readOnly">
              </t-datetime-picker>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="计划完工日期：" prop="proPlanEndDate" verify class="is-required">
              <t-datetime-picker v-model="dataForm.proPlanEndDate" type="date" :readOnly="readOnly">
              </t-datetime-picker>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="proLimitTime" label="工程工期：">
              <t-int-input v-model="dataForm.proLimitTime"></t-int-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="实际开工日期：" prop="proRealStartDate" verify class="is-required">
              <t-datetime-picker v-model="dataForm.proRealStartDate" type="date" :readOnly="readOnly">
              </t-datetime-picker>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="实际完工日期：" prop="proRealEndDate" verify class="is-required">
              <t-datetime-picker v-model="dataForm.proRealEndDate" type="date" :readOnly="readOnly">
              </t-datetime-picker>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>
      <el-card shadow="never">
        <t-sub-title :title="'建筑情况'"></t-sub-title>
        <el-row :gutter="20">
          <el-col :span="6">
            <el-form-item label="建筑面积：" prop="proBuildArea" verify class="is-required">
              <t-int-input v-model="dataForm.proBuildArea" :readOnly="readOnly">
                <span slot="append">平方米</span>
              </t-int-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="高度/最大跨度：" prop="proSpan" verify class="is-required">
              <t-int-input v-model="dataForm.proSpan" :readOnly="readOnly">
                <span slot="append">米</span>
              </t-int-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="层数：" prop="proLayer" verify class="is-required">
              <t-int-input v-model="dataForm.proLayer" :readOnly="readOnly">
                <span slot="append">层</span>
              </t-int-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="栋数：" prop="proBlock" verify class="is-required">
              <t-int-input v-model="dataForm.proBlock" :readOnly="readOnly">
                <span slot="append">栋</span>
              </t-int-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="地下室：" prop="proBasementArea" verify class="is-required">
              <t-int-input v-model="dataForm.proBasementArea" :readOnly="readOnly">
                <span slot="append">平方米、层</span>
              </t-int-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item prop="proIsFitout" label="是否为装配式：">
              <t-dic-dropdown-select dicType="y_or_n" v-model="dataForm.proIsFitout"
                                     :readOnly="readOnly"></t-dic-dropdown-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="装配率：" prop="proFitoutRate" verify class="is-required">
              <t-int-input v-model="dataForm.proFitoutRate" :readOnly="readOnly">
                <span slot="append">%</span>
              </t-int-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item prop="proIsBim" label="是否应用BIM技术：">
              <t-dic-dropdown-select dicType="y_or_n" v-model="dataForm.proIsBim"
                                     :readOnly="readOnly"></t-dic-dropdown-select>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>
      <el-card shadow="never">
        <t-sub-title :title="'经营方式'"></t-sub-title>
        <el-row :gutter="20">
          <el-col :span="5">
            <el-form-item label="经营方式：" prop="proRunMode">
              <t-dic-dropdown-select dicType="business_type" v-model="dataForm.proRunMode"
                                     :readOnly="readOnly"></t-dic-dropdown-select>
            </el-form-item>
          </el-col>
          <el-col :span="5" v-if="dataForm.proRunMode === 'pool' || dataForm.proRunMode === 'proprietary_pool'">
            <el-form-item label="联营单位管理费：" prop="proUnionCompanyMerate" verify class="is-required">
              <t-int-input v-model="dataForm.proUnionCompanyMerate" :readOnly="readOnly">
                <span slot="append">%</span>
              </t-int-input>
            </el-form-item>
          </el-col>
          <el-col :span="6" v-if="dataForm.proRunMode === 'proprietary' || dataForm.proRunMode === 'proprietary_pool'" >
            <el-form-item label="项目净利润承诺超：" prop="proProfitRate" verify class="is-required">
              <t-int-input v-model="dataForm.proProfitRate" :readOnly="readOnly">
                <span slot="append">%</span>
              </t-int-input>
            </el-form-item>
          </el-col>
          <el-col :span="8" v-if="dataForm.proRunMode === 'proprietary' || dataForm.proRunMode === 'proprietary_pool'">
            <el-form-item label="公司负责人：" prop="proCompanyHeader">
              <el-input v-model="dataForm.proCompanyHeader">
                <el-button slot="append" icon="el-icon-search" @click="queryDialogVisible=true"></el-button>
              </el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <div v-if="dataForm.proRunMode === 'pool'">
          <el-row>
            <el-col :span="8">
              <el-form-item label="联营公司名称：" prop="proUnionCompany">
                <el-input v-model="dataForm.proUnionCompany">
                  <el-button slot="append" icon="el-icon-search" @click="queryDialogVisible=true"></el-button>
                </el-input>
              </el-form-item>
            </el-col>
            <el-col :span="8">
              <el-form-item prop="proContacter" label="联系人：">
                <el-input v-model="dataForm.proContacter"></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="8">
              <el-form-item prop="proContactway" label="联系方式：">
                <el-input v-model="dataForm.proContactway"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
        </div>
        <div v-if="dataForm.proRunMode === 'proprietary_pool'">
          <el-row :gutter="20">
            <el-col :span="8">
              <el-form-item label="联营公司名称：" prop="proUnionCompany">
                <el-input v-model="dataForm.proUnionCompany" readonly>
                  <el-button slot="append" icon="el-icon-search" @click="queryDialogVisible=true"></el-button>
                </el-input>
              </el-form-item>
            </el-col>
            <el-col :span="8">
              <el-form-item prop="proContacter" label="联系人：">
                <el-input v-model="dataForm.proContacter"></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="8">
              <el-form-item prop="proContactway" label="联系方式：">
                <el-input v-model="dataForm.proContactway"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
        </div>
      </el-card>
      <el-card shadow="never">
        <t-sub-title :title="'办理信息'"></t-sub-title>
        <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item prop="proRegister" label="登记人：">
              <span>{{dataForm.proRegister}}</span>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="登记时间：" prop="proRegisterTime" verify class="is-required">
              <span>{{dataForm.proRegisterTime}}</span>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>
      <el-card shadow="never">
        <t-sub-title :title="'附件上传'"></t-sub-title>
        <sj-upload ref="demo" :assetCategoryClassifications="assetCategoryClassifications"
                   :businessDocId="docId"></sj-upload>
      </el-card>
    </el-form>
  </div>
</template>

<script>
  import moment from 'moment'
  import {mapState} from 'vuex'

  export default {
    props: {
      readOnly: {
        type: Boolean,
        default: false,
        required: false
      }
    },
    data () {
      return {
        assetCategoryClassifications: ['proma_demoform'], // 附件的分类标识 此处为示例
        docId: '',
        // 是否有投标流程
        haveOrNot: 'have',
        dataForm: {
          pcId: '',
          proCode: '',
          proName: '',
          proConstructCompany: '',
          proConstructCompanyAttr: '',
          proAddressProvince: '',
          proAddressCity: '',
          proAddressDetail: '',
          proTotalInvestment: '',
          proSubCompany: '',
          proSubCompanyName: '',
          proBusDept: '',
          proBusDeptName: '',
          proDriveSubject: '',
          proContractAttr: '',
          proType: '',
          proSubType: '',
          proFundSource: '',
          proStructure: '',
          proContractScope: '',
          proManager: '',
          proWinAmount: '',
          proWinAmountC: '',
          proPlanStartDate: '',
          proPlanEndDate: '',
          proRealStartDate: '',
          proRealEndDate: '',
          proLimitTime: '',
          proBuildArea: '',
          proSpan: '',
          proLayer: '',
          proBlock: '',
          proBasementArea: '',
          proIsFitout: '',
          proFitoutRate: '',
          proIsBim: '',
          proRunMode: 'proprietary', // 默认自营
          proProfitRate: '',
          proUnionCompanyMerate: '',
          proUnionCompany: '',
          proContacter: '',
          proCompanyHeader: '',
          proContactway: '',
          proRegister: '',
          proRegisterTime: '',
          proStatue: '',
          createtime: '',
          updatetime: '',
          createuser: '',
          updateuser: '',
          datastatus: '',
          bidProcessList: null,
          name: '',
        },
        dataRule: {
          pcId: [
            {required: true, message: '项目名称不能为空', trigger: 'blur'}
          ],
          proConstructCompany: [
            {required: true, message: '建设单位不能为空', trigger: 'blur'}
          ],
          proConstructCompanyAttr: [
            {required: true, message: '单位性质不能为空', trigger: 'blur'}
          ],
          proAddressDetail: [
            {required: true, message: '详细地址不能为空', trigger: 'blur'}
          ],
          proTotalInvestment: [
            {required: true, message: '项目总投资不能为空', trigger: 'blur'}
          ],
          proSubCompanyName: [
            {required: true, message: '所属分公司不能为空', trigger: 'blur'}
          ],
          proDriveSubject: [
            {required: true, message: '实施主体不能为空', trigger: 'blur'}
          ],
          proContractAttr: [
            {required: true, message: '承包形式不能为空', trigger: 'blur'}
          ],
          proType: [
            {required: true, message: '工程类别不能为空', trigger: 'blur'}
          ],
          proSubType: [
            {required: true, message: '类别子项不能为空', trigger: 'blur'}
          ],
          proFundSource: [
            {required: true, message: '资金来源不能为空', trigger: 'blur'}
          ],
          proStructure: [
            {required: true, message: '结构形式不能为空', trigger: 'blur'}
          ],
          proContractScope: [
            {required: true, message: '承包范围不能为空', trigger: 'blur'}
          ],
          proWinAmount: [
            {required: true, message: '中标金额-元不能为空', trigger: 'blur'}
          ],
          proWinAmountC: [
            {required: true, message: '金额大写不能为空', trigger: 'blur'}
          ],
          proPlanStartDate: [
            {required: true, message: '计划开工日期不能为空', trigger: 'blur'}
          ],
          proPlanEndDate: [
            {required: true, message: '计划完工日期不能为空', trigger: 'blur'}
          ],
          proRealStartDate: [
            {required: true, message: '实际开工日期不能为空', trigger: 'blur'}
          ],
          proRealEndDate: [
            {required: true, message: '实际完工日期不能为空', trigger: 'blur'}
          ],
          proLimitTime: [
            {required: true, message: '工程工期不能为空', trigger: 'blur'}
          ],
          proBuildArea: [
            {required: true, message: '建筑面积不能为空', trigger: 'blur'}
          ],
          proSpan: [
            {required: true, message: '高度/最大跨度不能为空', trigger: 'blur'}
          ],
          proLayer: [
            {required: true, message: '层数不能为空', trigger: 'blur'}
          ],
          proBlock: [
            {required: true, message: '栋数不能为空', trigger: 'blur'}
          ],
          proBasementArea: [
            {required: true, message: '地下室能为空', trigger: 'blur'}
          ],
          proIsFitout: [
            {required: true, message: '是否为装配式不能为空', trigger: 'blur'}
          ],
          proFitoutRate: [
            {required: true, message: '装配率不能为空', trigger: 'blur'}
          ],
          proIsBim: [
            {required: true, message: '是否应用BIM技术不能为空', trigger: 'blur'}
          ],
          proRunMode: [
            {required: true, message: '经营方式不能为空', trigger: 'blur'}
          ],
          proProfitRate: [
            {required: true, message: '项目净利润承诺超不能为空', trigger: 'blur'}
          ],
          proUnionCompanyMerate: [
            {required: true, message: '联营单位管理费不能为空', trigger: 'blur'}
          ],
          proUnionCompany: [
            {required: true, message: '联营公司id不能为空', trigger: 'blur'}
          ],
          proContacter: [
            {required: true, message: '联系人不能为空', trigger: 'blur'}
          ],
          proCompanyHeader: [
            {required: true, message: '负责人不能为空', trigger: 'blur'}
          ],
          proContactway: [
            {required: true, message: '联系方式不能为空', trigger: 'blur'}
          ]
        }
      }
    },
    created () {
      this.init()
    },
    computed: {
      ...mapState({
        currentUser: state => state.app.user,
      })
    },
    methods: {
      // 初始化 编辑和新增 2种情况
      init: function (id) {
        if (id) {
          this.dataForm.id = id || 0;
          this.$nextTick(() => {
            this.$refs['ruleForm'].resetFields();
            if (this.dataForm.id) {
              let self = this;
              tapp.services.proInfo.get(id).then(function (result) {
                self.dataForm = self.$util.deepObjectAssign({}, self.dataForm, result)
                tapp.services.base_User.getRoleCategoryUsers().then(manager => {
                  if(manager && manager.items && manager.items.length > 0) {
                    let item = find(manager.items, {id: result.proManager})
                    if(item) self.dataForm.name = item.name
                  }
                })
              })
            }
          })
        } else {
          this.$nextTick(() => {
            this.$refs.ruleForm.clearValidate();
            this.dataForm.proRegister = this.currentUser.userDisplayName;
            this.dataForm.proRegisterTime = this.$util.datetimeFormat(moment());
            this.dataForm.proBusDeptName = this.currentUser.userDepartmentlist[0].value;
            this.dataForm.proBusDept = this.currentUser.userDepartmentlist[0].key;
            this.dataForm.proSubCompanyName = this.currentUser.userGrouplist[0].value;
            this.dataForm.proSubCompany = this.currentUser.userGrouplist[0].key;
          })
        }
      },
      getSelectedRecord(record) {
        console.log(record);
        this.dataForm.proName = record.proName;
        this.dataForm.proConstructCompanyAttr = record.proConstructCompanyAttr;
        this.dataForm.proSubCompany = record.proSubCompany;
        this.dataForm.proCode = record.proCode;
        this.dataForm.proAddressProvince = record.proAddressProvince;
        this.dataForm.proAddressCity = record.proAddressCity;
        this.dataForm.proAddressDetail = record.proAddressDetail;
        this.dataForm.proManager = record.proManager;
        this.dataForm.proFundSource = record.proFundSource;
        this.dataForm.proBusDept = record.proBusDept;
        this.dataForm.proSubType = record.proSubType;
        this.dataForm.proConstructCompany = record.proConstructCompany;
        this.dataForm.proContractAttr = record.proContractAttr;
        this.dataForm.proTotalInvestment = record.proTotalInvestment;
        this.dataForm.proType = record.proType;
        this.dataForm.proRunMode = record.proRunMode;
        this.dataForm.proBuildArea = record.proBuildArea;
        this.dataForm.proRealStartDate = record.proRealStartDate;
        this.dataForm.proPlanEndDate = record.proPlanEndDate;
        this.dataForm.proUnionCompanyMerate = record.proUnionCompanyMerate;
        this.dataForm.proProfitRate = record.proProfitRate;
        this.dataForm.proContacter = record.proContacter;
        this.dataForm.proContactway = record.proContactway;
        this.dataForm.proSpan = record.proSpan;
        this.dataForm.proLayer = record.proLayer;
        this.dataForm.proBlock = record.proBlock;
        this.dataForm.proBasementArea = record.proBasementArea;
        this.dataForm.proIsFitout = record.proIsFitout;
        this.dataForm.proFitoutRate = record.proFitoutRate;
        this.dataForm.proUnionCompany = record.proUnionCompany;
        this.dataForm.proIsBim = record.proIsBim;
        tapp.services.base_User.getRoleCategoryUsers().then(manager => {
          if(manager && manager.items && manager.items.length > 0) {
            let item = find(manager.items, {id: record.proManager})
            if(item) this.dataForm.name = item.name
          }
        })
      },
      getSelectedManager(manager) {
        console.log('current manager', manager)
      },
      moneyCapital(value) {
        if (value) {
          this.dataForm.proWinAmountC = this.$util.moneyArabiaToChinese(value);
        }
      },
      // 表单提交
      doSave () {
        let self = this
        let validPromises = [self.$refs['ruleForm'].validate()]
        Promise.all(validPromises).then(resultList => {
          let model = {...self.dataForm}
          tapp.services.proInfo.save(model).then(function (result) {
            self.dataForm = self.$util.deepObjectAssign({}, self.dataForm, result)
            self.$notify.success({
              title: '操作成功！',
              message: '保存成功！',
            })
          })
        }).catch(function (e) {
          self.$notify.error({
            title: '错误',
            message: '保存失败！'
          })
          return false
        })
      },
    }
  }
</script>
