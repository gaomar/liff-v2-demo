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

      <v-flex mb-4 xs12>
        <div class="ma-4">
          <v-btn large color="success" @click="sendLINE" :disabled="isClient === false">LINEに送る</v-btn>
        </div>
      </v-flex>

<!--
      <v-flex mb-4 xs12>
        <div class="ma-4">
          <v-btn large color="success" @click="shareLINE">LINEにシェア</v-btn>
        </div>
      </v-flex>
-->

      <!-- Snackbar -->
      <div>
        <v-snackbar
          v-model="snackbarFlag"
          color="primary"
          multi-line
          :timeout="snackbarTimeout"
          top
        >
          {{ snackbarText }}
        </v-snackbar>
      </div>
    </v-layout>
  </v-container>
</template>

<script>
export default {
  data () {
    return {
      snackbarFlag: false,
      snackbarTimeout: 3000,
      snackbarText: '',
      name: '',
      imgURL: '',
      inClient: false
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
        this.isClient = liff.isInClient()
        // プロフィール取得
        liff.getProfile()
          .then(profile => {
            me.name = profile.displayName
            me.imgURL = profile.pictureUrl
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
    },
    sendLINE: function () {
      var me = this
      // LINEに送る
      liff.sendMessages([
        {
          "type": "text",
          "text": "あなたのアイコン画像を送ります"
        },
        {
          "type": "image",
          "originalContentUrl": me.imgURL,
          "previewImageUrl": me.imgURL
        }
      ])
      .then(() => {
        console.log('message sent');
        me.showSnackbar("LINEに送信しました")
/*
        // 自動的に閉じる
        setTimeout(()=> {
          liff.closeWindow()
        },
          this.snackbarTimeout
        )
*/
      })
      .catch((err) => {
        console.log('error', err)
        me.showSnackbar("LINEに送信失敗しました")
      })
    },
    shareLINE: function () {
      var me = this
      // LINEに送る
      liff.shareTargetPicker([
        {
          "type": "text",
          "text": "あなたのアイコン画像を送ります"
        },
        {
          "type": "image",
          "originalContentUrl": me.imgURL,
          "previewImageUrl": me.imgURL
        }
      ])
      .then(() => {
        console.log('message sent');
        me.showSnackbar("LINEにシェアしました")
      })
      .catch((err) => {
        console.log('error', err)
        me.showSnackbar("LINEにシェア失敗しました")
      })
    },
    showSnackbar(text) {
      this.snackbarFlag = true
      this.snackbarText = text
      setTimeout(()=> {
          this.snackbarFlag = false
          this.snackbarText = ''
        },
        this.snackbarTimeout
      )
    },
  }
}
</script>