<template>
  <br />

  <div style="text-align: center">
    <h1 style="font-size: larger">
      Please login below using your Nate's Furniture employee credentials.
    </h1>
  </div>

  <br />

  <q-page>
    <q-form>
      <q-input v-model="email" filled type="email" hint="Email" />
      <q-input
        v-model="password"
        filled
        :type="!isPwd ? 'password' : 'text'"
        hint="Password"
      >
        <template v-slot:append>
          <q-icon
            :name="isPwd ? 'visibility_off' : 'visibility'"
            class="cursor-pointer"
            @click="isPwd = !isPwd"
          />
        </template>
      </q-input>
      <div style="display: flex; justify-content: center">
        <q-btn color="primary" label="Login" @click="login" />
      </div>
    </q-form>
    <q-page q-page-container class="flex flex-center">
      <img
        alt="Nate's Furniture logo"
        src="~assets/Logo.png"
        style="width: 200px; height: 200px"
      />
    </q-page>
  </q-page>
</template>

<script>
import { client } from "/src/pages/directusclient.js";
export const email = "";
export const password = "";
export default {
  data() {
    return {
      email,
      password,
      isPwd: false,
    };
  },
  methods: {
    async login() {
      console.log("Login method called");
      try {
        const response = await client.login(this.email, this.password);
        console.log("Login successful");
        // Store the token securely in localStorage
        localStorage.setItem("authToken", response.access_token);
        this.$router.push("/home");
      } catch (error) {
        console.error("Login error:", error);
      }
    },
  },
};
</script>
