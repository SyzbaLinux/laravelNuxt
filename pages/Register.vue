<template>
  <v-container>
    <v-row >
      <v-col cols="12" xs="10" sm="10" offset-sm="1"  offset-xs="2" md="8" offset-md="2">
        <v-card class="mt-5 login-registerForm">
          <v-card-text>
            <v-form v-model="valid" @submit.prevent="register">
             <v-toolbar color="primary" dark>
               <h2>Create an Account for Free</h2>
             </v-toolbar>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" xs="12" sm="6">
                      <v-text-field
                        label="Your First Name"
                        append-icon="mdi-account-multiple-plus-outline"
                        v-model="registerForm.fname"
                        :rules="fNameRules"
                        :error-messages="fname_errors"
                        @input="clearFnameErrors"
                      />
                    </v-col>
                    <v-col cols="12" xs="12" sm="6">
                      <v-text-field
                        label="Your Last Name"
                        append-icon="mdi-account-multiple-plus-outline"
                        v-model="registerForm.lname"
                        :rules="lNameRules"
                        :error-messages="lname_errors"
                        @input="clearLnameErrors"
                      />
                    </v-col>
                  </v-row>
                  <v-row>
                    <v-col cols="12" xs="12" sm="6">
                      <v-text-field
                        label="Your Email"
                        append-icon="mdi-account-circle-outline"
                        v-model="registerForm.email"
                        :rules="emailRules"
                        :error-messages="email_errors"
                        @input="clearEmailErrors"
                        required
                        hint="A verification code will be sent"
                      />
                    </v-col>
                    <v-col cols="12" xs="12" sm="6">
                      <v-text-field
                        label="Your Phone No"
                        append-icon="mdi-account-circle-outline"
                        v-model="registerForm.mobile"
                        :rules="mobileRules"
                        :error-messages="phone_errors"
                        @input="clearPhoneErrors"
                        required
                        hint="Your phone number with country code"
                      />
                    </v-col>
                  </v-row>
                  <v-row>
                    <v-col cols="12" sm="12" md="6">
                      <v-text-field
                        name="password"
                        v-model="registerForm.password"
                        :rules="passwordRules"
                        label="Enter password"
                        :append-icon="value ? 'mdi-eye' : 'mdi-eye-off'"
                        @click:append="() => (value = !value)"
                        :type="value ? 'password' : 'text'"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="12" md="6">
                      <v-text-field
                        :append-icon="value ? 'mdi-eye' : 'mdi-eye-off'"
                        @click:append="() => (value = !value)"
                        :type="value ? 'password' : 'text'"
                        label="Confirm Password"
                        :rules="[(registerForm.password === registerForm.password_confirmation) || 'Password must match']"
                        v-model="registerForm.password_confirmation"
                      />
                    </v-col>
                  </v-row>
                  <v-checkbox
                    v-model="registerForm.accepted_terms"
                    :rules="[termsRules]"
                    :true-value="1"
                    :false-value="0"
                    :error-messages="terms_errors"
                    @click="clearTermsErrors"
                  >
                    <template v-slot:label>

                      <div >
                        I agree to

                        <v-tooltip bottom>
                          <template v-slot:activator="{ on }">
                            <a
                              target="_blank"
                              href="https://omni-learning.com/tutor_policy"
                              @click.stop
                              v-on="on"
                            >
                              Terms of use and privacy policy
                            </a>
                          </template>
                          Read our Terms of use and privacy policy
                        </v-tooltip>
                      </div>
                    </template>
                  </v-checkbox>
                </v-container>
              </v-card-text>


              <v-card-actions class="text-center">
                <v-btn
                  color="success"
                  type="submit"
                  :disabled="!valid"
                  :loading="loading"
                >
                  <v-icon>mdi-account-lock</v-icon> Register
                </v-btn>
                <v-spacer/>

                <v-btn
                  color="primary"
                  text
                  to="/login"
                  outlined
                >
                  <v-icon>mdi-account</v-icon> LogIn
                </v-btn>
              </v-card-actions>

            </v-form>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>


<script>
    export default {

        auth: false,
        data() {
            return {
                loading:false,
                valid:false,
                value: String,
                value2: String,
                registerForm: {
                    email: '',
                    password: '',
                    fname:'',
                    lname:'',
                    mobile:'',
                    accepted_terms:0,
                    password_confirmation:'',
                },

                fname_errors:'',
                lname_errors:'',
                email_errors:'',
                password_errors:'',
                phone_errors:'',
                terms_errors:'',

                passwordRules: [
                    v => !!v || 'Password is required',
                    v => v.length >= 5 || 'Password must be more than 6 characters',
                ],
                emailRules: [
                    v => !!v || 'E-mail is required',
                    v => /.+@.+/.test(v) || 'E-mail must be valid',
                ],
                termsRules: v  => {
                    if (v === 1) return true;
                    return 'You must read and agree to continue!';
                },
                mobileRules: [
                    v => !!v || 'Phone Number is Required',
                    v => v.length <= 16 || 'This seems to be a wrong phone number',
                ],
                fNameRules: [
                    v => !!v || 'First name is required',
                    v => v.length >= 3 || 'Name must be more than 3 characters',
                ],
                lNameRules: [
                    v => !!v || 'Last name  is required',
                    v => v.length >= 3 || 'Last Name must be more than 3 characters',
                ],
                confirmPasswordRules: [
                    v => !!v || 'Password must be is required',
                ],
            }
        },
        methods:{

            async register() {
                this.loading = true

                try {

                    await this.$axios.post('auth/register', this.registerForm).then(res=>{
                        this.logIn()
                    })
                } catch (e) {
                    this.error = e.response.data.message
                }

                this.loading = false
            },

            async logIn(){

               await this.$auth.loginWith('local', {
                    data: {
                        email: this.registerForm.email,
                        password: this.registerForm.password
                    },
                }).then(()=>{

                   this.$router.push('/login')

               })
            },


            clearEmailErrors(){
                if(this.email_errors){
                    this.email_errors = ''
                }
            },
            clearPasswordErrors(){
                if(this.password_errors){
                    this.password_errors = ''
                }
            },
            clearPhoneErrors(){
                if(this.phone_errors){
                    this.phone_errors = ''
                }
            },
            clearFnameErrors(){
                if(this.fname_errors){
                    this.fname_errors = ''
                }
            },
            clearLnameErrors(){
                if(this.lname_errors){
                    this.lname_errors = ''
                }
            },
            clearTermsErrors(){
                if(this.terms_errors){
                    this.terms_errors = ''
                }
            }
        }
    }
</script>

