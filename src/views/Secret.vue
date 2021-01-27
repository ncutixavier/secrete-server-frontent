<template>
  <v-container fill-height fluid>
    <v-layout justify-center>
      <v-flex xs12 md6 xl3>
        <v-card class="mx-auto my-6" height="70%">
          <v-alert color="primary" dark text>
            <h2 class="text-uppercase">View the secret</h2>
          </v-alert>
          <div class="pa-10">
            <v-alert type="warning" v-show="error" text>
              Your secret is no longer existed
            </v-alert>
            <v-form
              ref="form"
              height="100%"
              v-model="valid"
              v-on:submit.prevent="addSecret"
            >
              <v-text-field
                v-model="hash"
                :rules="secretRules"
                label="Paste Hashed Message"
                outlined
                required
              ></v-text-field>
              <v-btn color="primary" class="mr-4 my-4" @click="validate">
                view
              </v-btn>
            </v-form>
          </div>
        </v-card>
        <v-card class="mx-auto" v-show="response">
          <v-alert color="primary" dark text class="pa-10">
            <h3>{{ message }}</h3>
          </v-alert>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<style>
@media (max-width: 600px) {
  .v-application .pa-10 {
    padding: 20px !important;
  }
}
</style>

<script>
import axios from 'axios';
export default {
  data: () => ({
    response: false,
    error: false,
    hash: '',
    secretRules: [(v) => !!v || 'hashed message is required'],
    message: '',
  }),

  methods: {
    validate() {
      this.$refs.form.validate();
      axios
        .get(
          'https://secrete-server-staging.herokuapp.com/api/secret/' + this.hash
        )
        .then((res) => {
          if (res) {
            this.message = res.data.message.secret;
            this.response = !this.response;
            this.$refs.form.reset();
          }
        })
        .catch((e) => {
          console.log(e);
          if(e){
            this.error = !this.error;
          }
        });
    },
  },
};
</script>
