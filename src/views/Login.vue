<template>
  <v-container>
    <v-layout
      text-center
      wrap
    >
      <v-flex xs12>
        <h1>ようこそ！ {{name}} さん</h1>
        <h3><v-btn large color="error" @click="logout">ログアウト</v-btn></h3>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
export default {
  data () {
    return {
      name: ''
    }
  },
  created () {
    this.initializeLiff()
  },
  methods: {
    initializeLiff: function () {
      var me = this
      liff.init(
        {
          liffId: process.env.VUE_APP_LIFF_ID
        },
        data => {
          me.loginCheck()
        },
        err => {
          console.log('LIFF initialization failed', err)
        }
      )
    },
    loginCheck: function () {
      var me = this

      // ログインチェック
      if (liff.isLoggedIn()) {
        // プロフィール取得
        liff.getProfile()
          .then(profile => {
            me.name = profile.displayName
          })
          .catch((err) => {
            console.log('error', err)
          })
      } else {
        // ログインまだ
        this.$router.push({ name: 'home' })
      }
    },
    logout: function () {
      // ログアウト
      if (liff.isLoggedIn()) {
        liff.logout()
        this.$router.push({ name: 'home' })
      }
    }
  }
}
</script>