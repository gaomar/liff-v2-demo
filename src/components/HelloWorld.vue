<template>
  <v-container>
    <v-layout
      text-center
      wrap
    >
      <v-flex xs12>
        <h3><v-btn large color="success" @click="loginAction">ログイン</v-btn></h3>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
export default {
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
          if (liff.isLoggedIn()) {
            // ログイン済み
            me.$router.push({ name: 'login' })
          }
        },
        err => {
          console.log('LIFF initialization failed', err)
        }
      )
    },
    loginAction: function () {
      // ログインまだ
      if (!liff.isLoggedIn()) {
        liff.login()
      }
    }
  }
}
</script>
