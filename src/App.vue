<template>
  <div id="app">
    <nav class="navbar is-light" role="navigation" aria-label="main navigation">
      <div class="navbar-brand">
        <a class="navbar-item">
          <img
            id="logo"
            src="./assets/cine-app-logo.svg"
            width="112"
            height="28"
          />
        </a>

        <a
          role="button"
          class="navbar-burger burger"
          aria-label="menu"
          aria-expanded="false"
          data-target="navbarBasicExample"
          @click="isOpen = !isOpen"
          v-bind:class="{ 'is-active': isOpen }"
        >
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
        </a>
      </div>

      <div
        id="navbarBasicExample"
        class="navbar-menu"
        v-bind:class="{ 'is-active': isOpen }"
      >
        <div class="navbar-start">
          <router-link class="has-text-black router-link" to="/">
            <a class="navbar-item">
              <div class="item">Home</div>
            </a>
          </router-link>
          <router-link class="has-text-black" to="/new-releases">
            <a class="navbar-item">
              <div class="item">Popular Movies</div>
            </a>
          </router-link>
          <router-link class="has-text-black" to="/genres">
            <a class="navbar-item">
              <div class="item">Browse Genre</div>
            </a>
          </router-link>
          <router-link class="has-text-black" to="/favorites">
            <a class="navbar-item">
              <div class="item">Watchlist</div>
            </a>
          </router-link>
          <router-link class="has-text-black" to="/Journal">
            <a class="navbar-item">
              <div class="item">Journal</div>
            </a>
          </router-link>
        </div>

        <div class="navbar-end">
          <div class="navbar-item">
            <div class="buttons">
              <router-link to="/login">
                <a class="button is-danger is-small" @click="logout()">
                  Logout
                </a>
              </router-link>
              <router-link to="/Register">
                <a class="button is-small is-primary">
                  <strong>Register</strong>
                </a></router-link
              >
              <router-link to="/Login">
                <a class="button is-small is-light">
                  Login
                </a>
              </router-link>
            </div>
          </div>
        </div>
      </div>
    </nav>

    <!-- <div id="nav">
      <div class="has-text-right">
        <router-link to="/Login"
          ><button class="button is-small">Login</button></router-link
        >
        <router-link to="/Register"
          ><button class="button is-small">Register</button></router-link
        >
        <router-link to="/Home"
          ><button class="button is-small" @click="logout">
            Logout
          </button></router-link
        >
      </div>
      <div class="logo"><img id="logo" src="./assets/cine-app-logo.svg" /></div>
      <router-link to="/">Home</router-link> |
      <router-link to="/new-releases">Popular</router-link> |
      <router-link to="/genres">By Genre</router-link> |
      <router-link to="/favorites">Watchlist</router-link> |
      <router-link to="/Journal">Journals</router-link> | -->
    <!-- </div> -->
    <router-view />
  </div>
</template>

<script>
import firebase from "firebase";

export default {
  name: "app",
  data() {
    return {
      isOpen: false,
      isLoggedIn: false,
      currentUser: false,
    };
  },
  created() {
    this.getUser();
  },
  methods: {
    getUser() {
      if (firebase.auth().currentUser) {
        this.isLoggedIn = true;
        this.currentUser = firebase.auth().currentUser.email;
        console.log(this.isLoggedIn);
      }
      console.log(this.isLoggedIn);
    },
    logout() {
      firebase
        .auth()
        .signOut()
        .then(() => {
          alert("you have been signed out");
        })
        .then(() => {
          this.$router.push("/login");
        });
    },

    // getUser() {
    //   firebase.auth().onAuthStateChanged(function(user) {
    //     if (user) {
    //       console.log(user, " < signed in on APP.vue");
    //       this.isLoggedIn = true;
    //     } else {
    //       console.log(user, " < signed NOTTTTT in");
    //       this.isLoggedIn = false;
    //     }
    //   });
    // },
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;
  background: #012844;
  color: white;
  a {
    font-weight: bold;
    color: white;

    &.router-link-exact-active {
      color: #3abac0;
    }
  }
}

#logo {
  width: 60px;
}
a {
  color: black;
}

.item {
  margin-top: 7px;
}
</style>
