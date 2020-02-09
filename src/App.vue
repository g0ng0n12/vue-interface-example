<template>
  <div id="main-app" class="container">
    <div class="row justify-content-center">
      <add-appointment @add="addItem"/>
      <appointment-list v-bind:appointments="appointments" @remove="removeItem" @edit="editItem"/>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import AppointmentList from "./components/AppointmentList";
import AddAppointment from "./components/AddAppointment";
import _ from "lodash"

export default {
  name: 'MainApp',
  data: function() {
    return {
      title: "Appointment List",
      appointments: []
    }
  },

  components: {
    AppointmentList,
    AddAppointment
  },

  mounted() {
    axios.get("./data/appointments.json")
    .then(response => (this.appointments = response.data.map(item => {
      item.aptId = this.aptIndex;
      this.aptIndex++;
      return item
    })));
  },
  methods: {
    removeItem: function(apt){
      this.appointments = _.without(this.appointments, apt);
    },
    editItem: function(id, field, text){
      const aptIndex = _.findIndex(this.appointments, {
        aptId: id
      });
      this.appointments[aptIndex][field] = text;
    },
    addItem: function(apt){
      apt.aptId = this.aptIndex;
      this.aptIndex++;
      this.appointments.push(apt)
    },
  }
}
</script>

<style>

</style>
