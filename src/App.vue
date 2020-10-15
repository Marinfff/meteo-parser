<script>
import Core from './Core.vue';
import WindowBar from './WindowBar.vue';

export default {
  name: 'App',
  components: {
    Core,
    WindowBar,
  },
  beforeMount() {
    this.$vuetify.theme.dark = true
  }
};
</script>

<template>
  <v-app class="application--toolbar application--footer application--footer-fixed fullHeigth">
    <WindowBar></WindowBar>
    <Core></Core>
  </v-app>
</template>

<style>
  html {
    overflow-y: auto;
  }
</style>
