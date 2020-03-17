<template lang="pug">
  .container.p-3(v-cloak)
    .row
      .col-12.d-flex.flex-column.justify-content-between
        h1.text-center.text-white オールスター風感謝祭
        LogoImage
        .my-2
          .alert.alert-danger(v-if="hasWarnings()")
            ul.mb-1
              li(v-for="(v,i) in warnings") {{v}}
        //- .input-group.input-group-lg.mb-2
        //-   .input-group-prepend
        //-     .input-group-text.w-100.text-center 番号
        //-   input.form-control(type="number" v-model="loginNumber" pattern="\d*" )
        .input-group.input-group-lg.mb-2
          .input-group-prepend
            .input-group-text.w-100.text-center 名前
          input.form-control(type="text" v-model.trim="loginAccount")
        button.p-3.w-100.btn.btn-lg.btn-primary(@click="login") ログイン
</template>

<script>
import axios from "axios"
import LogoImage from "~/components/LogoImage";

export default {
  components: {
    LogoImage
  },
  data() {
    return {
      account: null,
      loginNumber: null,
      loginAccount: null,
      name: null,
      warnings: []
    };
  },
  computed: {
    accountName() {
      return this.account != null ? this.account.name : "";
    },
  },
  mounted() {
  },
  methods: {
    addWarning(text) {
      this.warnings.push(text);
    },
    clearWarning() {
      this.warnings = [];
    },
    hasWarnings() {
      return this.warnings.length > 0;
    },
    async login() {
      try {
        this.clearWarning();

        if(!this.loginAccount){
          this.addWarning('アカウント名を入力してください。')
          return
        }

        await this.$axios.post(`/user/make/${this.loginAccount}`)
          .then(function(res){
          })
          .catch(function(err){
            const check = window.confirm('名前が重複しています。再ログインの場合のみOKを押してください')
            if(check === false){
              throw new Error("名前が重複しています。異なる名前を指定してください")
            }
          })

        await this.$axios.get(`/user/${this.loginAccount}`)
          .then(function(res){
            console.log(res)
            this.loginAccount = res.data.name
            this.loginNumber = res.data.id

            this.$store.dispatch("login", {
              loginNumber: this.loginNumber,
              loginAccount: this.loginAccount
            })
          }.bind(this))

        this.$router.push("/client")
      } catch (e) {
        this.addWarning(e.message);
        console.error(e.message);
      }
    }
  }
};
</script>

<style lang="scss">

body {
  background-repeat: repeat;
  background-image: url("/img/background.png");
  background-color: #02aebe;
}

.container{
  max-width:540px;
}

.input-group-prepend{
  width: 8em;
}
</style>
