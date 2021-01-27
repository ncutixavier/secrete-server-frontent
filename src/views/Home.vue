<template>
  <v-container fill-height fluid>
    <v-layout justify-center>
      <v-flex xs12 md6 xl3>
        <v-card class="mx-auto pa-10" height="100%">
          <v-alert type="success" v-show="response" text>
            Secret created successful
          </v-alert>
          <v-alert color="success" v-show="response" text>
           <p> Hashed Message (Copy this): {{hash}}</p>
          </v-alert>
          <v-alert type="error" v-show="error" text>
            Error occured, Try again
          </v-alert>
          <h1 class="display-2 my-10">Create secret</h1>
          <v-form
            ref="form"
            height="100%"
            v-model="valid"
            v-on:submit.prevent="addSecret"
          >
            <v-text-field
              v-model="secret"
              :rules="secretRules"
              label="Secret Message"
              required
            ></v-text-field>

            <v-text-field
              v-model="expireAfterViews"
              type="Number"
              :rules="expireAfterViewsRules"
              label="Limited Views"
              required
            ></v-text-field>

            <v-text-field
              v-model="expireAfter"
              type="Number"
              :rules="expireAfterRules"
              label="Time to expire (In minutes)"
              required
            ></v-text-field>

            <v-btn color="primary" class="mr-4 my-4" @click="validate">
              create
            </v-btn>
            <v-btn color="success" class="mr-4 my-4" @click="$router.push({ name: 'secret' })">
              check hashed message
            </v-btn>
          </v-form>
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
    valid: true,
    secret: '',
    secretRules: [(v) => !!v || 'Secret is required'],
    expireAfterViewsRules: [(v) => !!v || 'Limited views is required'],
    expireAfterRules: [(v) => !!v || 'Time is required'],
    item: {},
    hash: ''
  }),

  methods: {
    validate() {
      this.$refs.form.validate();
      axios
        .post(`https://secrete-server-staging.herokuapp.com/api/secret/`, {
          secret: this.secret,
          expireAfterViews: this.expireAfterViews,
          expireAfter: this.expireAfter,
        })
        .then((response) => {
          if (response) {
            this.response = true;
            this.$refs.form.reset();
            this.hash = response.data.data.message.hash
          }
        })
        .catch((e) => {
          if(e){
            this.error = true
          }
        });
    },
  },
};
</script>
