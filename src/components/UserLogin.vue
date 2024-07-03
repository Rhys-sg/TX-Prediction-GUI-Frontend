<template v-slot:default="{ isActive }">
  <v-form 
    ref="ligateForm"
    @submit.prevent="ligateSubmit()"
  >
    <v-card :title="this.activeTab" style="width: 500px; max-height: 600px; top: 50%; left: 50%; transform: translate(-50%, 0%);">
      <v-card-text style="padding-top: 12px;">
        <v-row>
          <v-col cols="12" md="12" style="padding-bottom: 0px;">
            <v-text-field
              v-if="activeTab === 'Sign Up'"
              v-model="email"
              label="Name"
              :rules="emailRules"
              rows="1"
              variant="filled"
              required
            ></v-text-field>
          </v-col>
        </v-row>

        <v-row>
          <v-col cols="12" md="12" style="padding-bottom: 0px;">
            <v-text-field
              v-model="email"
              label="Email"
              :rules="emailRules"
              rows="1"
              variant="filled"
              required
            ></v-text-field>
          </v-col>
        </v-row>

        <v-row>
          <v-col cols="12" md="12" style="padding-bottom: 0px;">
            <v-text-field
              v-model="password"
              label="Password"
              :rules="passwordRules"
              rows="1"
              variant="filled"
              required
            ></v-text-field>
          </v-col>
        </v-row>
      </v-card-text>

      <v-card-actions style="display: flex; justify-content: center; padding-top: 0px; padding-bottom: 12px; padding-right: 22px;">
        <v-btn text="Sign Up" variant="plain" @click="this.activeTab = 'Sign Up'"></v-btn>
        <v-btn text="Login" variant="text" type="submit"></v-btn>
      </v-card-actions>
    </v-card>
  </v-form>
</template>


<script>
export default {
  data() {
    return {
      activeTab: 'Login',
      email: '',
      password: '',
      confirmPassword: '',
      emailRules: [
        v => !!v || 'Email is required',
        v => /.+@.+\..+/.test(v) || 'E-mail must be valid',
      ],
      passwordRules: [
      ],
    };
  },
  methods: {
    handleLogin() {
      // Add login logic here
      console.log('Login:', this.loginEmail, this.loginPassword);
    },
    handleSignUp() {
      if (this.signupPassword !== this.signupConfirmPassword) {
        alert('Passwords do not match!');
        return;
      }
      // Add sign-up logic here
      console.log('Sign Up:', this.signupEmail, this.signupPassword);
    },
  }
};
</script>

<style scoped>
</style>
