<template>
  <v-app>
    <v-main>
      <v-container>
        <v-row>
          <v-col>
            <img src="./assets/logo.png" width="150" alt="Her Care Clinic" />
          </v-col>
        </v-row>
        <MainTable />

        <v-row>
          <a href="https://drmonikaagrawal.com" target="_blank">
            DrMonikaAgrawal.com
          </a>
          <v-spacer> </v-spacer>
          <a href="tel:+91-91670-20945" target="_blank">+91-91670-20945</a>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import MainTable from "./components/MainTable";

export default {
  name: "App",

  components: {
    MainTable,
  },

  data: () => ({
    //
  }),
};
</script>
