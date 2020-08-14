<template>
  <section class="hero main-container">
    <div class="movie-theatre-wrapper">
      <div class="form-wrapper">
        <p
          class="title is-3 is-size-5-mobile has-text-white has-text-weight-bold"
        >
          Login
        </p>
        <form action="index.html">
          <div class="input-field">
            <input
              class="input"
              type="email"
              id="email"
              placeholder="Email Address"
              v-model="email"
            />
          </div>
          <div class="input-field">
            <input
              class="input"
              type="password"
              id="password"
              placeholder="Password"
              v-model="password"
            />
          </div>
          <button v-on:click="login" class="button is-small mt-2">
            Login
          </button>
        </form>
      </div>
    </div>
  </section>
</template>

<script>
import "bulma/css/bulma.css";
import firebase from "firebase";
export default {
  name: "login",
  data() {
    return {
      email: "",
      password: "",
    };
  },
  methods: {
    login(e) {
      firebase
        .auth()
        .signInWithEmailAndPassword(this.email, this.password)
        .then(
          (user) => {
            alert(`account loggeed in as ${user.user.email}`);
            this.$router.push("/");
          },
          (error) => alert(error)
        );
      e.preventDefault();
    },
  },
};
</script>

<style lang="sass" scoped>
.movie-theatre-wrapper
  width: 100%
  height: 100vh
  background-position: center
  background-repeat: no-repeat
  background-size: cover
  background-image: linear-gradient(rgba(1, 40, 68 , .6), rgba(1, 40, 68 , .6)),  url("../assets/2001.jpg")
  display: flex
  justify-content: center
  align-items: center
  flex-direction: column
  position: relative

.form-wrapper
  position: absolute
  top: 10vh
</style>
