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
        <t-workflow-map businessKey="t_fina_key_receive_approval"></t-workflow-map>
        <div slot="footer">
          <el-button type="primary" @click="dialogVisible = false">确定</el-button>
        </div>
      </el-dialog>
    </el-row>
    <el-form :model="dataForm" :rules="dataRule" ref="ruleForm" @submit.native.prevent label-width="120px" label-position="right">
      <el-card shadow="never">
      <t-sub-title :title="'资金到账信息'"></t-sub-title>
      <el-row :gutter="20">
        <el-col :span="8">
          <el-form-item prop="proName" label="项目名称">
            <t-project-select placeholder="选择一个项目" v-model="dataForm.proName" @selectedProject="getSelectedProject"></t-project-select>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item prop="proRunMode" label="经营模式">
            <t-dic-dropdown-select dicType="business_type" disabled v-model="dataForm.proRunMode" @change="onProRunMode" ></t-dic-dropdown-select>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item prop="proSubCompany" label="所属单位">
            <el-input readonly v-model="dataForm.proSubCompany"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item prop="rAmount" label="到帐金额">
            <el-input v-model="dataForm.rAmount" @change="onRAmount">
              <span slot="append">万元</span>
            </el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item prop="rDatetime" label="到帐时间">
            <t-datetime-picker type="date" v-model="dataForm.rDatetime"></t-datetime-picker>
          </el-form-item>
        </el-col>
        <el-col :span="4">
          <el-form-item prop="rWay" label="到帐方式">
            <t-dic-dropdown-select dicType="account_way" v-model="dataForm.rWay" @change="onLNumFlag" :readOnly="false"></t-dic-dropdown-select>
          </el-form-item>
        </el-col>
        <el-col :span="4">
          <el-form-item prop="lNum" label="票号">
            <el-input :readonly="lNumflag" v-model="dataForm.lNum"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item prop="rType" label="到帐类型">
            <t-dic-dropdown-select dicType="account_type" v-model="dataForm.rType"
                                   :readOnly="readOnly"></t-dic-dropdown-select>
          </el-form-item>
        </el-col>
        <el-col :span="6">
          <el-form-item prop="sAmount" label="自营">
            <el-input v-model="dataForm.sAmount" @change="onAmount" :readonly="sAmount">
              <span slot="append">万元</span>
            </el-input>
          </el-form-item>
        </el-col>
        <el-col :span="6">
          <el-form-item prop="oAmount" label="联营">
            <el-input v-model="dataForm.oAmount" @change="onAmount" :readonly="oAmount">
              <span slot="append">万元</span>
            </el-input>
          </el-form-item>
        </el-col>
      </el-row>
      </el-card>
      <el-card shadow="never">
      <t-sub-title :title="'办理信息'"></t-sub-title>
      <el-row :gutter="20">
        <el-col :span="8">
          <el-form-item prop="sign" label="经办人">
            <span>{{dataForm.sign}}</span>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item prop="signTime" label="经办时间">
            <span>{{dataForm.signTime}}</span>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="24">
          <el-form-item prop="remark" label="备注">
            <el-input type="textarea" v-model="dataForm.remark" :readonly="readOnly"></el-input>
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
  import moment from "moment";
  import {mapState} from "vuex";

  export default {
    data () {
      return {
        assetCategoryClassifications: ['proma_demoform'], // 附件的分类标识 此处为示例
        docId: '',
        oAmount: true,
        sAmount: true,
        lNumflag: true,
        showButton: true,
        readOnly: false,
        dialogVisible: false,
        isBackFill: false,
        dataForm: {
          bId: '',actTaskKey: '',pId: '',proRunMode: '',unionCompany: '',rAmount: '',rDatetime: '',rWay: '',
          lNum: '',rType: '',sAmount: '',oAmount: '',approvalStatus: '',sign: '',signTime: '',propose: '',result: '',
          createtime: '',updatetime: '',createuser: '',updateuser: '',datastatus: '' ,proCode:''                                                                                       },
        dataRule: {
          proName: [
            { required: true, message: '项目名称不能为空', trigger: 'blur' }
          ],
          proRunMode: [
            { required: true, message: '经营模式不能为空', trigger: 'blur' }
          ],
          proSubCompany: [
            { required: true, message: '所属单位不能为空', trigger: 'blur' }
          ],
          rAmount: [
            { required: true, message: '到帐金额不能为空', trigger: 'blur' }
          ],
          rDatetime: [
            { required: true, message: '到帐时间不能为空', trigger: 'blur' }
          ],
          rWay: [
            { required: true, message: '到帐方式不能为空', trigger: 'blur' }
          ],
          lNum: [
            { required: true, message: '票号不能为空', trigger: 'blur' }
          ],
          rType: [
            { required: true, message: '到帐类型不能为空', trigger: 'blur' }
          ],
          sAmount: [
            { required: true, message: '自营不能为空', trigger: 'blur' }
          ],
          oAmount: [
            { required: true, message: '联营不能为空', trigger: 'blur' }
          ],
          sign: [
            { required: true, message: '执行人不能为空', trigger: 'blur' }
          ],
          signTime: [
            { required: true, message: '执行时间不能为空', trigger: 'blur' }
          ],

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
      onAmount(){
      },
      onRAmount(){
        let proRunMode = this.dataForm.proRunMode
        // 联营
        if (proRunMode == 'pool') {
          this.dataForm.oAmount = this.dataForm.rAmount
          this.dataRule.oAmount[0].required = true
          this.dataRule.sAmount[0].required = false
        }
        // 自营
        if (proRunMode == 'proprietary') {
          this.dataForm.sAmount = this.dataForm.rAmount
          this.dataRule.oAmount[0].required = false
          this.dataRule.sAmount[0].required = true
        }
        if (proRunMode == 'proprietary_pool') {
          this.dataForm.sAmount = ''
          this.dataForm.oAmount = ''
          this.dataRule.oAmount[0].required = true
          this.dataRule.sAmount[0].required = true
        }
      },
      onProRunMode(){
        let proRunMode = this.dataForm.proRunMode

        // 自营 + 联营
        if (proRunMode == 'proprietary_pool') {
          this.sAmount = false
          this.oAmount = false
        }
      },
      onLNumFlag(){
        if (this.dataForm.rWay == 'promise_draft') {
          this.lNumflag = false
          this.dataRule.lNum[0].required = true
        } else {
          this.lNumflag = true
          this.dataRule.lNum[0].required = false
        }
      },

      // 选择项目
      getSelectedProject(project) {
        // 项目 id 已从从组件里已经带出来，这里定义为 dataForm.projectId，可以自行修改为当前传到接口的变量名
        this.dataForm.proName = project.proName
        this.dataForm.pId = project.id
        this.dataForm.proSubCompany = project.proSubCompany
        this.dataForm.proRunMode = project.proRunMode
        this.dataForm.proCode = project.proCode
      },
      // 初始化 编辑和新增 2种情况
      init (id) {
        if(id) {
          this.dataForm.id = id || 0
          this.$nextTick(() => {
            this.$refs["ruleForm"].resetFields()
            if (this.dataForm.id) {
              let self = this;
              tapp.services.finaReceiveAccounapproval.get(id).then(function(result) {
                self.dataForm = self.$util.deepObjectAssign({}, self.dataForm, result)
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
          tapp.services.finaReceiveAccounapproval.save(model).then(function(result) {
            self.dataForm = self.$util.deepObjectAssign({}, self.dataForm, result)
            self.$notify.success({
              title: "操作成功！",
              message: "保存成功！",
            });
            self.$refs['ruleForm'].resetFields()
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
