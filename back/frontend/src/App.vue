<template>
  <div id="app">
    <Header :user="user"/>
    <Middle :posts="posts" :users="users"/>
    <Footer/>
  </div>
</template>

<script>
import Header from "./components/Header";
import Middle from "./components/Middle";
import Footer from "./components/Footer";
import axios from "axios"

export default {
  name: 'App',
  components: {
    Footer,
    Middle,
    Header
  },
  data: function () {
    return {
      user: null,
      users: [],
      posts: []
    }
  },
  beforeMount() {
    if (localStorage.getItem("jwt") && !this.user) {
      this.$root.$emit("onJwt", localStorage.getItem("jwt"));
    }

    axios.get("/api/1/posts").then(response => {
      this.posts = response.data;
    });

    axios.get("/api/1/users").then(response => {
      this.users = response.data;
    });
  },
  beforeCreate: function () {
    this.$root.$on("onEnter", (login, password) => {
      if (password === "") {
        this.$root.$emit("onEnterValidationError", "Password is required");
        return;
      }

      axios.post("/api/1/jwt", {
        login, password
      }).then(response => {
        localStorage.setItem("jwt", response.data);
        this.$root.$emit("onJwt", response.data);
      }).catch(error => {
        this.$root.$emit("onEnterValidationError", error.response.data);
      });
    });

    this.$root.$on("onRegister", (login, name, password) => {
      login = login.trim()
      name = name.trim()
      if (password === "") {
        this.$root.$emit("onRegisterValidationError", "Password is required");
        return;
      }

      if (login.length < 3 || login.length > 16) {
        this.$root.$emit("onRegisterValidationError", "Login should contain from 3 to 16 characters");
        return;
      }
      if (!login.match(/^[a-z]+$/)) {
        this.$root.$emit("onRegisterValidationError", "Login should contain only lowercase latin letters");
        return;
      }
      if (name.length === 0 || name.length > 32) {
        this.$root.$emit("onRegisterValidationError", "Name should contain from 1 to 32 characters");
        return;
      }

      axios.post("/api/1/users", {
        login, name, password
      }).catch(error => {
        this.$root.$emit("onRegisterValidationError", error.response.data);
        // eslint-disable-next-line no-unused-vars
      }).then(_ => {
        axios.get("/api/1/users").then(response => {
          this.users = response.data;
        });
        this.$root.$emit("onEnter", login, password);
      })
    });

    this.$root.$on("onJwt", (jwt) => {
      localStorage.setItem("jwt", jwt);

      axios.get("/api/1/users/auth", {
        params: {
          jwt
        }
      }).then(response => {
        this.user = response.data;
        this.$root.$emit("onChangePage", "Index");
      }).catch(() => this.$root.$emit("onLogout"))
    });

    this.$root.$on("onLogout", () => {
      localStorage.removeItem("jwt");
      this.user = null;
    });

  }
}
</script>

<style>
#app {

}
</style>
