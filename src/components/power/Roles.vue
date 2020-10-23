<template>
  <div class="container">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>角色列表</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card>
      <!-- 添加表单 -->
      <el-button size="mini" type="primary" @click="adddialogVisible = true">添加角色</el-button>
      <!-- 展示列表 -->
      <el-table :data="rolesList" border stripe>
        <el-table-column type="expand">
          <template v-slot="whc">
            <el-row :class="['bdbottom',i1===0?'bdtop':'' ,'vcenter']" v-for="(item1,i1) in whc.row.children"
              :key="item1.id">
              <!-- 渲染一级权限 -->
              <el-col :span="5">
                <el-tag closable @close="removeRightById(whc.row, item1.id)">{{item1.authName}}</el-tag>
                <i class=" el-icon-caret-right"></i>
              </el-col>
              <el-col :span="19">
                <!-- 通过 for 循环 嵌套渲染二级权限 -->
                <el-row :class="[i2 === 0 ? '' : 'bdtop', 'vcenter']" v-for="(item2, i2) in item1.children"
                  :key="item2.id">
                  <el-col :span="6">
                    <el-tag type="success" closable @close="removeRightById(whc.row, item2.id)">{{ item2.authName }}
                    </el-tag>
                    <i class="el-icon-caret-right"></i>
                  </el-col>
                  <el-col :span="18">
                    <el-tag type="warning" v-for="item3 in item2.children" :key="item3.id" closable
                      @close="removeRightById(whc.row, item3.id)">{{ item3.authName }}</el-tag>
                  </el-col>
                </el-row>
              </el-col>
            </el-row>
          </template>
        </el-table-column>
        <el-table-column type="index">
        </el-table-column>
        <el-table-column prop="roleName" label="角色名称">
        </el-table-column>
        <el-table-column prop="roleDesc" label="角色描述">
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="whc">
            <el-button type="primary" size="mini" icon="el-icon-edit" @click="onEdit(whc.row.id)">编辑</el-button>
            <el-button type="danger" size="mini" icon="el-icon-delete" @click="deleteData(whc.row.id)">删除</el-button>
            <el-button type="warning" size="mini" icon="el-icon-s-tools" @click="showSetRightDialog(whc.row)">分配权限
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 添加角色 -->
      <el-dialog title="添加角色" :visible.sync="adddialogVisible" width="50%" @close="addDialogClosed">
        <!-- 表单验证 -->
        <el-form :model="rolesForm" :rules="rolesFormRules" ref="rolesFormRef" label-width="100px">
          <el-form-item label="角色名称" prop="roleName">
            <el-input v-model="rolesForm.roleName"></el-input>
          </el-form-item>
          <el-form-item label="角色描述" prop="roleDesc">
            <el-input v-model="rolesForm.roleDesc"></el-input>
          </el-form-item>
        </el-form>
        <!-- /表单验证 -->
        <span slot="footer" class="dialog-footer">
          <el-button @click="adddialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="submitFrom">确 定</el-button>
        </span>
      </el-dialog>
      <!-- /添加角色 -->
      <!-- ------------------------------------------------------------------------------------------- -->
      <!-- 编辑功能 -->
      <el-dialog title="修改角色" :visible.sync="editdialogVisible" width="50%">
        <!-- 表单验证 -->
        <el-form :model="editRolesForm" :rules="edmitRolesFormRules" ref="deleteRolesFormRef" label-width="100px">
          <el-form-item label="角色名称" prop="roleName">
            <el-input v-model="editRolesForm.roleName"></el-input>
          </el-form-item>
          <el-form-item label="角色描述" prop="roleDesc">
            <el-input v-model="editRolesForm.roleDesc"></el-input>
          </el-form-item>
        </el-form>
        <!-- /表单验证 -->
        <span slot="footer" class="dialog-footer">
          <el-button @click="editdialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="editSubnit">确 定</el-button>
        </span>
      </el-dialog>
      <!-- /编辑功能 -->
      <!-- ------------------------------------------------------------------------------------------- -->
      <!--分配权限  -->
      <el-dialog title="分配权限" :visible.sync="setRightDialogVisible" width="50%" @close="setRightDialogClosed">
        <!-- 树形控件 -->
        <el-tree :data="rightslist" :props="treeProps" show-checkbox node-key="id" default-expand-all
          :default-checked-keys="defKeys" ref="treeRef"></el-tree>
        <span slot="footer" class="dialog-footer">
          <el-button @click="setRightDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="allotRights">确 定</el-button>
        </span>
      </el-dialog>
      <!--分配权限  -->
    </el-card>
  </div>
