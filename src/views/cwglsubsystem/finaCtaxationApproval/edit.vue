<template>
  <div>
    <el-row v-if="showButton" :gutter="10" class="search-top-operate">
      <el-button type="primary" icon="el-icon-s-check" @click="doSave()">
        提交审批
      </el-button>
      <el-button type="primary" plain icon="el-icon-s-data" @click="dialogVisible = true">
        审批流程图
      </el-button>
      <el-dialog title="审批流程图" :visible.sync="dialogVisible" width="70%">
        <!-- businessKey值请修改当前流程的key值 -->
        <t-workflow-map businessKey="t_baseinfo_key_approval_process"></t-workflow-map>
        <div slot="footer">
          <el-button type="primary" @click="dialogVisible = false">确定</el-button>
        </div>
      </el-dialog>
    </el-row>
    <el-row :gutter="10" class="search-top-operate" v-if="isBackFill">
      <el-button type="primary" icon="el-icon-upload2" @click="doBackFillSave()">保存</el-button>
    </el-row>
    <el-form :model="dataForm" :rules="dataRule" ref="ruleForm" @submit.native.prevent label-width="140px" label-position="right">
      <el-card shadow="never">
      <t-sub-title :title="'申请信息'"></t-sub-title>
      <el-row :gutter="20">
        <el-col :span="12">
          <el-form-item prop="proName" label="项目名称">
            <t-bank-project-select v-model="dataForm.proName" :readonly="true" @selectedData="selectedData"></t-bank-project-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item prop="pId" label="工程起止时间">
            <t-datetime-range-picker readOnly type="datetime"  v-model="dataForm.proPlanDate"></t-datetime-range-picker>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="12">
          <el-form-item prop="cId" label="合同名称">
            <t-input readonly v-model="dataForm.conName"></t-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item prop="cId" label="合同金额">
            <t-input readonly v-model="dataForm.conTotal"></t-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="12">
          <el-form-item prop="cId" label="合同对方企业名称">
            <t-input readonly v-model="dataForm.cId"></t-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item prop="cId" label="所属公司">
            <t-input readonly v-model="dataForm.proSubCompany"></t-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="12">
          <el-form-item prop="taxMethod" label="计税方式">
            <t-dic-dropdown-select :disabled="readOnly" dicType="tax_method" v-model="dataForm.taxMethod"></t-dic-dropdown-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item prop="applyAmount" label="申请金额">
            <t-input v-model="dataForm.applyAmount"></t-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="12">
          <el-form-item prop="province" label="外出经营地">
            <t-region-picker :province.sync="dataForm.province" :city.sync="dataForm.city" :district.sync="dataForm.district"
                             :disabled="readOnly"></t-region-picker>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item prop="address" label="详细地址">
            <t-input v-model="dataForm.address"></t-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item prop="licenceCode" label="外经证号" :class="{'is-required': isBackFill}">
              <t-input placeholder="申请通过后填写" :disabled="!isBackFill" v-model="dataForm.licenceCode"></t-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="使用期限" :rules="dataRule.startDate" :class="{'is-required': isBackFill}">
            <t-datetime-range-picker :disabled="!isBackFill" v-model="timeRange" @change="onStartDateRangeChanged"></t-datetime-range-picker>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="12">
          <el-form-item prop="sign" label="经办人">
            <span>{{dataForm.sign}}</span>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item prop="signTime" label="经办时间">
            <span>{{dataForm.signTime}}</span>
          </el-form-item>
        </el-col>
      </el-row>
      </el-card>
      <el-card shadow="never">
      <t-sub-title :title="'附件上传'"></t-sub-title>
      <sj-upload ref="demo" :assetCategoryClassifications="assetCategoryClassifications" :businessDocId="docId"></sj-upload>
      </el-card>
    </el-form>
  </div>
</template>

