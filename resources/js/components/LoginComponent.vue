<template>
 <v-app id="inspire">
    <v-content>
      <v-container
        class="fill-height"
        fluid
      >
        <v-row
          align="center"
          justify="center"
        >
          <v-col
            cols="12"
            sm="8"
            md="4"
          >
            <v-card class="elevation-12">
              <v-toolbar
                color="error"
                dark
                flat
              >
                <v-toolbar-title>Login form</v-toolbar-title>
                
              </v-toolbar>
              <v-card-text>
                <v-progress-linear
                  :active="loading"
                  :indeterminate="loading"
                  absolute
                  top
                  color="deep-purple accent-4"
                ></v-progress-linear>
                <v-form
                ref="form"
                v-model="valid"
                >
                  <v-text-field
                    label="Login"
                    name="email"
                    v-model="email"
                    :rules="emailRules"
                    required
                    prepend-icon="mdi-account-box"
                    type="email"
                  />

                  <v-text-field
                    id="password"
                    label="Password"
                    name="password"
                    v-model="password"
                    prepend-icon="mdi-lock"
                    type="password"
                    required
                    :rules="passwordRules"
                  />
                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-spacer />
                <v-btn :disabled="!valid" color="primary" @click="login">Login</v-btn>
              </v-card-actions>
            </v-card>
            <v-snackbar
              v-model="snackbar"
            >
              {{ text }}
              <v-btn
                color="pink"
                text
                @click="snackbar = false"
              >
                Close
              </v-btn>
            </v-snackbar>
          </v-col>
        </v-row>
      </v-container>
    </v-content>
  </v-app>
    
</template>
<script>
export default {
    data(){
       return{
         password:'',
         email:'',
         loading:false,
         snackbar:false,
         text:'',
         valid:true,
         emailRules: [
          v => !!v || 'E-mail is required',
          v => /.+@.+\..+/.test(v) || 'E-mail must be valid',
         ],
         passwordRules: [
          v => !!v || 'Password is required',
         ],
       } 
    },
    methods:{
      login: function() {

          axios.interceptors.request.use( (config)  => {
              this.loading = true;
              return config;
            }, (error) => {
              this.loading = false;
              return Promise.reject(error);
            });

          axios.interceptors.response.use( (response) => {
              this.loading = false;
              return response;
            }, (error)  => {
              this.loading = false;
              return Promise.reject(error);
            });

            axios.post('/api/auth/login',{
              'password':this.password,
              'email': this.email
            })
            .then(res => {
              localStorage.removeItem("token");
              localStorage.setItem('token',res.data.token);
              localStorage.setItem('loggedIn',true);
              this.$router.push('/admin').then(res => console.log('loggedIn')).catch(err => console.log(err))

            })
            .catch(err => {
              this.text = err.response.data.retErrMessage;
              this.snackbar = true;
            });
      },
    }
}
</script>
<style scoped>

</style>