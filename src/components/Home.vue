<template>
  <div class="home-container">
    <el-container>
      <el-header>
        <div class="header-left">
          <img src="../assets/heima.png" alt="">
          <span>电商后台管理系统</span>
        </div>
        <div class="header-button">
          <el-button type="primary" @click="loginout">退出</el-button>
        </div>
      </el-header>
      <el-container>
        <el-aside :width="isCollapse?'60px':'200px'">
          <div class="toggle-button" @click="toggleCollapse">|||</div>
          <!-- 导航菜单 -->
          <el-menu :unique-opened=true background-color="#545c64" text-color="#fff" active-text-color="red"
            :collapse-transition=false :default-active="avtivePath" router :collapse="isCollapse">
            <!-- 一级菜单 -->
            <el-submenu :index="item.id+''" v-for="item in getList" :key="item.id">
              <template slot="title">
                <i :class="iconList[item.id]"></i>
                <span>{{item.authName}}</span>
              </template>
              <!-- 二级菜单 -->
              <el-menu-item :index="'/'+data.path" v-for="data in item.children" :key="data.id"
                @click="clickBtn(data.path)">
                <template slot="title">
                  <i class="el-icon-menu"></i>
                  <span>{{data.authName}}</span>
                </template>
              </el-menu-item>
            </el-submenu>
          </el-menu>
        </el-aside>
        <el-main>
          <router-view />
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>
<script>
export default {
  data () {
    return {
      getList: [],
      avtivePath: '',
      iconList: {
        125: 'iconfont icon-user',
        103: 'iconfont icon-tijikongjian',
        101: 'iconfont icon-shangpin',
        102: 'iconfont icon-danju',
        145: 'iconfont icon-baobiao'
      },
      isCollapse: false
    }
  },
  created () {
    this.getAllList()
    this.avtivePath = window.sessionStorage.getItem('avtivePath')
  },
  methods: {
    loginout: function () {
      window.sessionStorage.clear()
      this.$router.push('./login')
    },
    async getAllList () {
      const { data: res } = await this.$http.get('menus')
      if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
      this.getList = res.data
      console.log(res)
    },
    toggleCollapse () {
      this.isCollapse = !this.isCollapse
    },
    clickBtn (path) {
      window.sessionStorage.setItem('avtivePath', '/' + path)
    }
  }
}
</script>
<style lang="less" scoped>
.home-container {
  height: 100%;
  .el-container {
    height: 100%;
    .el-header {
      display: flex;
      justify-content: space-between;
      background-color: #545c64;
      .header-left {
        display: flex;
        justify-content: center;
        align-items: center;
        span {
          margin-left: 8px;
          font-size: 23px;
          color: #fff;
        }
      }
      .header-button {
        line-height: 60px;
      }
    }
    .el-container {
      .el-aside {
        background-color: #545c64;
        .toggle-button {
          display: flex;
          justify-content: center;
          align-items: center;
          color: pink;
        }
        .el-menu {
          border: none;
          .el-submenu {
            .el-submenu__title {
              span {
                margin-left: 10px;
              }
            }
          }
        }
      }
    }
  }
}
</style>
