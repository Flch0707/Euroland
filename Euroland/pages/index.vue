<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <div class="text-center">
<!--         <logo />
 -->        <vuetify-logo />
        <v-card>
          <v-card-text class="headline">
            Welcome to EUROLAND!
          </v-card-text>
          <v-card-text>
            <p>Travel through Europe in this fantastic game !</p>
            <p>Let's go on a new adventure</p>
            <div class="text-xs-right">
              <em><small>&mdash; Florian Cheveau</small></em>
            </div>
            <hr class="my-3">
          </v-card-text>
          <v-card-actions>
            <v-spacer />
            <v-btn
              color="primary"
              nuxt
              to="/Destination"
            >
              DESTINATIONS
            </v-btn>
            <v-spacer />
          </v-card-actions>
        </v-card>
      </div>
    </v-col>
  </v-row>
</template>

<script>
/* import Logo from '~/components/Logo.vue'
 */import VuetifyLogo from '~/components/VuetifyLogo.vue'

export default {
  components: {
    /*     Logo,
 */ VuetifyLogo
  }
}
</script>
