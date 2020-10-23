<template>
  <div class="container">
    <!-- 面包屑导航区域 -->
    <el-breadcrumb>
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>权限列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图 -->
    <el-table :data="tableData" style="width: 100%" stripe border>
      <el-table-column type="index" width="50">
      </el-table-column>
      <el-table-column prop="authName" label="权限名称">
      </el-table-column>
      <el-table-column prop="path" label="路径">
      </el-table-column>
      <el-table-column label="权限等级">
        <template slot-scope="whc">
          <el-tag size="mini" v-if="whc.row.level==='0'">一级</el-tag>
          <el-tag size="mini" type="success" v-else-if="whc.row.level==='1'">二级</el-tag>
          <el-tag size="mini" type="danger" v-else-if="whc.row.level==='2'">三级</el-tag>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>
<script>
export default {
  components: {},
  props: {},
  data () {
    return {
      // 数据存储
      tableData: []
    }
  },
  computed: {},
  watch: {},
  created () {
    this.getFromData()
  },
  mounted () { },
  methods: {
    async getFromData () {
      const { data: res } = await this.$http.get('rights/list')
      console.log(res)
      this.tableData = res.data
    }
  }
}
</script>
<style lang="less" scoped>
.container {
  .el-breadcrumb {
    padding-bottom: 30px;
  }
}
</style>
