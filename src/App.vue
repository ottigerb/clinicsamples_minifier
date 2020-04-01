<template>
  <v-app style="background:#f8f8f8;background-color:#f8f8f8;">
    <v-content>
      <v-container>
          <Optimizer />
      </v-container>
    </v-content>
  </v-app>

</template>

<script>
import Optimizer from "./components/Optimizer";

export default {
  name: "App",

  components: {
    Optimizer
  },

  data: () => ({
    //
  })
};
</script>
