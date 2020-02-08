<template>
  <div id="main-app" class="container">
    <div class="row justify-content-center">
      <appointment-list v-bind:appointments="appointments" />
    </div>
  </div>
</template>

<script>
import axios from "axios";
import AppointmentList from "./components/AppointmentList";

export default {
  name: 'MainApp',
  data: function() {
    return {
      title: "Appointment List",
      appointments: []
    }
  },

  components: {
    AppointmentList
  },

  mounted() {
    axios.get("./data/appointments.json")
    .then(response => (this.appointments = response.data));
  }
}
</script>

<style>

</style>
