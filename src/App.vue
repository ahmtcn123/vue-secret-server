<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        Secret Server
      </div>

      <v-spacer></v-spacer>
    </v-app-bar>

    <v-main>
      <v-container>
        <v-row class="mb-6" no-gutters>
          <v-col md="6" sm="5">
            <CreateSecret/>
          </v-col>
          <v-col md="6" sm="5">
            <ViewSecret />
          </v-col>

        </v-row>
      </v-container>


    </v-main>
  </v-app>
</template>

<script>
import CreateSecret from './components/CreateSecret';
import ViewSecret from './components/ViewSecret';

export default {
  name: 'App',

  components: {
    CreateSecret,
    ViewSecret,
  },

  data: () => ({
    //
  }),
};
</script>
