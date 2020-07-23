<template>
  <v-app>
      <Weather />
  </v-app>
</template>

<script>
import Weather from './components/Weather';

export default {
  name: 'App',

  components: {
    Weather
  }
};
</script>