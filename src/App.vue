<template>
  <v-app>
    <v-container class="my-5">
      <p class="text-center text-h5 grey--text text--darken-2 font-weight-bold">Enter all required details</p>
      <v-row justify="center" class="ma-4">
        <v-col cols="12" xs="12" md="6" lg="4">
          <v-form class="text-center" v-model="valid" ref="form">
            <v-text-field outlined label="Username" required prepend-icon="account_box" v-model="username" :rules="usernameRules"></v-text-field>
            <v-text-field outlined label="Password" required prepend-icon="vpn_key" v-model="password" :rules="passwordRules"
            :append-icon="show ? 'visibility' : 'visibility_off'" :type="show ? 'text' : 'password'" @click:append="show = !show"
            ></v-text-field>
            <v-text-field label="How many courses are you going to take?" required :prepend-icon="returnIcon" v-model="courseCount"
            hint="Type the number of courses, default: 5" persistent-hint :rules="courseCountRules"
            :disabled="courseCountDisabled"></v-text-field>
            <v-divider class="mt-5"></v-divider>
            <v-text-field v-for="i in +courseCount" :key="i" :label="`CRN ${i}`" required :rules="courseRules"
            v-model="crns[i - 1]">
            </v-text-field>
            <v-btn class="mt-5" color="primary" @click="submitForm" :disabled="!valid" :loading="loading">
              Submit
            </v-btn>
            <p class="mt-2" v-if="success">Success!</p>
          </v-form>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',

  data: () => ({
    loading: false,
    success: false,
    valid: false,
    show: false,
    username: '',
    password: '',
    courseCount: 5,
    courseCountDisabled: false,
    crns: [],
    usernameRules: [
      v => !!v || 'Username is required',
    ],
    passwordRules: [
      v => !!v || 'Password is required'
    ],
    courseRules: [
      v => !!v || 'CRN is required',
      v => v && /\d+/g.test(v) || 'Type valid CRN',
      v => v && v.length == 5 || 'Exact length of CRN is 5'
    ],
    courseCountRules: [
      v => !!v || 'The number of courses is required',
      v => v >= 1 || 'Type valid number of courses',
      v => v <= 5 || 'Max number of courses is 5 for now'
    ]
  }),
  watch: {
    courseCount() {
      if (this.courseCount > 5 || this.courseCount < 1) {
        this.courseCount = 5;
      }
    }
  },
  computed: {
    returnIcon() {
      return `filter_${this.courseCount}`
    }
  },
  methods: {
    submitForm() {
      this.loading = true;
      axios.post(`http://localhost:4000/`, {
        username: this.username,
        password: this.password,
        crns: this.crns
      }).then(() => {
        this.loading = false;
        this.success = true;
      })
      .catch(() => {
        this.loading = false;
      })
    }
  }
};
</script>
