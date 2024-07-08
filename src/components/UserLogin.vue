<template>
  <v-form 
    ref="userLoginForm"
    @submit.prevent="userLoginSubmit()"
  >
    <v-card :title="this.activeTab" style="width: 500px; max-height: 600px; top: 50%; left: 50%; transform: translate(-50%, 0%);">
      <v-card-text style="padding-top: 0px;">
        
        <v-row>
          <v-col
            cols="12"
            md="12"
            style="
              display: flex;
              align-items: center;
              justify-content: left;
              padding-top: 0px;
              padding-bottom: 0px;
            "
          >
            <span>{{ isLogin ? 'New user?' : 'Already have an account?' }}</span>
            <v-btn
              style="padding-left: 8px; text-transform: unset !important;"
              variant="plain"
              text
              @click="changePage"
            >
              {{ isLogin ? 'Create an account' : 'Sign in' }}
            </v-btn>
          </v-col>
        </v-row>

        <v-row>
          <v-col cols="12" md="12" style="padding-bottom: 0px;">
            <v-text-field
              v-if="isLogin === false"
              v-model="fullName"
              label="Full Name"
              :rules="fullNameRules"
            >
              <template v-slot:prepend-inner>
                <v-icon style="margin-right: 8px;">mdi-account</v-icon>
              </template>
            </v-text-field>
          </v-col>
        </v-row>

        <v-row>
          <v-col cols="12" md="12" style="padding-bottom: 0px;">
            <v-text-field
              v-model="email"
              label="Email"
              :rules="isLogin ? emailRules : signupEmailRules"
              :error-messages="signUpErrorMessage && !isLogin ? [signUpErrorMessage] : []"
            >
              <template v-slot:prepend-inner>
                <v-icon style="margin-right: 8px;">mdi-email</v-icon>
              </template>
            </v-text-field>
          </v-col>
        </v-row>

        <v-row>
          <v-col cols="12" md="12" style="padding-bottom: 0px;">
            <v-text-field
              v-model="password"
              label="Password"
              :append-inner-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
              :rules="isLogin ? passwordRules : signupPasswordRules"
              :type="showPassword ? 'text' : 'password'"
              :counter="!isLogin"
              @click:append-inner="showPassword = !showPassword"
              :error-messages="loginErrorMessage && isLogin ? [loginErrorMessage] : []"
            >
              <template v-slot:prepend-inner>
                <v-icon style="margin-right: 8px;">mdi-lock</v-icon>
              </template>
            </v-text-field>
          </v-col>
        </v-row>

        <v-row>
          <v-col cols="12" md="12" style="padding-bottom: 0px;">
            <v-text-field
              v-if="isLogin === false"
              v-model="confirmPassword"
              label="Confirm Password"
              :append-inner-icon="showConfirmPassword ? 'mdi-eye' : 'mdi-eye-off'"
              :rules="confirmPasswordRules"
              :type="showConfirmPassword ? 'text' : 'password'"
              counter
              @click:append-inner="showConfirmPassword = !showConfirmPassword"
            >
              <template v-slot:prepend-inner>
                <v-icon style="margin-right: 8px;">mdi-lock</v-icon>
              </template>
            </v-text-field>
          </v-col>
        </v-row>
      </v-card-text>

      <v-card-actions style="display: flex; justify-content: center; padding-top: 0px; padding-bottom: 12px; padding-right: 22px;">
        <v-btn text="Close" variant="text" @click="$emit('close')">Close</v-btn>
        <v-btn :text="this.activeTab" variant="text" type="submit"></v-btn>
      </v-card-actions>
    </v-card>
  </v-form>
</template>

<script>
import axios from 'axios'

export default {
  props: {
    backendUrl: {
      type: String,
      required: true
    },
  },
  data() {
    return {
      backendUrl: backendUrl,
      isLogin: true,
      fullName: '',
      email: '',
      password: '',
      showPassword: false,
      confirmPassword: '',
      showConfirmPassword: false,
      fullNameRules: [
        v => !!v || 'Full Name is required',
      ],
      emailRules: [
        v => !!v || 'Email is required',
      ],
      signupEmailRules: [
        v => !!v || 'Email is required',
        v => /.+@.+\..+/.test(v) || 'E-mail must be valid',
      ],
      passwordRules: [
        v => !!v || 'Password is required',
      ],
      signupPasswordRules: [
        v => !!v || 'Password is required',
        v => (v && v.length >= 8) || 'Password must be at least 8 characters',
      ],
      confirmPasswordRules: [
        v => !!v || 'Confirm Password is required',
        v => (v && v.length >= 8) || 'Password must be at least 8 characters',
        v => v === this.password || 'Passwords must match',
      ],
      loginErrorMessage: '',
      signUpErrorMessage: '',
    };
  },
  computed: {
    activeTab() {
      return this.isLogin ? 'Login' : 'Sign Up';
    }
  },
  methods: {
    changePage (){
      this.isLogin = !this.isLogin;
    },

    async userLoginSubmit() {
      const { valid } = await this.$refs.userLoginForm.validate();
      if (!valid) {
        return;
      }
      this.submitSuccessful = this.isLogin ? await this.handleLogin() : await this.handleSignUp();
    },

    async handleSignUp() {
      try {
        const response = await axios.post(`${this.backendUrl}/handle_signup`, {
          fullName: this.fullName,
          email: this.email.toLowerCase(),
          password: this.password,
        })
        if (response.data.successful) {
          this.$emit('close')
        } else {
          this.signUpErrorMessage = 'This email already exists';
        }
      } catch (error) {
        console.error('An error occurred.', error)
      }
    },

    async handleLogin() {
      try {
        const response = await axios.post(`${this.backendUrl}/handle_login`, {
          email: this.email.toLowerCase(),
          password: this.password,
        });
        if (response.data.successful) {
          this.$emit('close')
        } else {
          this.loginErrorMessage = 'Incorrect email or password';
        }
      } catch (error) {
        console.error('An error occurred.', error);
      }
    },
  }
};
</script>
