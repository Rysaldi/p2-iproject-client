<template>
  <LoadingSign v-if="isLoading" />
  <div v-if="!isLoading" class="container pt-5">
    <div class="row justify-content-center">
      <div class="col-md-7 col-lg-5">
        <div class="p-4 p-md-5 border">
          <h1 class="text-center mb-3">Serum</h1>
          <form @submit.prevent="handleLogin">
            <div class="mb-3">
              <label class="form-label">Email address</label>
              <input v-model="login.email" class="form-control" type="text" placeholder="enter your email here" />
            </div>
            <div class="mb-3">
              <label class="form-label">Password</label>
              <input v-model="login.password" class="form-control" type="password" placeholder="enter your password here" />
            </div>
            <div class="mb-3">
              <ButtonLayout type="submit" class="btn-primary form-control" buttonName="Log in" />
            </div>
          </form>
          <p class="mt-2">
            Don't have an account yet?
            <router-link to="/register" style="text-decoration: none; color: blue">Sign Up</router-link>
          </p>
          <p class="mt-2">
            Browse houses first?
            <router-link to="/" style="text-decoration: none; color: blue">Back to Home</router-link>
          </p>
          <div class="row">
            <div class="col">
              <hr />
            </div>
            <div class="col text-center">
              <p>OR</p>
            </div>
            <div class="col">
              <hr />
            </div>
          </div>
          <div id="googleLogin"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ButtonLayout from "../components/ButtonLayout.vue";
import { mapActions, mapWritableState } from "pinia";
import { useLoginStore } from "../stores/user";
import { useHouseStore } from "../stores/house";
import LoadingSign from "../components/LoadingSign.vue";

export default {
  components: {
    ButtonLayout,
    LoadingSign,
  },

  data() {
    return {
      login: {
        email: "",
        password: "",
      },
    };
  },

  methods: {
    ...mapActions(useLoginStore, ["loginSubmission", "loginGoogle"]),
    ...mapActions(useHouseStore, ["errorHandler"]),

    async handleLogin() {
      this.isLoading = true;
      try {
        await this.loginSubmission(this.login);
        this.$router.push("/");
      } catch (error) {
        this.errorHandler(error);
      } finally {
        this.isLoading = false;
      }
    },

    async handleCredentialResponse(response) {
      this.isLoading = true;
      try {
        await this.loginGoogle(response);
        this.$router.push("/");
      } catch (error) {
        this.errorHandler(error);
      } finally {
        this.isLoading = false;
      }
    },
  },

  computed: {
    ...mapWritableState(useHouseStore, ["isLoading"]),
  },

  mounted() {
    const cb = this.handleCredentialResponse;

    google.accounts.id.initialize({
      client_id: "549755604066-jhc6otku5r65km7tam2ev48pjmnv0rnn.apps.googleusercontent.com",
      callback: cb,
    });
    google.accounts.id.renderButton(document.getElementById("googleLogin"), { theme: "outline", size: "large" });
  },
};
</script>
