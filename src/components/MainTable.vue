<template>
  <div>
    <v-container>
      <v-row>
        <v-col cols="6" sm="6" md="4">
          <h1 contenteditable>Patient Name</h1>
        </v-col>
        <v-spacer></v-spacer>
        <v-col cols="6" sm="6" md="4">
          <v-menu
            v-model="menu"
            :close-on-content-click="false"
            :nudge-right="40"
            transition="scale-transition"
            offset-y
            min-width="290px"
          >
            <template v-slot:activator="{ on, attrs }">
              <v-text-field
                v-model="expectedDate"
                label="Expected Date"
                hint="YYYY-MM-DD format"
                persistent-hint
                readonly
                v-bind="attrs"
                v-on="on"
              ></v-text-field>
            </template>
            <v-date-picker v-model="expectedDate" @input="updateDates" @change="updateDates"></v-date-picker>
          </v-menu>
        </v-col>
      </v-row>
    </v-container>

    <v-container>
      <v-data-table
        dense
        :headers="headers"
        :items="schedule"
        hide-default-footer
        disable-pagination
        class="elevation-1"
      >
        <template v-slot:item.dateString="{ item }">
          <div v-if="checkColor(item)">
            <v-chip class="ma-2" color="green" text-color="white">
              {{
              item.dateString
              }}
            </v-chip>
          </div>
          <div v-else>{{ item.dateString }}</div>
        </template>

        <template v-slot:item.milestones="props">
          <v-edit-dialog
            :return-value.sync="props.item.milestones"
            large
            persistent
            @save="save"
            @cancel="cancel"
            @open="open"
            @close="close"
          >
            <div>{{ props.item.milestones }}</div>
            <template v-slot:input>
              <div class="mt-4 title">Update milestones</div>
            </template>
            <template v-slot:input>
              <v-text-field v-model="props.item.milestones" label="Edit" counter autofocus></v-text-field>
            </template>
          </v-edit-dialog>
        </template>
      </v-data-table>

      <p align="right" v-on:click="resetEntries">Reset Milestones</p>
    </v-container>

    <v-snackbar v-model="snack" :timeout="3000" :color="snackColor">
      {{ snackText }}
      <template v-slot:action="{ attrs }">
        <v-btn v-bind="attrs" text @click="snack = false">Close</v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
import defaultSched from "../assets/defaultSchedule.json";

const monthNames = [
  "Jan",
  "Feb",
  "Mar",
  "Apr",
  "May",
  "June",
  "July",
  "Aug",
  "Sept",
  "Oct",
  "Nov",
  "Dec"
];

export default {
  data() {
    return {
      menu: false,
      expectedDate: new Date(Date.now() + 8.64e7).toISOString().substr(0, 10),
      snack: false,
      snackColor: "",
      snackText: "",

      pagination: {},
      headers: [
        {
          text: "Week",
          align: "center",
          value: "week",
          width: "10ch"
        },
        { text: "Trimester", value: "trimester", width: "10ch" },
        { text: "Dates", value: "dateString", width: "30ch" },
        { text: "Milestones", value: "milestones" }
      ],
      schedule: JSON.parse(JSON.stringify(defaultSched))
    };
  },

  mounted() {
    this.$cookies.config(-1);
    this.updateDates();
    //console.log("All cookies", this.$cookies.keys());
    this.getEntries();
  },

  methods: {
    save() {
      this.snack = true;
      this.snackColor = "success";
      this.snackText = "Data saved";
      this.setEntries();
    },
    cancel() {
      this.snack = true;
      this.snackColor = "error";
      this.snackText = "Canceled";
    },
    open() {
      this.snack = true;
      this.snackColor = "info";
      this.snackText = "Dialog opened";
    },
    close() {
      console.log("Dialog closed");
    },
    checkColor(item) {
      if (item.startDate == undefined) {
        return false;
      }

      if (item.endDate == undefined) {
        return false;
      }

      let today = new Date();

      if (
        today.getTime() >= item.startDate.getTime() &&
        today.getTime() <= item.endDate.getTime()
      ) {
        return true;
      }
      return false;
    },
    resetEntries() {
      //console.log(defaultSched);
      this.schedule = JSON.parse(JSON.stringify(defaultSched));
      this.setEntries();
      this.updateDates();

      this.snack = true;
      this.snackColor = "success";
      this.snackText = "Milestones Reset Successful";
    },
    setEntries() {
      var arr = new Array();

      this.schedule.forEach(el => {
        if (el.milestones !== "") {
          let t = {};
          t.week = el.week;
          t.milestones = el.milestones;
          arr.push(t);
        }
      });

      //console.log(arr);
      this.$cookies.set("calcEntries", JSON.stringify(arr));
      console.log("Saved Array");
    },
    getEntries() {
      console.log("Checking for entries");
      if (this.$cookies.isKey("calcEntries")) {
        console.log("Loading saved entries");

        let arrString = this.$cookies.get("calcEntries");

        //  console.log(arrString);

        let arr = JSON.parse(arrString);

        //console.log(arr);

        arr.forEach(el => {
          //console.log("For index", el.week - 1, el.milestones);
          this.schedule[el.week - 1].milestones = el.milestones;
        });
      } else {
        console.log("No entries found");
      }
    },
    updateDates() {
      this.menu = false;

      if (this.expectedDate == null) {
        return null;
      }

      let lastDate = new Date(this.expectedDate);

      console.log(lastDate);

      for (var i = this.schedule.length - 1; i >= 0; i--) {
        let d_end = new Date(lastDate.toString());
        let d_start = new Date(lastDate.toString());
        let diff = (this.schedule.length - i - 1) * 7;

        d_start.setDate(lastDate.getDate() - diff - 6);
        d_end.setDate(lastDate.getDate() - diff);

        let dateString = "";

        if (d_start.getYear() == d_end.getYear()) {
          if (d_start.getMonth() == d_end.getMonth()) {
            dateString =
              monthNames[d_end.getMonth()] +
              " " +
              d_start.getDate() +
              " - " +
              d_end.getDate();
          } else {
            dateString =
              monthNames[d_start.getMonth()] +
              " " +
              d_start.getDate() +
              " - " +
              monthNames[d_end.getMonth()] +
              " " +
              d_end.getDate();
          }
        } else {
          dateString =
            d_start.getFullYear() +
            " " +
            monthNames[d_start.getMonth()] +
            " " +
            d_start.getDate() +
            " - " +
            d_end.getFullYear() +
            " " +
            monthNames[d_end.getMonth()] +
            " " +
            d_end.getDate();
        }

        this.schedule[i].startDate = d_start;
        this.schedule[i].endDate = d_end;
        this.schedule[i].dateString = dateString;
      }
    }
  }
};
</script>

<style>
.class-on-data-table table {
  table-layout: fixed;
}
</style>
