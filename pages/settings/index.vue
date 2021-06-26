<template>
  <div class="settings-page">
    <div class="container page">
      <div class="row">

        <div class="col-md-6 offset-md-3 col-xs-12">
          <h1 class="text-xs-center">Your Settings</h1>

          <form @submit.prevent="handleUpdateUser">
            <fieldset >
                <fieldset class="form-group">
                  <input v-model="copyUser.image" class="form-control" type="text" placeholder="URL of profile picture">
                </fieldset>
                <fieldset class="form-group">
                  <input v-model="copyUser.username" class="form-control form-control-lg" type="text" placeholder="Your Name">
                </fieldset>
                <fieldset class="form-group">
                  <textarea v-model="copyUser.bio" class="form-control form-control-lg" rows="8" placeholder="Short bio about you"></textarea>
                </fieldset>
                <fieldset class="form-group">
                  <input v-model="copyUser.email" class="form-control form-control-lg" type="text" placeholder="Email">
                </fieldset>
                <fieldset class="form-group">
                  <input v-model="copyUser.password" class="form-control form-control-lg" type="password" placeholder="Password">
                </fieldset>
                <button class="btn btn-lg btn-primary pull-xs-right"
                >
                  Update Settings
                </button>
            </fieldset>
          </form>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
import { updateUser } from '@/api/user'
import { mapState } from 'vuex'
// 仅在客户端加载 js-cookie 包
const Cookie = process.client ? require('js-cookie') : undefined
export default {
  middleware: 'authenticated',
  name: 'SettingsIndex',
  data () {
    return {
      copyUser: null
    }
  },
  computed: {
    ...mapState(['user'])
  },
  created () {
    this.copyUser = JSON.parse(JSON.stringify(this.user))
  },
  methods: {
    async handleUpdateUser () {
      const { user } = await updateUser({
        user: this.copyUser
      })
      console.log(user,'user55555');
      
      this.$store.commit('setUser', user)

      // 为了防止刷新页面数据丢失，我们需要把数据持久化
      Cookie.set('user', user)

      this.$router.push(`/profile/${user.username}`)
    }
  },
}
</script>

<style>

</style>
