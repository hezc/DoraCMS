<template>
  <el-tree :data="treeData" :props="defaultProps" node-key="id" default-expand-all :expand-on-click-node="false" :render-content="renderContent">
  </el-tree>
</template>

<script>
import services from '../../store/services.js';

export default {
  props: {
    treeData: Array
  },
  data() {
    return {
      defaultProps: {
        children: 'children',
        label: 'name'
      }
    }
  },

  methods: {
    append(store, data) {
      let formData = {};
      formData.parentId = data._id;
      formData.parentObj = data;
      this.$store.dispatch('showContentCategoryForm', {
        edit: false,
        type: 'children',
        formData: formData
      });
    },

    edit(store, data) {
      this.$store.dispatch('showContentCategoryForm', {
        edit: true,
        type: 'children',
        formData: data
      });
    },

    remove(store, data) {
      this.$confirm('此操作将永久删除该记录, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        return services.deleteContentCategory({
          ids: data._id
        });
      }).then((result) => {
        if (result.data.state === 'success') {
          this.$store.dispatch('getContentCategoryList');
          this.$message({
            message: '删除成功',
            type: 'success'
          });
        } else {
          this.$message.error(result.data.message);
        }
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },

    renderContent(h, { node, data, store }) {
      return (
        <span>
          <span>
            <span>{node.label}</span>
          </span>
          <span style="float: right; margin-right: 20px">
            <el-button size="mini" on-click={() => this.append(store, data)}>添加</el-button>
            <el-button size="mini" on-click={() => this.edit(store, data)}>编辑</el-button>
            <el-button size="mini" on-click={() => this.remove(store, data)}>删除</el-button>
          </span>
        </span>);
    }
  }
};
</script>