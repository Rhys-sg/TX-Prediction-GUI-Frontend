<template>
  <v-form 
    ref="userLoginForm"
    @submit.prevent="userLoginSubmit"
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
          <v-col cols="12" md="6" style="padding-bottom: 0px;">
            <v-text-field
              v-if="isLogin === false"
              v-model="internalFirstName"
              label="First Name"
              :rules="firstNameRules"
            >
              <template v-slot:prepend-inner>
                <v-icon style="margin-right: 8px;">mdi-account</v-icon>
              </template>
            </v-text-field>
          </v-col>

          <v-col cols="12" md="6" style="padding-bottom: 0px;">
            <v-text-field
              v-if="isLogin === false"
              v-model="internalLastName"
              label="Last Name"
              :rules="lastNameRules"
            >
            </v-text-field>
          </v-col>
        </v-row>

        <v-row>
          <v-col cols="12" md="12" style="padding-bottom: 0px;">
            <v-text-field
              v-model="internalEmail"
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
        <v-btn :text="this.activeTab" variant="text" type="submit"></v-btn>
      </v-card-actions>
    </v-card>
  </v-form>
</template>

<script>
import axios from 'axios';

export default {
  props: {
    backendUrl: {
      type: String,
      required: true
    },
    isUserLogedin: {
      type: Boolean,
      required: true
    },
    firstName: {
      type: String,
      required: true
    },
    lastName: {
      type: String,
      required: true
    },
    email: {
      type: String,
      required: true
    },
    inputSets: {
      type: Array,
      required: true
    },
  },
  data() {
    return {
      isLogin: true,
      internalIsUserLogedin: this.isUserLogedin,
      internalFirstName: this.firstName,
      internalLastName: this.lastName,
      internalEmail: this.email,
      internalInputSets: this.inputSets,
      password: '',
      showPassword: false,
      confirmPassword: '',
      showConfirmPassword: false,
      firstNameRules: [
        v => !!v || 'First Name is required',
      ],
      lastNameRules: [
        v => !!v || 'last Name is required',
      ],
      emailRules: [
        v => !!v || 'Email is required',
      ],
      signupEmailRules: [
        v => !!v || 'Email is required',
        v => /.+@.+\..+/.test(v) || 'E-mail must be valid',
        v => this.validateDomain(v) || 'E-mail must end with a valid domain',
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
      validDomains: [],
    };
  },
  watch: {
    internalFirstName(newVal) {
      this.$emit('update:firstName', newVal);
    },
    internalLastName(newVal) {
      this.$emit('update:lastName', newVal);
    },
    internalEmail(newVal) {
      this.$emit('update:email', newVal);
    },
    internalInputSets(newVal) {
      this.$emit('update:internalInputSets', newVal);
    }
  },
  computed: {
    activeTab() {
      return this.isLogin ? 'Login' : 'Sign Up';
    }
  },
  created() {
    this.getValidDomain();
  },
  methods: {
    changePage() {
      this.isLogin = !this.isLogin;
    },

    async userLoginSubmit() {
      const { valid } = await this.$refs.userLoginForm.validate();
      if (!valid) {
        return;
      }      
      let submitSuccessful = this.isLogin ? await this.handleLogin() : await this.handleSignUp();
      if (submitSuccessful) {
        this.$emit('update:inputSets', 
          [{
            firstname: this.internalFirstName,
            lastname: this.internalLastName,
            email: this.internalEmail,
          }]
        );
        this.$emit('update:isUserLogedin', true);
        this.$emit('close');
      }
    },

    async handleSignUp() {
      try {
        const response = await axios.post(`${this.backendUrl}/handle_signup`, {
          firstName: this.firstName,
          lastName: this.lastName,
          email: this.email.toLowerCase(),
          password: this.password,
        });
        if (!response.data.successful) {
          this.signUpErrorMessage = 'This email already exists';
        }
        return response.data.successful;
      } catch (error) {
        console.error('An error occurred.', error);
      }
    },

    async handleLogin() {
      try {
        const response = await axios.post(`${this.backendUrl}/handle_login`, {
          email: this.email.toLowerCase(),
          password: this.password,
        });
        if (response.data.firstName && response.data.lastName) {
          this.internalFirstName = response.data.firstName;
          this.internalLastName = response.data.lastName;
          this.$emit('update:firstName', response.data.firstName);
          this.$emit('update:lastName', response.data.lastName);
          return true;
        } else {
          this.loginErrorMessage = 'Incorrect email or password';
          return false;
        }
      } catch (error) {
        console.error('An error occurred.', error);
      }
    },

    async getValidDomain() {
      try {
        const response = await axios.post(`${this.backendUrl}/get_valid_domain`);
        console.log("Domains:");
        console.log(response.data.emails);
        this.validDomains = response.data.emails;
      } catch (error) {
        console.error('An error occurred.', error);
      }
    },

    validateDomain(email) {
      if (!this.validDomains.length) {
        return false;
      }
      let domain = email.split('@')[1];
      return this.validDomains.some(validDomain => domain.endsWith(validDomain));
    }
  },
};
</script>