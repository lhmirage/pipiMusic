<template>
  <div class="register-wrapper">
    <div class="register-box">
      <div class="logo">
        <img src="../assets/images/logo-a.png"
             alt="">
      </div>
      <h2>PP MUSIC</h2>
      <el-form ref="form"
               :model="formData"
               label-width="0"
               :rules="formRules">
        <el-form-item prop="nickname">
          <div class="item">
            <i class="iconfont iconnicheng"></i>
            <el-input v-model="formData.nickname"
                      placeholder="请输入昵称"></el-input>
          </div>
        </el-form-item>
        <el-form-item prop="phone">
          <div class="item phone">
            <i class="iconfont iconshouji"></i>
            <el-input v-model="formData.phone"
                      placeholder="请输入手机号"></el-input>
            <span @click="getCaptcha" v-show="oversend">发送验证码</span>
          </div>
        </el-form-item>
        <el-form-item prop="password">
          <div class="item">
            <i class="iconfont iconmima"></i>
            <el-input v-model="formData.password"
                      placeholder="请输入密码"
                      type="password"></el-input>
          </div>
        </el-form-item>
        <el-form-item prop="captcha">
          <div class="item">
            <i class="iconfont iconyanzhengma"></i>
            <el-input v-model="formData.captcha"
                      placeholder="请输入验证码"></el-input>
          </div>
        </el-form-item>
        <el-form-item class="login-botton">
          <el-button @click="submit">注册</el-button>
        </el-form-item>
        <div class="register">
          <router-link to="/login"
                       tag="a">登录</router-link>
        </div>
      </el-form>
    </div>
  </div>
</template>

<script>

export default {
  name: 'Login',
  data () {
    return {
      formData: {
        phone: "",
        password: "",
        captcha: "",
        nickname: ""
      },
      formRules: {
        nickname: [
          { required: true, message: '请输入正确昵称！', trigger: 'blur' },
          { min: 2, max: 16, message: '长度在2-16为字符之间', trigger: 'blur' }
        ],
        phone: [
          { required: true, message: '请输入手机号！', trigger: 'blur' },
          { min: 11, max: 11, message: '手机号码格式不对！', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码！', trigger: 'blur' },
          { min: 6, max: 16, message: '密码格式不对！', trigger: 'blur' }
        ],
        captcha: [
          { required: true, message: '请输入验证码！', trigger: 'blur' },
          { min: 4, max: 6, message: '验证码格式不对！', trigger: 'blur' }
        ]
      },
      parallax: 'depth',
      verifyData: false,
      captchaData: false,
      oversend: true
    }
  },
  methods: {
    submit () {
      this.$refs.form.validate(valid => {
        if (!valid) {
          return this.$message.error("表单验证不通过")
        }
        if (!this.captchaData) {
          return this.$message.error("需要获取验证码")
        }
        // 先验证验证码
        this.verifyCaptcha()
        if (!this.verifyData) {
          return this.$message.error("请输入正确的验证码")
        }
        // 验证成功
        if (this.verifyData) { }
        try {
          this.$api.register(this.formData).then(res => {
            if (res.data.code === 200) {
              this.$message.success("注册成功")
              this.$router.replace("/login")
            }
            return this.$message.error("昵称重复了")
          })
        } catch (err) {
          console.log(err);
        }
      })
    },
    // 发送验证码
    async getCaptcha () {
      let res = await this.$api.getCaptcha(this.formData.phone)
      this.captchaData = res.data.data
      oversend = false
      setTimeout(() => {
        oversend = true
      }, 60000)
    },
    // 验证验证码
    async verifyCaptcha () {
      let params = {
        phone: this.formData.phone,
        captcha: this.formData.captcha
      }
      let res = await this.$api.verifyCaptcha(params)
      this.verifyData = res.data.data
    }
  }
}
</script>

<style lang="less">
.register-wrapper {
  width: 100%;
  height: 100vh;
  background: #fff url("../assets/images/newbg1.png") right no-repeat;
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 100;
  overflow: hidden; // 解决高度塌陷问题
  .register-box {
    width: 350px;
    height: 486px;
    margin: 100px auto;
    border-radius: 10px;
    box-shadow: 1px 2px 15px rgba(0, 0, 0, 0.3);
    background: url("../assets/images/logbg.jpg") bottom center #fff;

    .logo {
      display: flex;
      justify-content: center;
      padding: 20px 0 0;

      img {
        height: 47px;
        width: 55px;
      }
    }

    h2 {
      text-align: center;
      font-weight: 400;
      padding-bottom: 20px;
    }

    .item {
      background-color: #fff;
      border: 1px solid #dcdfe6;
      border-radius: 5px;
      margin: 0px 25px 5px;

      i {
        position: relative;
        top: 16px;
        left: 15px;
        width: 40px;
        text-align: center;
        z-index: 10;
      }

      .el-input {
        display: block;
        padding: 0 25px;

        .el-input__inner {
          display: block;
          border: 0;
          padding: 0px 10px 10px;
        }
      }
    }
    .phone {
      position: relative;
      span {
        position: absolute;
        top: 50%;
        right: 8px;
        transform: translate(0, -50%);
        background-color: rgba(229, 221, 217, 0.64);
        height: 30px;
        line-height: 30px;
        font-size: 12px;
        border-radius: 6px;
        padding: 0 8px;
        cursor: pointer;
      }
      i {
        left: 15px;
      }
    }
    .el-form-item__error {
      margin-left: 40px;
      color: red;
    }
    .login-botton {
      margin: 30px 25px 0;
      .el-button {
        display: block;
        width: 100%;
        font-size: 15px;
        background-color: #f780e3;
        color: #fff;
        height: 30px;
        cursor: pointer;
      }
    }
    .register {
      padding-right: 24px;
      margin-top: 10px;
      a {
        float: right;
      }
    }
  }
}
</style>