</template>
<script>
export default {
  components: {},
  props: {},
  data () {
    return {
      rolesList: [],
      adddialogVisible: false,
      rolesForm: {
        roleName: '',
        roleDesc: ''
      },
      rolesFormRules: {
        roleName: [
          { required: true, message: '请输入角色名称', trigger: 'blur' },
          { min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ],
        roleDesc: [
          { required: true, message: '请输入描述信息', trigger: 'blur' },
          { min: 3, max: 20, message: '长度在 3 到 20 个字符', trigger: 'blur' }
        ]
      },
      editdialogVisible: false,
      editRolesForm: {
        roleName: '',
        roleDesc: '',
        roleId: ''
      },
      edmitRolesFormRules: {
        roleName: [
          { required: true, message: '请输入角色名称', trigger: 'blur' },
          { min: 2, max: 10, message: '长度在 2 到 10 个字符', trigger: 'blur' }
        ],
        roleDesc: [
          { required: true, message: '请输入描述信息', trigger: 'blur' },
          { min: 2, max: 20, message: '长度在 2 到 20 个字符', trigger: 'blur' }
        ]
      },
      rightslist: [],
      treeProps: {
        label: 'authName',
        children: 'children'
      },
      // 默认选中的节点Id值数组
      defKeys: [],
      // 当前即将分配权限的角色id
      roleId: '',
      setRightDialogVisible: false
    }
  },
  computed: {},
  watch: {},
  created () {
    this.getRoles()
  },
  mounted () { },
  methods: {
    async getRoles () {
      const { data: res } = await this.$http.get('/roles')
      this.rolesList = res.data
      console.log(res)
    },
    submitFrom () {
      this.$refs.rolesFormRef.validate(async valid => {
        if (!valid) return
        const { data: res } = await this.$http.post('/roles', this.rolesForm)
        if (res.meta.status !== 201) {
          this.$message.error('添加角色失败')
        } else {
          this.$message.success('添加角色成功')
          this.getRoles()
        }
      })
      this.adddialogVisible = false
    },
    addDialogClosed () {
      this.$refs.rolesFormRef.resetFields()
    },
    async onEdit (id) {
      const { data: res } = await this.$http.get(`roles/${id}`)
      if (res.meta.status !== 200) {
        this.$message.error('获取数据失败')
      } else {
        console.log(123)
        console.log(res)
        this.editRolesForm = res.data
        this.$message.success('获取数据成功')
      }
      this.editdialogVisible = true
    },
    editSubnit () {
      this.$refs.deleteRolesFormRef.validate(async valid => {
        if (!valid) return
        const { data: res } = await this.$http.put(`roles/${this.editRolesForm.roleId}`, {
          roleName: this.editRolesForm.roleName,
          roleDesc: this.editRolesForm.roleDesc
        })
        if (res.meta.status !== 200) {
          this.$message.error('修改角色失败')
        } else {
          this.$message.success('修改角色成功')
          this.getRoles()
        }
      })
      this.editdialogVisible = false
    },
    async deleteData (id) {
      const confirmResult = await this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).catch(err => err)
      if (confirmResult !== 'confirm') {
        return this.$message.info('您已取消删除')
      }
      const { data: res } = await this.$http.delete('roles/' + id)
      if (res.meta.status !== 200) {
        return this.$message.error('删除用户失败')
      }
      this.$message.success('用户删除成功')
      this.getRoles()
    },
    // 根据Id删除对应的权限
    async removeRightById (role, rightId) {
      // 弹框提示用户是否要删除
      const confirmResult = await this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).catch(err => err)
      if (confirmResult !== 'confirm') {
        return this.$message.info('取消了删除！')
      }
      const { data: res } = await this.$http.delete(`roles/${role.id}/rights/${rightId}`)
      if (res.meta.status !== 200) {
        return this.$message.error('删除权限失败！')
      }
      // this.getRoles()
      role.children = res.data
    },
    async showSetRightDialog (role) {
      this.setRightDialogVisible = true
      this.roleId = role.id
      // 获取所有权限的数据
      const { data: res } = await this.$http.get('rights/tree')
      if (res.meta.status !== 200) {
        return this.$message.error('获取权限数据失败！')
      }
      // 把获取到的权限数据保存到 data 中
      this.rightslist = res.data
      console.log(this.rightslist)
      // 递归获取三级节点的Id
      this.getLeafKeys(role, this.defKeys)
    },
    getLeafKeys (node, arr) {
      // 如果当前 node 节点不包含 children 属性，则是三级节点
      if (!node.children) {
        return arr.push(node.id)
      }
      node.children.forEach(item => this.getLeafKeys(item, arr))
    },
    // 点击为角色分配权限
    async allotRights () {
      const keys = [...this.$refs.treeRef.getCheckedKeys(), ...this.$refs.treeRef.getHalfCheckedKeys()]
      const idStr = keys.join(',')
      const { data: res } = await this.$http.post(`roles/${this.roleId}/rights`, { rids: idStr })
      if (res.meta.status !== 200) {
        return this.$message.error('分配权限失败！')
      }
      this.$message.success('分配权限成功！')
      this.getRoles()
      this.setRightDialogVisible = false
    },
    // 监听分配权限对话框的关闭事件
    setRightDialogClosed () {
      this.defKeys = []
    }
  }
}
</script>
<style lang="less" scoped>
.container {
  .el-breadcrumb {
    padding-bottom: 20px;
  }
  .el-table {
    margin-top: 20px;
  }
  .el-tag {
    margin: 7px;
  }
  .bdtop {
    border-top: 1px solid #eee;
  }

  .bdbottom {
    border-bottom: 1px solid #eee;
  }
  .vcenter {
    display: flex;
    align-items: center;
  }
}
</style>