<script>
  import moment from 'moment'
  import { mapState } from 'vuex'
  import find from "lodash/find";

  export default {
    data () {
      var checkLicenceCode = (rule, value, callback) => {
        if (this.isBackFill && (this.dataForm.licenceCode == '' || this.dataForm.licenceCode == undefined)) callback(new Error('外经证号不能为空'));
        else callback();
      };
      var checkStartDate = (rule, value, callback) => {
        if (this.isBackFill && (this.dataForm.startDate == '' || this.dataForm.startDate == undefined)) callback(new Error('使用期限不能为空'));
        else callback();
      };
      var checkEndDate = (rule, value, callback) => {
        if (this.isBackFill && (this.dataForm.endDate == '' || this.dataForm.endDate == undefined)) callback(new Error('使用期限不能为空'));
        else callback();
      };
      return {
        assetCategoryClassifications: ['proma_demoform'], // 附件的分类标识 此处为示例
        docId: '',
        showButton: true,
        readOnly: false,
        dialogVisible: false,
        isBackFill: false,
        timeRange: [],
        proPlanDateRange: [],
        dataForm: {
          bId: '',actTaskKey: '',pId: '',cId: '',taxMethod: '',applyAmount: '',province: '',city: '',district: '',
          address: '',licenceCode: '',startDate: '',endDate: '',approvalStatus: '',sign: '',signTime: '',proPlanDate:'',
          propose: '',result: '',createtime: '',updatetime: '',createuser: '',updateuser: '',datastatus: '',proName: ''                                                                                       },
        dataRule: {
          pId: [
            { required: true, message: '项目名称不能为空', trigger: 'blur' }
          ],
          cId: [
            { required: true, message: '合同名称不能为空', trigger: 'blur' }
          ],
          taxMethod: [
            { required: true, message: '计税方式不能为空', trigger: 'blur' }
          ],
          applyAmount: [
            { required: true, message: '申请金额不能为空', trigger: 'blur' }
          ],
          address: [
            { required: true, message: '详细地址不能为空', trigger: 'blur' }
          ],
          sign: [
            { required: true, message: '执行人不能为空', trigger: 'blur' }
          ],
          signTime: [
            { required: true, message: '执行时间不能为空', trigger: 'blur' }
          ],
          licenceCode: [{validator: checkLicenceCode, trigger: 'blur'}],
          startDate: [{validator: checkStartDate, trigger: 'blur'}],
          endDate: [{validator: checkEndDate, trigger: 'blur'}],
        }
      }
    },
    created() {
      const currentQuery = this.$route.query
      this.readOnly = (currentQuery.readonly == 'true') || this.readOnly
      this.showButton = !(currentQuery.readonly == 'true')
      this.isBackFill = currentQuery.status && (currentQuery.status == 1 || currentQuery.status == 2) ? true : false
      this.init(currentQuery.businessId)
    },
    activated() {
      const currentQuery = this.$route.query
      this.readOnly = (currentQuery.readonly == 'true') || this.readOnly
      this.showButton = !(currentQuery.readonly == 'true')
      this.isBackFill = currentQuery.status && (currentQuery.status == 1 || currentQuery.status == 2) ? true : false
      this.init(currentQuery.businessId)
    },
    computed: {
      ...mapState({
        currentUser: state => state.app.user,  })
    },
    methods: {
      // 选择项目
      selectedData(data) {
        // 项目 id 已从从组件里已经带出来，这里定义为 dataForm.projectId，可以自行修改为当前传到接口的变量名
        console.log(data)
        this.dataForm.pId = data.pId
        this.dataForm.cId = data.cId
        this.dataForm.proName = data.proName
        this.dataForm.conName = data.conName
        this.dataForm.conTotal = data.conTotal
        this.dataForm.proSubCompany = data.proSubCompany
        this.dataForm.proPlanDate = [data.proPlanStartDate + ' 00:00:00', data.proPlanEndDate + ' 00:00:00'];
      },
      // 初始化 编辑和新增 2种情况
      init (id) {
        if(id) {
          this.dataForm.id = id || 0
          this.$nextTick(() => {
            this.$refs["ruleForm"].resetFields()
            if (this.dataForm.id) {
              let self = this;
              tapp.services.finaCtaxationApproval.get(id).then(function(result) {
                self.dataForm = self.$util.deepObjectAssign({}, self.dataForm, result)
                self.dataForm.proPlanDate = [self.dataForm.proPlanStartDate + ' 00:00:00', self.dataForm.proPlanEndDate + ' 00:00:00']
                let params = {
                  filters: {},
                  maxResultCount: 20,
                  skipCount: 1,
                  sorting: "id descending",
                  id: result.pId
                }
                tapp.services.finaFwaccounapproval.getFinaBankList(params).then(_result => {
                  if(_result && _result.items && _result.items.length > 0) {
                    let item = find(_result.items, {id: result.pId})
                    self.dataForm = self.$util.deepObjectAssign({}, self.dataForm, item)
                  }
                })
              })
            }
          })
        } else {
          this.$nextTick(() => {
            this.$refs.ruleForm.clearValidate()
            this.dataForm.sign = this.currentUser.userDisplayName
            this.dataForm.signTime = this.$util.datetimeFormat(moment())
          })
        }
      },
      // 表单提交
      doSave () {
        let self = this;
        let validPromises = [self.$refs['ruleForm'].validate()];
        Promise.all(validPromises).then(resultList => {
          let model = { ...self.dataForm };
          tapp.services.finaCtaxationApproval.save(model).then(function(result) {
            self.dataForm = self.$util.deepObjectAssign({}, self.dataForm, result)
            self.$notify.success({
              title: "操作成功！",
              message: "保存成功！",
            });
          });
        }).catch(function(e) {
          self.$notify.error({
            title: "错误",
            message: "保存失败！"
          });
          return false;
        });
      },
      onStartDateRangeChanged(val) {
        this.startDate = val[0];
        this.endDate = val[1];
      },
      // 保存回填
      doBackFillSave() {
        let self = this;
        let validPromises = [self.$refs['ruleForm'].validate()];
        Promise.all(validPromises).then(resultList => {
          let model = { ...self.dataForm };
          tapp.services.finaCtaxationApproval.save(model).then(function(result) {
            self.$notify.success({
              title: "操作成功！",
              message: "保存成功！",
            });
          });
        }).catch(function(e) {
          self.$notify.error({
            title: "错误",
            message: "保存失败！"
          });
          return false;
        });
      }
    }
  }
</script>
