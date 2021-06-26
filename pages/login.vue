<template>
  <div class="auth-page">
    <div class="container page">
      <div class="row">
        <div class="col-md-6 offset-md-3 col-xs-12">
          <h1 class="text-xs-center">{{ isLogin ? "Sign in" : "Sign up" }}</h1>
          <p class="text-xs-center">
            <nuxt-link v-if="isLogin" to="/register"
              >Need an account?
            </nuxt-link>
            <nuxt-link v-else to="/login">Have an account?</nuxt-link>
          </p>
          <ul class="error-messages">
            <li v-for="msg in Object.keys(errorMsgs)" :key="msg">
              {{ msg }}: {{ errorMsgs[msg].join('、')}}
            </li>
          </ul>
          <form @submit.prevent="handleFormSubmit">
            <fieldset v-if="!isLogin" class="form-group">
              <input
                v-model="user.username"
                class="form-control form-control-lg"
                type="text"
                required
                placeholder="Your Name"
              />
            </fieldset>
            <fieldset class="form-group">
              <input
                v-model="user.email"
                class="form-control form-control-lg"
                type="email"
                required
                placeholder="Email"
              />
            </fieldset>
            <fieldset class="form-group">
              <input
                v-model="user.password"
                class="form-control form-control-lg"
                type="password"
                required
                placeholder="Password"
              />
            </fieldset>
            <button class="btn btn-lg btn-primary pull-xs-right">
              {{ isLogin ? "Sign in" : "Sign up" }}
            </button>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import { login, register } from '@/api/user'

// 仅在客户端加载 js-cookie 包
const Cookie = process.client ? require('js-cookie') : undefined
export default {
  name: "LoginIndex",
  async asyncData() {
  },
  middleware: 'notAuthenticated',
  data() {
    return {
      user: {
        username: '',
        password: '',
        email: '',
      },
      errorMsgs: {}
    };
  },
  computed: {
    isLogin() {
      return this.$route.path === "/login";
    },
  },
  methods: {
    // 提交 处理
    async handleFormSubmit () {
      try {        
        const { email, password } = this.user
        const data = this.isLogin ? await login({
          user: { email, password }
        }) : await register({
          user: this.user
        })
        console.log(data);
        this.$store.commit('setUser', data.user)

        // 为了防止刷新页面数据丢失，我们需要把数据持久化
        Cookie.set('user', data.user)
        
        this.$router.push('/')
      } catch (error) {
        console.log(error, 'err515');
        const { errors } = error || {}
        this.errorMsgs = errors
      }
      
    }
  }
};
</script>
<style>
</style>
