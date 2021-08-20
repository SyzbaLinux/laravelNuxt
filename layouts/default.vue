<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
      v-if="this.$auth.loggedIn"
    >

      <v-list>
        <v-list-item>
          <v-tooltip content-class="tooltip-right primary"  right v-if="miniVariant">
            <template v-slot:activator="{ attrs, on }">
              <v-list-item-avatar
                v-on="on"
                v-bind="attrs"
                color="primary" size="30">
                <v-icon color="white">
                  mdi-account-box-multiple-outline mdi-18px
                </v-icon>
              </v-list-item-avatar>
            </template>
            {{ this.$auth.user.fname }}   {{ this.$auth.user.lname }}
          </v-tooltip>

          <v-list-item-avatar
            v-else
            color="primary" size="50">
            <v-icon color="white">
              mdi-account-box-multiple-outline mdi-30px
            </v-icon>
          </v-list-item-avatar>

          <v-list-item-content>
            <v-list-item-title class="text-h6">
              {{ this.$auth.user.fname }}   {{ this.$auth.user.lname }}
            </v-list-item-title>
            <v-list-item-subtitle>
              {{ this.$auth.user.role }}
              <br>
              {{ this.$auth.user.email }}
            </v-list-item-subtitle>
          </v-list-item-content>
        </v-list-item>
      </v-list>
      <v-divider></v-divider>

      <v-list dense nav>
        <v-subheader v-show="!miniVariant">Omni Learning tutor App</v-subheader>
        <v-list-item-group color="primary">
          <v-list-item
            v-for="(item, i) in items"
            :key="i"
            :to="item.to"
            router
            exact
          >
            <v-list-item-action>
              <v-icon>{{ item.icon }}</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title v-text="item.title" />
            </v-list-item-content>
          </v-list-item>

          <v-list-item  class="logout" @click="userLogOut" >
            <v-list-item-action>
              <v-tooltip content-class="tooltip-right primary"  right v-if="miniVariant">
                <template v-slot:activator="{ attrs, on }">
                  <v-icon
                    v-on="on"
                    v-bind="attrs"
                  >
                    mdi-logout
                  </v-icon>
                </template>
                <span>Sign Out</span>
              </v-tooltip>

              <v-icon v-else>
                mdi-logout
              </v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title >Sign Out</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar
      :clipped-left="clipped"
      fixed
      app
    >

      <v-btn
        icon
        @click.stop="miniVariant = !miniVariant"
        v-if="this.$auth.loggedIn"
      >
        <v-icon>mdi-{{ `chevron-${miniVariant ? 'right' : 'left'}` }}</v-icon>
      </v-btn>
      <v-btn icon @click="drawer = !drawer" color="primary" class="mx-1">
        <v-icon>mdi-menu</v-icon>
      </v-btn>

      <v-toolbar-title>Omni Learning</v-toolbar-title>

      <v-spacer/>

       <template v-if="!this.$auth.loggedIn">
         <v-btn icon  to="/" fab small class="mx-1">
           <v-icon>mdi-home</v-icon>
         </v-btn>
         <v-btn outlined to="/login" color="success" class="mx-1">
           Sign In
         </v-btn>
         <v-btn  dark to="/register" color="primary" class="mx-1">
           Sign up
         </v-btn>
       </template>


    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  data () {
    return {
      clipped: true,
      drawer: true,
      fixed: false,
      items: [
        {
          icon: 'mdi-apps',
          title: 'Welcome',
          to: '/'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'Inspire',
          to: '/inspire'
        }
      ],
      miniVariant: false,
      right: true,
      title: 'Vuetify.js'
    }
  },

    methods:{
        async userLogOut() {
            try {
                let response = await this.$auth.logout()
            } catch (err) {
                console.log(err)
            }
        }
    }
}
</script>

<style  >
  .logout{
    position: fixed;
    bottom: 0;
    width: 100%;
  }
</style>
