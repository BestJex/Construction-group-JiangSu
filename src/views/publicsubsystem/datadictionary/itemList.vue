<template>
<div class="mod-role">
  <el-card class="box-card">
    <div slot="header" class="clearfix">
      <span>{{categoryName}}-数据字典项</span>
      <div style="float: right; padding: 3px 0">
        <el-button  icon="el-icon-plus" type="primary" @click="doNewRoot()">添加根节点</el-button>
        <el-button  icon="el-icon-delete" type="danger" @click="doBatchDelete()">批量删除</el-button>
      </div>
    </div>
    <div class="text item">
      <t-tree ref="tree" :options="treeOptons" @node-drop="handleDrop">
      </t-tree>
    </div>
  </el-card>
  <edit-form v-if="editFormVisible" ref="editForm" @change="doRefresh"></edit-form>
</div>
</template>
<script>
import EditForm from './edit'
export default {
  props: {
    categoryId: {
      type: String,
      required: true
    },
    categoryName: {
      type: String,
      required: true
    },
  },
  data() {
    return {
      editFormVisible: false,
      treeOptons: {
        dataSource: {
          serviceInstance: tapp.services.base_DataDictionary.getDictionaryTreeList,
          serviceInstanceInputParameters: {
            categoryId:null,
          },
          autoLoadData:false,
        },
        tree: {
          checkStrictly: true,
          showCheckbox: true,
          draggable: true,
          defaultCheckedKeys: [],
          renderContent: this.renderTreeNodeContent,
        }
      },

    }
  },
  components: {
    EditForm,
  },
  created() {
    this.load(this.categoryId);
  },
  computed: {},
    watch: {
    categoryId(value) { 
      this.load(value);
    },
  },
  methods: {
    renderTreeNodeContent(h, {
      node,
      data,
      store
    }) {
      return (
            <span class="custom-tree-node">
              <span>{node.label}</span>
              <span>
                <el-button  size="mini"  type="text" on-click={ () => this.doEdit(node, data) }>编辑</el-button>
                <el-button  size="mini"  type="text" on-click={ () => this.doNewChild(node, data) }>增加子项</el-button>
              </span>
            </span>);
        },
        load(categoryId){
          this.treeOptons.dataSource.serviceInstanceInputParameters.categoryId = categoryId;
          this.doRefresh();
        },
        doEdit(node, data) {
          this.editFormVisible = true
          let parentNode = node.parent;
          this.$nextTick(() => {
            this.$refs.editForm.init(data.parentId,parentNode && parentNode.label,this.categoryId,this.categoryName,data.id);
          })
        },
        doNewRoot() {
          this.editFormVisible = true
          this.$nextTick(() => {
            this.$refs.editForm.init(null, null,this.categoryId,this.categoryName,null);
          })
        },
        doNewChild(node,data) {
          this.editFormVisible = true
          let parentNode = node.parent;
          this.$nextTick(() => {
            this.$refs.editForm.init(data.id, data.name, this.categoryId,this.categoryName,null);
          })
        },
        handleDrop(draggingNode, dropNode, dropType, ev) {
          let self = this;
          tapp.services.base_DataDictionary.updateDictionaryParentIdAndSortIndex({
            'id': draggingNode.data.id,
            'newParentId': dropType == 'inner' ? dropNode.data.id : dropNode.data.parentId,
            'newSortIndex': dropType == 'inner' ? 0 : (dropNode.data.self.sortIndex || 0) + (dropType == 'after' ? 1 : -1),
          }).then(function(result) {
            self.$notify.success({
              title: '数据字典项变更父节点或顺序成功',
              message: '系统提示'
            });

          })
        },
        doBatchDelete() {
          let self = this;
          let ids = self.$refs.tree.getCheckedKeys();
          if (!ids || ids.length == 0) {
            self.$notify.info({
              title: '系统提示',
              message: '您没选择任何行，无法操作！'
            });
            return;
          }
          self.$confirm('此操作将永久删除' + ids.length + '个数据字典项, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            tapp.services.base_DataDictionary.batchDelete(ids).then(function(result) {
              self.$notify.success({
                title: '系统删除成功',
                message: '数据字典项已删除成功！'
              });
              self.doRefresh();
            })
          });
        },
        doRefresh() {
          if(this.treeOptons.dataSource.serviceInstanceInputParameters.categoryId){
            this.$nextTick(()=>{
              this.$refs.tree.refresh();
            })
          }
        }
    }
  }
</script>
