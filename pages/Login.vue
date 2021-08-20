<template>
  <div>
    <v-row >
      <v-col cols="12" xs="8" sm="8" offset-sm="2"  offset-xs="2" md="6" offset-md="3">
        <v-card class="mt-5 pa-3 login-form">
          <v-form v-model="valid" @submit.prevent="userLogin">
              <v-card-title>
                <h1>SignIn</h1>
              </v-card-title>
              <v-card-text>
                <v-text-field
                  label="Your Email"
                  append-icon="mdi-account-circle-outline"
                  v-model="form.email"
                  :rules="emailRules"
                  :error-messages="email_errors"
                  @input="clearEmailErrors"
                  required
                />
                <v-text-field
                  label="Your Password"
                  type="password"
                  append-icon="mdi-shield-account"
                  v-model="form.password"
                  :rules="passwordRules"
                  :error-messages="password_errors"
                  @input="clearPasswordErrors"
                  required
                />


                <v-alert
                  border="left"
                  dismissible
                  icon="mdi-alert"
                  type="error"
                  v-if="get_gen_errors"
                >
                  {{ get_gen_errors }}
                </v-alert>
                <v-row>
                  <v-col>
                    <v-checkbox
                      v-model="form.remember"
                      label="Remember Me"
                      color="success"
                    ></v-checkbox>
                  </v-col>
                </v-row>
              </v-card-text>

            <v-card-actions class="text-center">
              <v-btn
                color="success"
                type="submit"
                :disabled="!valid"
                :loading="loading"
              >
                <v-icon>mdi-account-lock</v-icon> Login
              </v-btn>

              <v-spacer/>
              <v-btn
                color="primary"
                outlined
                to="/register"
              >
                <v-icon>mdi-account-multiple-plus-outline</v-icon> &nbsp;Register
              </v-btn>
            </v-card-actions>

          </v-form>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
    export default {
        data() {
            return {
                valid:true,
                loading:false,
                form: {
                    email: '',
                    password: '',
                    remember:''
                },
                passwordRules: [
                    v => !!v || 'Password is required',
                    v => v.length >= 5 || 'Name must be more than 6 characters',
                ],
                emailRules: [
                    v => !!v || 'E-mail is required',
                    v => /.+@.+/.test(v) || 'E-mail must be valid',
                ],
                email_errors:'',
                password_errors:'',
                get_gen_errors:'',
            }
        },
        methods: {
            async userLogin() {
                try {

                     let response = await this.$auth.loginWith('local', { data: this.form })

                } catch (err) {
                    console.log(err)
                }
            },

            clearEmailErrors(){
                if(this.email_errors){
                    this.email_errors = ''
                }
                this.clearGenErrors()
            },
            clearPasswordErrors(){
                if(this.password_errors){
                    this.password_errors = ''
                }
                this.clearGenErrors()
            },
            clearGenErrors(){
                if(this.gen_error){
                    this.gen_error = ''
                }
            }
        }
    }
</script>
