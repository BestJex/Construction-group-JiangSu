<template>
<t-form :model="model" ref="ruleForm">
  <el-form-item prop="age" verify>
    <el-input v-model="model.age"></el-input>
  </el-form-item>
  <el-form-item>
    <el-button  @click="doSave()">保存</el-button>
  </el-form-item>
</t-form>
</template>
<script>
export default {
  data() {
    return {
      model: {
        age: null
      }
    }
  },
  methods: {
    doSave(tab, event) { 
      let self = this;
      self.$refs['ruleForm'].validate((valid) => {
        alert(valid);
      });
    },
  }
}
</script>
