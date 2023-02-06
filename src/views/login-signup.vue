<template>
  <div class="container-login main-container">
    <p>{{ msg }}</p>

    <div class="loggedin-user" v-if="loggedinUser">
      <h3>Welcome {{ loggedinUser.fullname }}</h3>
      <router-link to="/orderList" class="out-of-form">
        You can check out you orders <a class="link">here</a></router-link
      >

      <button @click="doLogout">Logout</button>
    </div>
    <div class="signup-container" v-else>
      <form @submit.prevent="doLogin" v-if="!isSignup">
        <h2>Login</h2>
        <title>Username:</title>
        <input v-model="loginCred.username" type="text" />
        <title>Password:</title>
        <input v-model="loginCred.password" type="password" />
        <button>Login</button>
      </form>
      <form v-if="isSignup" class="signup-container" @submit.prevent="doSignup">
        <h2>Signup</h2>
        <title>Full name:</title>
        <input
          type="text"
          v-model="signupCred.fullname"
          placeholder="Enter your full name"
          autocomplete="off" />
        <title>Username:</title>
        <input
          type="text"
          v-model="signupCred.username"
          placeholder="Choose a Username"
          autocomplete="off" />
        <title>Password:</title>
        <input
          type="password"
          v-model="signupCred.password"
          placeholder="Choose a password"
          autocomplete="off" />
        <button>Signup</button>
      </form>
    </div>
  </div>
  <button
    class="out-of-form btn"
    v-if="!isSignup && !loggedinUser"
    @click="toggleIsSignup">
    Or signup
  </button>
</template>

<script>
  export default {
    name: 'login-signup',
    data() {
      return {
        msg: '',
        isSignup: false,
        loginCred: { username: '118676', password: 'Lizzy' },
        signupCred: {
          username: '',
          password: '',
          fullname: '',
          imgUrl: 'https://xsgames.co/randomusers/avatar.php?g=female',
        },
      }
    },
    computed: {
      users() {
        return this.$store.getters.users
      },
      loggedinUser() {
        // console.log(this.$store.getters.loggedinUser);
        return this.$store.getters.loggedinUser
      },
    },
    created() {
      this.loadUsers()
    },
    methods: {
      async doLogin() {
        if (!this.loginCred.username) {
          this.msg = 'Please enter username/password'
          return
        }
        try {
          await this.$store.dispatch({
            type: 'login',
            userCred: this.loginCred,
          })
          this.$router.push('/')
        } catch (err) {
          console.log(err)
          this.msg = 'Failed to login'
        }
      },
      doLogout() {
        this.$store.dispatch({ type: 'logout' })
        this.imgUrl = null
      },
      async doSignup() {
        if (
          !this.signupCred.fullname ||
          !this.signupCred.password ||
          !this.signupCred.username
        ) {
          this.msg = 'Please fill up the form'
          return
        }
        await this.$store.dispatch({
          type: 'signup',
          userCred: this.signupCred,
        })
        this.$router.push('/')
      },
      loadUsers() {
        this.$store.dispatch({ type: 'loadUsers' })
      },
      async removeUser(userId) {
        try {
          await this.$store.dispatch({ type: 'removeUser', userId })
          this.msg = 'User removed'
        } catch (err) {
          this.msg = 'Failed to remove user'
        }
      },
      onUploaded(imgUrl) {
        this.signupCred.imgUrl = imgUrl
      },
      toggleIsSignup() {
        this.isSignup = true
      },
    },
    components: {},
  }
</script>
