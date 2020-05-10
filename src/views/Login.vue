<template>
  <el-container>
    <el-main>
      <el-card shadow="never">
        <div style="display: flex; flex-direction: row; justify-content: center; margin-bottom: 36px">
        <el-image
          style="width: 100px; height: 100px"
          :src="`${publicPath}img/icons/android-chrome-192x192.png`"
          :fit="fit"></el-image>
        </div>
        <el-form>
          <el-form-item label="Username" prop="model.username">
            <el-input placeholder="Username" v-model="model.username" auto-complete="off"></el-input>
          </el-form-item>
          <el-form-item label="Password" prop="model.password">
            <el-input placeholder="Password" v-model="model.password" show-password auto-complete="off"></el-input>
          </el-form-item>
          <el-row>
            <el-button type="primary" @click="login" size="medium">Login</el-button>
          </el-row>
        </el-form>
      </el-card>
    </el-main>
  </el-container>
</template>

<style lang="stylus" scoped>
  .el-button--medium
    width 100%
    border-radius 32px
    height 50px
  .el-main
    max-width 600px
  .el-card__body
    padding 48px
  .el-container
    height 100vh
    align-items center
    justify-content center
</style>

<script lang="ts">
import router from '../router'
export default {
  data () {
    return {
      publicPath: process.env.BASE_URL,
      model: {
        username: '',
        password: ''
      }
    }
  },
  methods: {
    login () {
      if (this.model.username === 'admin' && this.model.password === 'admin') {
        const token = this.generateToken(32)
        localStorage.setItem('accessToken', token)
        this.$message.success('Login successfully')
        router.push({ path: 'home' })
      } else {
        this.model.username = ''
        this.model.password = ''
        this.$message.error('Invalid username and password')
      }
    },
    generateToken (n: number) {
      const chars = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789'
      let token = ''
      for (let i = 0; i < n; i++) {
        token += chars[Math.floor(Math.random() * chars.length)]
      }
      return token
    }
  }
}
</script>
