<template>
  <div>
    <h1 class="title">ユーザー登録</h1>
    <div class="field">
      <form class="control">
        <div>
          <label for="watchWord">合言葉</label>
        </div>
        <input
          v-model="inputWatchWord"
          placeholder="合言葉は？？"
          :type="watchwordType"
          class="input"
        />
        <label for="js-watchWordcheck">合言葉を表示する</label>
        <input id="js-watchWordcheck" v-model="showWatchword" type="checkbox" />
        <div>
          <label for="userName">ユーザー名</label>
          <input id="userName" v-model="userName" type="text" class="input" />
        </div>
        <div>
          <label for="password">パスワード</label>
          <input
            id="password"
            v-model="password"
            :type="passwordType"
            class="input"
          />
          <label for="password">パスワード2回目</label>
          <input
            id="password"
            v-model="password2"
            :type="passwordType"
            class="input"
          />
          <label for="js-passcheck">パスワードを表示する</label>
          <input id="js-passcheck" v-model="showPassword" type="checkbox" />
        </div>

        <button class="button" type="button" @click="submit()">登録</button>
      </form>
    </div>
    <nuxt-link to="/login">登録がお済みの方はこちらへ</nuxt-link>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
export default Vue.extend({
  data(): {
    inputWatchWord: string;
    userName: string;
    password: string;
    password2: string;
    showPassword: boolean;
    showWatchword: boolean;
  } {
    return {
      inputWatchWord: "",
      userName: "",
      password: "",
      password2: "",
      showPassword: false,
      showWatchword: false,
    };
  },
  computed: {
    passwordType(): string {
      return this.showPassword ? "text" : "password";
    },
    watchwordType(): string {
      return this.showWatchword ? "text" : "password";
    },
  },
  methods: {
    async submit(): Promise<void> {
      if (this.password !== this.password2) {
        this.$toast.error("パスワードが一致しません");
        return;
      }
      if (this.password.length === 0) {
        this.$toast.error("パスワードを入力してください");
        return;
      }
      if (this.userName.length === 0) {
        this.$toast.error("ユーザー名を入力してください");
        return;
      }
      if (this.userName.length > 30) {
        this.$toast.error("ユーザー名が長すぎます");
        return;
      }
      if (encodeURIComponent(this.password).replace(/%../g, "x").length > 50) {
        this.$toast.error("パスワードが長すぎます");
        return;
      }

      await this.$axios
        .$post("/auth/create_user", {
          username: this.userName,
          password: this.password,
          watchword: this.inputWatchWord,
        })
        .then((response) => {
          if (response.result) {
            this.$store.commit("setUser", {
              user: response.data,
              token: response.token,
            });
            this.$toast.success("ユーザーを登録しました");
            this.$router.push("/users/" + this.$store.state.currentUser.id);
            return true;
          } else {
            this.$toast.error(
              "ユーザー登録に失敗しました : " + response.message
            );
            return false;
          }
        })
        .catch((e: { response: { message: string } }) => {
          console.error(
            "エラーが発生しました。" + JSON.stringify(e.response, null, 2)
          );
          this.$toast.error(
            "ユーザー登録に失敗しました :" + e.response.message
          );
          this.$router.push("/");
        });
    },
  },
});
</script>
