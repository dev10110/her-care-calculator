<template>
  <v-app>
    <v-main>
      <v-container id="printSection">
        <v-container>
          <MainTable />

          <v-row>
            <a href="https://drmonikaagrawal.com" target="_blank"
              >DrMonikaAgrawal.com</a
            >
            <v-spacer></v-spacer>
            <a href="tel:+91-91670-20945" target="_blank">+91-91670-20945</a>
          </v-row>
        </v-container>
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

<style>
@media print {
  h1 {
    font-size: 20px !important;
  }

  #reset {
    display: none !important;
  }
  th,
  td {
    font-size: 12px !important;
    height: 12px !important;
  }
  @page {
    size: A4; /* DIN A4 standard, Europe */
    margin: 5mm !important;
    padding: 0mm !important;
  }
  html,
  body {
    width: 210mm !important;
    /* height: 297mm; */
    height: 282mm !important;
    background: #fff !important;
    overflow: visible !important;
  }
}
</style>
