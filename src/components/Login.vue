<template>
  <div class="login-container">
    <div class="box">
      <div class="box1">
        <img src="../assets/314613.jpg" alt="">
      </div>
      <el-form class="box2" :model="loginForm" :rules="loginFromRules" ref="loginFormRef">
        <el-form-item prop="username">
          <el-input v-model="loginForm.username" placeholder="请输入用户名"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input v-model="loginForm.password" placeholder="请输入密码" type="password"></el-input>
        </el-form-item>
        <el-form-item class="btn-right">
          <el-button type="primary" @click="denglu">登录</el-button>
          <el-button type="info" @click="resetbutton">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>
<script>
export default {
  components: {},
  props: {},
  data () {
    return {
      loginForm: {
        username: 'admin',
        password: '123456'
      },
      loginFromRules: {
        username: [
          { required: true, message: '请输入用户名称', trigger: 'blur' },
          { min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ]
      }
    }
  },
  computed: {},
  watch: {},
  created () { },
  mounted () { },
  methods: {
    resetbutton () {
      this.$refs.loginFormRef.resetFields()
    },
    denglu () {
      this.$refs.loginFormRef.validate(async valid => {
        if (valid) {
          const { data: res } = await this.$http.post('login', this.loginForm)
          console.log(res)
          if (res.meta.status !== 200) {
            this.$message.error('登陆失败')
          } else {
            this.$message.success('登录成功')
            window.sessionStorage.setItem('token', res.data.token)
            this.$router.push('/home')
          }
        } else {
          this.$message.success('登录失败')
        }
      })
    }
  }
}
</script>
<style lang="less" scoped>
.login-container {
  height: 100%;
  // background: url(../assets/2023261.jpg);
  // background-size: 100%, 100%;
  background-color: red;
  .box {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    width: 500px;
    height: 300px;
    background-color: #fff;
    padding: 0 30px;
    .box1 {
      position: fixed;
      left: 50%;
      transform: translate(-50%, -50%);
      width: 130px;
      height: 130px;
      z-index: 111;
      border-radius: 50%;
      background-color: #fff;
      box-shadow: 0px 0px 7px #999;
      img {
        width: 80%;
        height: 80%;
        border-radius: 50%;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
      }
    }
    .box2 {
      margin-top: 90px;
      .btn-right {
        display: flex;
        justify-content: flex-end;
      }
    }
  }
}
</style>
